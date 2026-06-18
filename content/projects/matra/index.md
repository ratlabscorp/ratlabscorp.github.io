---
title: "Matra"
date: 2026-01-15
description: "A tokenizer built for multilingual text. Outperforms every major commercial tokenizer across 23 languages."
status: "Released"
project_tags:
  - "nlp"
  - "tokenization"
  - "multilingual"
  - "systems"
contributors:
  - "mx"
  - "tz"
  - "vn"
links:
  - label: "GitHub"
    url: "https://github.com/ratlabscorp/matra"
  - label: "Hugging Face"
    url: "https://huggingface.co/ratlabscorp/matra"
  - label: "Paper"
    url: "matra-paper.pdf"
---

![Matra benchmark chart](cover.png)

> Technical report: [matra-paper.pdf](matra-paper.pdf)

## The Question

What does a tokenizer look like when it's designed for a language family rather than English?

Most tokenizers treat Indian languages as an afterthought. A single syllable in Hindi or Tamil gets decomposed into individual bytes or Unicode fragments. The result is predictable: more tokens, worse compression, and representations that don't actually represent anything meaningful.

We built Matra to find out if this was fixable.

## What We Built

Matra is a BPE tokenizer trained specifically for multilingual text. It uses script-aware frequency scaling, a two-stage merge strategy, and a few tricks we didn't expect to matter as much as they did.

Key architectural choices:

- **Unicode-aware pre-tokenization.** Diacritics stay attached to base letters. CJK characters split individually. Sentence delimiters are preserved as explicit tokens.
- **Language-weighted training.** Target-script words receive a frequency boost during Stage 1. A sentence with 80% Hindi and 20% English gets 80% of the full weight. This prevents code-switched text from distorting the merge budget.
- **Sentence boundary detection.** Any merge containing a sentence delimiter is permanently banned. This prevents tokens like "delhi. the" from entering the vocabulary.
- **Two-pass streaming.** Training splits into word-level and sentence-level passes. Peak memory drops from ~75 GB to ~5 GB. It now trains comfortably on a 16 GB machine.
- **RAM Shield.** Stage 2 hard-caps memory at ~2.5 GB by purging singleton pairs and rebuilding the heap. The math checks out: a pair with frequency ≤ 1 can never be selected for merging, so dropping them is lossless.

## The Evidence

Benchmark date: 15 June 2026. Test split: IN22-Gen, approximately 200,000 tokens per language.

| Tokenizer | SeqRed | NSL | BPT | Fert | 1ch% | Total Tokens |
|---|---|---|---|---|---|---|
| **Matra** | **70.3%** | **0.1225** | **8.90** | **2.06** | **7.5** | **1,103,089** |
| Sutra-v2 | 64.6% | 0.1453 | 7.29 | 2.47 | 25.1 | 1,311,098 |
| Sarvam-105B | 64.9% | 0.1454 | 7.35 | 2.45 | 29.2 | 1,301,947 |
| Gemini 3.5 Flash | 51.5% | 0.1959 | 6.28 | 3.34 | 0.0 | 1,794,568 |
| Gemma-4-31B | 51.5% | 0.1959 | 6.28 | 3.34 | 39.3 | 1,794,545 |
| GPT-5 | 37.4% | 0.2494 | 5.58 | 4.27 | 44.7 | 2,327,945 |
| MUTANT (Krutrim-2) | 23.4% | 0.3025 | 4.56 | 5.29 | 55.2 | 2,864,195 |
| Qwen-3.6-MoE | 13.2% | 0.3418 | 3.35 | 6.07 | 77.6 | 3,225,298 |

Matra encodes the full benchmark corpus in **42% fewer tokens than Gemma-4-31B** and **66% fewer than Qwen-3.6-MoE**.

On Manipuri (Meitei script), where GPT-5, Qwen, and MUTANT all collapse to negative sequence reduction with fertility above 16, Matra achieves 53.6% sequence reduction with fertility 3.05. That wasn't in the design doc. It just fell out of the architecture.

## What Surprised Us

The 2-pass streaming architecture wasn't in the original plan. We added it after the first training run OOM-killed a 64 GB machine. Turns out reality had other plans.

The RAM Shield was added after the second run. We thought Stage 2 would be memory-light. It wasn't.

The O(1) encode path was an optimization we expected to be minor. At inference time it eliminated the largest bottleneck we had. Some of the best decisions are the ones you make because you're tired of waiting.

## Where This Breaks

Matra is optimized for Indic scripts and English. We have not benchmarked it on Semitic languages, most Dravidian scripts outside the supported set, or indigenous languages with small corpora. It might work. It might not. We don't know yet.

The 200k vocabulary is large. A 128k variant exists but compression ratios are 6-8% lower on average. Whether that tradeoff matters depends on your constraints.

LLM integration is pending. Matra produces fewer tokens, but we have not yet trained a model from scratch to verify that fewer tokens actually translate to better downstream performance. That experiment is next.

## Status

| | |
|---|---|
| **State** | Stable |
| **Confidence** | High on benchmarks, medium on downstream tasks |
| **Last updated** | June 2026 |
| **Known limitations** | Unbenchmarked on non-Indic language families |
| **Open question** | Does fewer tokens actually mean better model performance? |
| **Next experiment** | Train a small LLM with Matra and measure downstream accuracy |

**What tool do you wish existed?**
