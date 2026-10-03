---
layout: post
title: "BOOST: Concurrent Access to Host Memory and HBM to Accelerate LLM Inference"
date: 2026-10-01
authors: "A. Saxena, J.H. Ju, H. Taneja, P.A. Tsai, A. Jaleel, C. Kozyrakis, M. Qureshi — arXiv:2609.13592"
---

## The problem

A GPU's memory is really two tiers: **HBM** plus **host memory** reachable over the CPU-to-GPU interconnect. On Grace Hopper, host read bandwidth is ~450 GB/s — roughly **10% of HBM**. Today's serving systems treat the tiers hierarchically: serve from HBM when data fits, otherwise *prefetch* from host to HBM before use.

The catch the paper quantifies: prefetching consumes HBM **write** bandwidth, stealing from demand **read** bandwidth. Even a perfect prefetcher delivers *less* read bandwidth than HBM alone. Host bandwidth is never actually used for serving.

## The key idea: CAP access

To extract the combined bandwidth, every **wave** of GPU threadblocks must access both tiers **concurrently** and **proportionally** to the bandwidth ratio (α ≈ 0.10 → ~10× fewer host accesses). The ratio is chosen so both sides finish together: 1/11th of the bytes at 1/10th the bandwidth takes the same time as 10/11ths at full bandwidth. (Same equalizing principle as the [KV-cache paper's]({% post_url 2026-09-30-kv-cache-restore-vs-recompute %}) sweet-spot formula — two paths, one bottleneck.)

**The subtle failure it fixes:** decade-old proportional-placement schemes randomly assigned 4KB pages across tiers. That worked because a wave touched thousands of pages and the law of large numbers held. Modern GPUs use **2MB pages**, so a wave touches only tens — per-wave the ratio varies wildly, either wasting host bandwidth or stalling on it while HBM idles. Measured: random placement *degrades* TPOT by 0.4–2.7%.

## How BOOST does it (no kernel changes, integrated into vLLM)

- **Modulo-based page placement (MPP)** for static weights: with HBM:host ratio K, the first K pages go to HBM, the (K+1)th to host. Deterministic per-wave ratio — a wave sweeping contiguous memory always inherits the right split. Zero variance, unlike random placement.
- **CTA-level wave-awareness:** warp-level assignment is too coarse (4-warp CTAs only express ratios in multiples of 0.25); thread-level breaks memory coalescing. The CTA is the right granularity.
- **PAM superpages** for misaligned footprints: when a CTA's footprint doesn't align to pages (Llama's down-projection: 3.5MB footprint vs 2MB pages), a straddling CTA stalls on its slowest byte. Group pages into superpages of LCM(footprint, page size) = 14MB, place each wholly in one tier, apply MPP across superpages.
- **Dynamic KV cache:** alloc/free churn re-randomizes the address→tier mapping and attention kernels re-chunk KV→CTA mappings as contexts grow, so static placement can't work. Fix: a **wave-aware free-page pool** (tier assigned by head/request identity, not address luck) plus **tensor layout reordering** (group heads into placeable units). Approximate, but beats random.

## Results (vLLM on Grace Hopper, Llama-3.3-70B / Qwen3-Next-80B / Qwen2.5-72B)

- Iso-batch: **+4.3% TPOT** over HBM-only; prefetching **−6%**.
- High-throughput: **+31% average** — mostly from added host capacity, plus 4% purely from concurrent access that no capacity-only scheme provides; 15% better than prefetching.

## Key concepts

- **Thread / warp / CTA / wave:** thread = one worker; warp = 32 threads in lockstep; CTA (threadblock) = team of warps on one SM, cooperating via shared memory; wave = all CTAs executing concurrently across the GPU (a few hundred). The wave is the timescale at which memory load balance matters.
- **TPOT vs P99 ITL:** TPOT = mean inter-token latency (total decode time ÷ tokens). P99 ITL = 99th percentile — captures the tail stalls the mean hides. Five tokens with gaps [1,1,1,1,8]s → TPOT 2.4s looks fine, P99 ITL 8s reveals the freeze.
- **Grace Hopper (GH200):** Grace CPU + Hopper GPU in one package, NVLink-C2C interconnect — cache-coherent host memory at ~10% of HBM bandwidth. What makes the host tier a real bandwidth peer instead of a slow spillover.
- **Down-projection:** the second matmul in a transformer MLP block, contracting the intermediate dimension back to hidden size (e.g. 28,672 → 8,192). Its awkward 3.5MB CTA footprint motivated PAM.
