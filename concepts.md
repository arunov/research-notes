# Concept glossary

Running definitions across all papers. Each term links back to the paper where it mattered.

- **CAP access** — Concurrent and Proportional: every GPU wave touches both memory tiers concurrently, split by bandwidth ratio. ([BOOST]({% post_url 2026-10-01-boost-concurrent-hbm-host-memory %}))
- **CTA (Cooperative Thread Array)** — NVIDIA's name for a threadblock: a team of warps on one SM cooperating via shared memory. ([BOOST]({% post_url 2026-10-01-boost-concurrent-hbm-host-memory %}))
- **Down-projection** — the MLP matmul contracting intermediate dim back to hidden size. ([BOOST]({% post_url 2026-10-01-boost-concurrent-hbm-host-memory %}))
- **Evolvability** — extensibility, composability, compatible interfaces across accelerator generations. ([ACE]({% post_url 2026-10-02-ace-center-evolvable-computing %}))
- **EWMA** — exponentially weighted moving average; O(1) running average for drifting quantities. ([KV-cache]({% post_url 2026-09-30-kv-cache-restore-vs-recompute %}))
- **Grace Hopper (GH200)** — Grace CPU + Hopper GPU, NVLink-C2C; host memory ≈10% of HBM bandwidth. ([BOOST]({% post_url 2026-10-01-boost-concurrent-hbm-host-memory %}))
- **HBM** — High Bandwidth Memory stacked on the GPU package: huge bandwidth, small capacity. ([KV-cache]({% post_url 2026-09-30-kv-cache-restore-vs-recompute %}))
- **JUMP 2.0** — SRC+DARPA university microelectronics program; seven centers. ([ACE]({% post_url 2026-10-02-ace-center-evolvable-computing %}))
- **KV cache** — cached key/value tensors per token; the thing being tiered. ([KV-cache]({% post_url 2026-09-30-kv-cache-restore-vs-recompute %}))
- **MPP** — modulo-based page placement: every (K+1)th page to the slower tier; deterministic per-wave ratio. ([BOOST]({% post_url 2026-10-01-boost-concurrent-hbm-host-memory %}))
- **P99 ITL** — 99th-percentile inter-token latency; the tail the mean hides. ([BOOST]({% post_url 2026-10-01-boost-concurrent-hbm-host-memory %}))
- **PAM** — page-aligned mapping via LCM-sized superpages for misaligned CTA footprints. ([BOOST]({% post_url 2026-10-01-boost-concurrent-hbm-host-memory %}))
- **Tail latency** — high-percentile latency; what interactive services are judged by. ([ACE]({% post_url 2026-10-02-ace-center-evolvable-computing %}))
- **TPOT** — time per output token; the mean of inter-token latencies. ([BOOST]({% post_url 2026-10-01-boost-concurrent-hbm-host-memory %}))
- **UCIe** — Universal Chiplet Interconnect Express; open chiplet standard. ([ACE]({% post_url 2026-10-02-ace-center-evolvable-computing %}))
- **Warp** — 32 threads executing in lockstep; the hardware scheduling unit. ([BOOST]({% post_url 2026-10-01-boost-concurrent-hbm-host-memory %}))
- **Wave** — all CTAs executing concurrently across the GPU; the timescale of memory load balance. ([BOOST]({% post_url 2026-10-01-boost-concurrent-hbm-host-memory %}))
