---
layout: post
title: "The ACE Center for Evolvable Computing"
date: 2026-10-02
paper:
  authors: "Josep Torrellas, Minlan Yu, et al. (21 faculty, 100+ students)"
  venue: "IEEE Micro 2026, special issue on SRC JUMP 2.0 Centers"
  doi: "10.1109/MM.2026.3731147"
---

## The problem

Datacenter power demand is on a trajectory utilities can't sustain, and the authors argue incremental efficiency gains won't bend the curve. ACE — a $31.5M SRC grant ($39.6M total), five-year, 21-faculty center led by Josep Torrellas (UIUC) — bets on order-of-magnitude energy-efficiency gains for distributed computing, from edge nodes to geo-distributed mega-datacenters.

## The core idea: evolvability

Everyone agrees the future is accelerator-centric: specialized hardware does far more work per watt than a CPU on its target workload, because it spends its transistors *computing* instead of managing computation (instruction fetch, branch prediction, speculation). The danger is losing what made general-purpose CPUs successful — extensibility, composability, compatible interfaces across generations. Today's accelerators are bespoke, vertically integrated towers: brilliant at today's workload, obsolete when it shifts.

ACE's thesis: the accelerator era needs CPU-like virtues. Evolvable accelerators, interconnects, and security mechanisms should be upgradeable, replaceable, and able to coexist across generations. The honest tension: specialization *is* the efficiency advantage, so every ounce of generality costs something. The bet is that evolvability can be bought cheaply enough to be worth it.

## The four thrusts

**Computing engines.** *COCA*: heterogeneous chiplets — CPU cores, accelerator ASICs, FPGAs — in one multichip module over the open UCIe interconnect, reconfigured by chiplet mix and reprogramming. The PC-upgrade model at chip scale: swap the accelerator brick without redesigning the system, with chip-scale rather than motherboard-scale data-movement energy. *μManycore*: a CPU that drops global cache coherence and long-running prefetchers to optimize tail latency for microservices — a direct attack on decades of general-purpose CPU orthodoxy. Plus a unified open-source compiler stack with LLM/GNN front ends.

**Memory and storage.** Tiering and data movement as first-class concerns — the thrust closest to our KV-cache and BOOST papers.

**Communication and coordination.** Datacenter hardware sits underutilized, which is pure energy waste. *Proclets*: computation bundled into small migratable buckets, shipped to where the data is. Reconfigurable optical interconnects, *LIBRA* (network topology design-space exploration for AI training), eBPF-customized network stacks, compute in SmartNICs/switches (*CC-NIC*).

**Security, privacy, correctness.** Rethinking CPU security tools for multi-tenant accelerator environments: information-flow control, RTL vulnerability detection, auto-generated TEEs, pre-silicon verification (*TEESec*, *Untangle*, *SpecVerilog*, *G-QED*).

## The through-line

All three papers so far ask one question at different scales: *where should data live, and how do tiers cooperate?* The KV-cache paper: restore or recompute evicted blocks. BOOST: which tier serves each GPU wave. ACE: proclets and tiering across the whole datacenter. Minimizing data movement, as Torrellas puts it, "will be the overriding constraint."

## Why it matters here

Kozyrakis is an ACE investigator — this is the institutional backdrop for BOOST-style work. And the paper's own tension is worth keeping: "incremental progress is not an option," yet the roadmap's pieces look familiar. The novelty bet is on *coordination* — that only a multidisciplinary center can get the combined 10×.
