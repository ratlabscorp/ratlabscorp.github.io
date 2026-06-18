---
title: "On the Cost of Abstraction in Systems Software"
date: 2026-05-14
draft: false
description: "Every abstraction has a price. In systems code, that price is paid in ways that are often invisible until they aren't."
tags: ["systems", "performance", "Go"]
categories: ["research notes"]
toc: true
---

Every abstraction has a price. In application code, you pay it in complexity and occasionally in speed. In systems code, you pay it in ways that are often invisible until the system is under load, running at 3am, and the on-call engineer is staring at a pprof graph that makes no sense.

This note is about making the invisible visible.

## The Layers We Don't Think About

Consider a simple function call in Go:

```go
func process(items []Item) error {
    for _, item := range items {
        if err := item.Validate(); err != nil {
            return fmt.Errorf("validation failed: %w", err)
        }
    }
    return nil
}
```

This looks cheap. In isolation, it is. But `Validate()` is an interface method call. Interface method calls in Go involve two pointer dereferences: one to the interface's type descriptor, one to the concrete method. Under cache pressure, these become cache misses.

At one thousand items, this is noise. At one million items in a hot loop, it's measurable.

## Measuring What You Can't See

The standard advice is "profile first." This is correct but incomplete. Profiling shows you where time is spent; it doesn't always show you *why*. For cache effects, you need hardware performance counters.

On Linux, `perf stat` gives you this:

```bash
perf stat -e cache-misses,cache-references,instructions ./your-binary
```

A cache miss rate above 5% in a tight loop is worth investigating.

## The Tradeoff Is Always Real

None of this means "don't use interfaces." Interfaces are the right tool in most situations. The point is that the cost is real, it's just deferred — deferred to runtime, deferred to scale, deferred to the conditions that are hardest to reproduce in a development environment.

Understanding the cost doesn't mean paying it. It means choosing when to pay it.

That's what it means to do systems work.
