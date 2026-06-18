---
title: "Reading the Linux Scheduler Source"
date: 2026-03-22
draft: false
description: "Notes from a week spent reading the Linux CFS scheduler implementation. What I expected, what I found, and what still puzzles me."
tags: ["linux", "operating systems", "schedulers"]
categories: ["field notes"]
series: "Reading Linux"
toc: true
---

Last week I spent about five hours reading through the Completely Fair Scheduler (CFS) implementation in the Linux kernel. These are my notes.

## Why the Scheduler

The scheduler is one of those components that every programmer knows exists and almost no programmer understands in detail. It's also responsible for a surprising amount of observable system behavior — latency spikes, CPU throttling under load, the "thundering herd" problem in multi-threaded servers.

I wanted to understand it better. So I read the source.

## The Source Files

The relevant files in the kernel tree are:

```
kernel/sched/core.c       — the main scheduler loop
kernel/sched/fair.c       — CFS implementation (~12,000 lines)
kernel/sched/sched.h      — data structures
include/linux/sched.h     — task_struct definition
```

Start with `sched.h` to understand the data structures. The `task_struct` is enormous — it contains the scheduling entity, memory management info, signals, file descriptors, and dozens of other fields. The scheduling-relevant parts are in the `se` field of type `sched_entity`.

## The Red-Black Tree

CFS maintains a red-black tree of runnable tasks, keyed by their `vruntime` — virtual runtime. The task with the smallest `vruntime` runs next.

```c
struct sched_entity {
    struct load_weight      load;
    struct rb_node          run_node;
    struct list_head        group_node;
    unsigned int            on_rq;
    u64                     exec_start;
    u64                     sum_exec_runtime;
    u64                     vruntime;
    /* ... */
};
```

What surprised me: the scheduler doesn't actually walk the tree on every tick. It caches the leftmost node in `cfs_rq->rb_leftmost`. The tree is only modified on enqueue/dequeue or context switch.

## What Still Puzzles Me

The interaction between CFS and cgroups is significantly more complex than I expected. The "bandwidth throttling" mechanism — where cgroups can have CPU quotas — involves a separate timer mechanism that I haven't fully traced yet.

That's the next week of reading.
