---
layout: post
title: "The ACE Center for Evolvable Computing"
date: 2026-10-02
authors: "J. Torrellas (director, UIUC), M. Yu (co-director, Harvard), et al. — IEEE Micro 2026 (SRC JUMP 2.0 special issue)"
---

## What this paper is

Unlike the last two posts, this isn't a single technical result — it's the **vision paper** for a research center. ACE (Advanced Computing for Evolvability… officially the *ACE Center for Evolvable Computing*) is an SRC/DARPA JUMP 2.0 center: ~21 faculty across a dozen universities, 100+ grad students, $31.5M SRC grant ($39.6M total over five years), led by Josep Torrellas (UIUC). Its theme: *systems and architectures for distributed compute*, with the goal of **order-of-magnitude energy-efficiency gains** — because data-center power demand is on a trajectory utilities can't sustain.

## The core idea: evolvability

As computing goes accelerator-centric (accelerators are the most energy-efficient platform, and the consensus is that most compute must move to them), we risk losing the virtues that made general-purpose processors successful: **extensibility, composability, compatible interfaces** — the ability to upgrade, replace, and co-exist across generations.

*Evolvability* means designing accelerators, communication stacks, and security mechanisms so they have compatible interfaces, accommodate upgrades of their environment, and can be replaced by next-generation designs of the same module. Keep what worked about CPUs; don't rebuild a brittle tower of bespoke accelerators.

## The four thrusts (with example projects)

- **Computing engines.** *COCA (Composable Compute Acceleration)*: heterogeneous chiplets (CPU cores, accelerator ASICs, FPGAs) in a multichip module over UCIe, reconfigured offline by chiplet mix and online by reprogramming. *μManycore*: a CPU that drops general-purpose baggage (global cache coherence, long-running prefetchers) to optimize for **tail latency** in microservice/RPC workloads. Plus a unified open-source ACE compiler stack with front ends for LLMs/GNNs.
- **Memory and storage.** (The thrust closest to the [KV-cache]({% post_url 2026-09-30-kv-cache-restore-vs-recompute %}) and [BOOST]({% post_url 2026-10-01-boost-concurrent-hbm-host-memory %}) papers — tiering, placement, and data movement are first-class concerns.)
- **Communication and coordination.** Datacenter hardware sits underutilized — a major energy waste. *Proclets*: computation bundled into small migratable buckets, shipped to where the data is. Reconfigurable optical interconnects and *LIBRA* (topology/bandwidth design-space exploration for distributed AI training). eBPF-customized network stacks for fast *and* secure bypass of the kernel. Compute in SmartNICs/switches (*CC-NIC*), e.g. straggler detection in AI training.
- **Security, privacy, correctness.** Rethinking CPU security tools for accelerator-rich, multi-tenant environments: information-flow control in accelerators, RTL-level vulnerability detection, auto-generated TEEs, and pre-silicon verification (*TEESec*, *Untangle*, *SpecVerilog*, *G-QED*).

## Why it matters to the other papers

ACE is the institutional backdrop for work like BOOST (Kozyrakis is an ACE investigator): the same questions — where should data live, how do heterogeneous tiers cooperate, how do we keep the memory system fed without drowning in data movement — recur at datacenter scale. "Evolvable" is also a useful lens for judging any accelerator proposal: does it compose, or does it lock you in?

## Key concepts

- **JUMP 2.0:** SRC + DARPA's Joint University Microelectronics Program — seven multi-university centers on semiconductor-critical themes, pairing universities with industry.
- **Evolvability:** extensibility + composability + compatible interfaces across generations; the general-purpose virtues to preserve in the accelerator era.
- **Tail latency:** the high-percentile latency (P99), not the mean — what microservices live and die by. (Same reason the BOOST paper reports P99 ITL.)
- **UCIe:** Universal Chiplet Interconnect Express — the open standard letting chiplets from different vendors interoperate in one package.
