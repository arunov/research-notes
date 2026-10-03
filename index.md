---
layout: page
title: Research Notes
---

Key concepts from computer systems research papers, worked through one paper at a time. Each post distills the paper's core ideas — the mental models worth keeping.

## Papers

- [LLM KV-cache: To Restore or To Recompute, That Is the Question]({% post_url 2026-09-30-kv-cache-restore-vs-recompute %}) — HotStorage '26. When a KV-cache block is evicted from GPU memory, should you restore it from a cheaper tier or recompute it? An IO-aware policy and its closed-form sweet spot.
- [BOOST: Concurrent Access to Host Memory and HBM to Accelerate LLM Inference]({% post_url 2026-10-01-boost-concurrent-hbm-host-memory %}) — arXiv:2609.13592. Making host memory a peer of HBM instead of a spillover tier, via wave-aware page placement. No kernel changes.

## Concept glossary

A running [glossary of terms](concepts.html) across all papers: HBM, CTA, warp, wave, TPOT, P99 ITL, EWMA, and more.
