---
layout: single
title: "Critic-Filtered Generative Data Augmentation for Low-Resource Image Classification"
date: 2026-06-12
categories: [generative-ai, research]
tags: [WGAN, data-augmentation, CNN, Chinese-MNIST]
author_profile: true
excerpt: "Filtering a generative model's synthetic images by quality — using the model's own critic — turns sparse-data augmentation from a liability into a statistically significant accuracy gain for an image classifier."
---

Can synthetic images stand in for sparse real training data? This project shows the answer hinges on quality control. Training an image classifier on just 25 real examples per class, I augmented the data with images from a GAN (generative adversarial network) — but first scored each synthetic image with the GAN's own critic, the network it had already trained to tell real from fake, and kept only the cleanest. Filtered this way, the synthetic data delivered a statistically significant accuracy gain of 8.4 percentage points (p = 0.0005), and the gain grew significantly as more filtered samples were added (+2.9 points going from 1:1 to 4:1 augmentation, p = 0.003). The same generator's *unfiltered* output, by contrast, degraded accuracy. The lesson is unambiguous: for synthetic augmentation, quality — not quantity — decides the outcome, and a generator's own discriminator is a free and effective quality filter.

## Overview

This project investigates whether generative data augmentation can improve 
CNN classification accuracy under simulated data scarcity, using Chinese-MNIST 
as a proxy for low-resource domains such as medical imaging.

## Key Finding

The same C-WGAN-GP generator is harmful or strongly beneficial depending on 
whether its output is curated by the trained critic:

| Condition | Accuracy | Δ vs Baseline | p-value |
|---|---|---|---|
| Baseline (50 real/class) | 88.49% ± 0.78% | — | — |
| Unfiltered 4:1 | 86.27% ± 0.25% | −2.22% | 0.008 |
| Baseline (25 real/class) | 71.77% ± 2.57% | — | — |
| Filtered 1:1 | 77.32% ± 1.10% | +5.55% | 0.0085 |
| Filtered 4:1 | 80.19% ± 1.63% | +8.42% | 0.0005 |

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
