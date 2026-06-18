---
title: "Ferret"
date: 2025-11-01
description: "A minimal, dependency-free tracing runtime for concurrent Go programs. Designed for production debugging without instrumentation overhead."
status: "Active"
project_tags:
  - "systems"
  - "go"
  - "observability"
contributors:
  - "mx"
  - "tz"
links:
  - label: "GitHub"
    url: "https://github.com/ratlabscorp/ferret"
  - label: "Docs"
    url: "https://ratlabscorp.github.io/ferret"
---

## Overview

Ferret is a lightweight tracing library for Go. Unlike OpenTelemetry and similar frameworks, Ferret makes no assumptions about your observability stack and adds zero dependencies to your binary.

It instruments goroutine scheduling, channel operations, and mutex contention at nanosecond resolution, emitting structured traces to a configurable sink.

## Design Goals

- **Zero allocation on the hot path.** Ferret uses a fixed-size ring buffer per goroutine to avoid heap pressure.
- **No external dependencies.** The core library is pure Go stdlib.
- **Deterministic replay.** Recorded traces can be replayed in a controlled environment for debugging.

## Status

Active development. The core tracing primitives are stable. The replay engine is experimental.
