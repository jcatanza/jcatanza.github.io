---
layout: single
title: "Generative Augmentation Recovers Nearly Half the Accuracy Lost to Data Scarcity"
date: 2026-06-12
categories: [generative-ai, research]
tags: [WGAN, data-augmentation, CNN, Chinese-MNIST]
author_profile: true
excerpt: "When labeled data is scarce, GAN augmentation recovers nearly half the accuracy lost to the shortage (+7 points, p < 0.01) — and a controlled factorial shows you don't need critic-based quality filtering to get it: plain augmentation works just as well."
---

When you're short on labeled data, can a GAN trained on that same small dataset help? Here, substantially — yes. Training an image classifier on Chinese-MNIST with only 25 real images per class, GAN augmentation lifts test accuracy by up to 7.2 points (p < 0.01), buying back about 43% of the accuracy lost when the real training set is halved — without a single new labeled example. That's the practical headline: under genuine scarcity, generative augmentation is a real, reliable lever. A controlled 2 × 2 × 3 factorial (real-count × filtered/unfiltered × ratio, drawn from one critic-scored pool) then adds two refinements. The gain is regime-dependent — augmentation helps a data-starved model but hurts a near-saturated one (50 real/class) — so apply it where data is thin. And critic filtering, which I'd hypothesized was the key, turns out to be unnecessary: filtered and unfiltered perform the same once sample count is fixed. Good news — the simple version captures the full benefit.

## Overview

This project investigates whether generative data augmentation can improve 
CNN classification accuracy under simulated data scarcity, using Chinese-MNIST 
as a proxy for low-resource domains such as medical imaging.

## Key Finding

**Under data scarcity, generative augmentation recovers nearly half the accuracy lost to the shortage.** Halving the real training set from 50 to 25 images per class costs 16.7 points (88.49% → 71.77%). A GAN trained on that same scarce data buys back 7.2 of them — about 43% — with no new labels (25/class, 4:1: **79.01%, +7.24, p = 0.004**).

| Real/class | Condition | Accuracy | Δ vs baseline | p |
|---|---|---|---|---|
| **25** | Baseline | 71.77% | — | — |
| | Unfiltered 1:1 | 77.52% | +5.75 | 0.005 |
| | Unfiltered 4:1 | 78.79% | +7.01 | 0.003 |
| | Filtered 1:1 | 77.88% | +6.11 | 0.007 |
| | Filtered 4:1 | 79.01% | +7.24 | 0.004 |
| **50** | Baseline | 88.49% | — | — |
| | Unfiltered 1:1 | 87.05% | −1.44 | 0.049 |
| | Unfiltered 4:1 | 86.35% | −2.15 | 0.009 |
| | Filtered 1:1 | 87.60% | −0.89 | 0.222 |
| | Filtered 4:1 | 86.48% | −2.01 | 0.001 |

**The gain is regime-dependent.** At 25 real/class every condition improves accuracy significantly (+5.8 to +7.2). At 50 real/class the classifier is near-saturated and the same synthetic data *degrades* it (down to −2.2). Use augmentation where data is thin; skip it when you already have enough.

**You don't need quality filtering.** Holding real-count *and* ratio fixed (filtered − unfiltered): +0.55 (p=0.034), +0.13 (p=0.78), +0.36 (p=0.55), +0.23 (p=0.64). All ≤ 0.55 points; three of four non-significant, the fourth failing multiple-comparison correction. Filtered and unfiltered are statistically indistinguishable — the simple approach captures the full benefit.

**Conclusion.** In low-resource classification, GAN augmentation is a sizable, reliable win, driven by added data volume in the scarce regime; critic-based filtering adds nothing measurable. (n = 5 on one stratified split, so the filtering result is "no *detectable* effect," not "none possible." A diagonal design pairing 50/class with unfiltered and 25/class with filtered would have misread the scarcity gain as a filtering effect; the factorial corrects it.)

## Method

- **Generator:** Conditional WGAN-GP (C-WGAN-GP), 500 epochs
- **Quality filter:** Score 400 candidates/class with the trained critic, 
  retain top 25%
- **Classifier:** 3-block CNN, 5 independent random seeds
- **Statistical test:** Paired t-test, α = 0.05

## Practical Recipe

> Train the pair, keep the critic, score the candidates, train on the best.

## Links

- [Research Paper (PDF)](/assets/papers/Catanzarite_ResearchProject_FinalPaper_v12.pdf)
- [Jupyter Notebook](/assets/notebooks/Catanzarite_ResearchProject_rev9_FINAL.html)
- **Course:** EN.705.603 Introduction to Generative AI, JHU Spring 2026
