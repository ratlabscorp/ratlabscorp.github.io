---
title: "Halogen"
date: 2024-06-01
description: "An experimental purely-functional intermediate representation for compilers, designed to make optimization passes composable and verifiable."
status: "Paused"
contributors:
  - "vn"
links:
  - label: "GitHub"
    url: "https://github.com/ratlabscorp/halogen"
  - label: "Paper (draft)"
    url: "https://ratlabscorp.github.io/halogen/paper.pdf"
---

## Overview

Halogen is a research compiler IR inspired by Administrative Normal Form (ANF) and sea-of-nodes representations. The central thesis is that optimization passes are easier to reason about and compose when they are expressed as pure functions over an immutable graph.

## Status

Development is paused while we finish the Ferret project. The theoretical foundations are solid; the implementation is about 40% complete.
