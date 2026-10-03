---
layout: post
title: "LLM KV-cache: To Restore or To Recompute, That Is the Question"
date: 2026-09-30
authors: "A. Najafizadeh, V. Tarasov, A. Merenstein, Y. Zhu, E. Zadok — HotStorage '26"
---

## The problem

LLM inference keeps a **KV cache** — the keys and values computed for every previous token — so each decode step doesn't recompute the whole context. The cache lives in GPU **HBM**, which is fast but tiny (an H200 has ~4.8 TB/s of bandwidth but only 141 GB). A 128K-token context on Llama-3.1-70B needs ~30 GB of KV cache, so long contexts spill down the hierarchy: HBM → host DRAM → NVMe/SSD.

When a session resumes and its cache blocks have been offloaded, each block faces a choice: **restore** it over the interconnect, or **recompute** it on the GPU. Static policies (always restore / always recompute) each starve one path.

## The sweet spot

The paper's IO-aware policy computes, per request, how many hit blocks `k` to reassign from restore to recompute:

```
k = N·Y / (X + Y) − M        (clamped to [0, hits])
```

- `N` = total blocks, `M` = miss blocks (must be recomputed anyway)
- `X` = restore rate (blocks/s), `Y` = recompute rate (blocks/s)

**Why this formula:** restore and recompute run *in parallel*, so total time is the max of the two paths. The optimum equalizes them — solving `(N−M−k)/X = (M+k)/Y` gives the formula above. It depends only on the *ratio* X:Y: if recompute is much faster, recompute everything; if restore is much faster, restore everything.

**SLO framing:** a split meets the SLO only if latency ≤ p95 **and** GPU utilization (`r·T_recompute`) ≤ 1 **and** storage utilization (`r·T_restore`) ≤ 1, where `r` is the request rate. If the balanced split still violates the SLO, the policy falls back to a best-effort search over the `k` values where each constraint goes tight.

## Verified with the authors' simulator

The authors open-sourced [KVC-TierSim](https://github.com/sbu-fsl/KVC-TierSim). Running it on the H200 stack (50K blocks, 99% hit ratio, 0.165 req/s, 10s SLO):

| Policy | Time | Meets SLO? |
|---|---|---|
| IO-aware (balanced) | 5.68s | ✅ (both paths at 0.94 util) |
| Restore everything | 49.66s | ❌ (storage 8.2× saturated) |
| Recompute everything | 6.40s | ❌ (GPU at 1.06 util) |

The balanced split — recompute ~44K blocks, restore ~6K — is the *only* one that fits under the SLO, and it's ~9× faster than restore-everything. The decision itself costs ~5µs.

## The honest caveats

- The paper's `X`/`Y` are **static catalog values** (spec-sheet bandwidths, GPU FLOPs × efficiency factor). In production they drift: restore bandwidth collapses under concurrent transfers, GPU throughput varies with batch mix.
- The utilization model is **capacity planning, not scheduling**: it asks "can this hardware sustain this rate?" via Little's-law-style `r·T ≤ 1`, but models no queueing, no per-request contention, no bursty arrivals.
- A production version needs an **online feedback loop** — e.g. an EWMA over observed restore/prefill times — to keep the formula's inputs honest, plus live tier-residency tracking in the cache manager.

## Key concepts

- **HBM (High Bandwidth Memory):** DRAM stacked directly on the GPU package — enormous bandwidth, small capacity. Where inference must run; too small to hold everything.
- **KV cache:** cached key/value tensors per token, letting decode attend to context without recomputation. The thing being tiered.
- **EWMA:** exponentially weighted moving average — `est = α·latest + (1−α)·est`. O(1) running average weighting recent observations more; the standard trick for tracking drifting quantities (TCP uses it for RTT).
