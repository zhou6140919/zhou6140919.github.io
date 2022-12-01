---
title: Making Pre-trained Language Models Better Few-shot Learners
tags: [nlp, prompt, papers]
description: todo
---

# Making Pre-trained Language Models Better Few-shot Learners

See [original paper](https://arxiv.org/abs/2012.15723) for more details.

## Introduction

**Motivation**

Inspired by GPT-3, the authors study few-shot learning in a more practical scenario,
where the authors use smaller language models for which fine-tuning is computationally efficient.

**Contributions**

The authors present LM-BFF (better few-shot fine-tuning of language models) a suite of simple
and complementary techniques for finetuning language models on a small number of annotated examples.

The authors' approach includes (1) prompt-based fine-tuning together with a novel pipeline for automating prompt generation;
and (2) a refined strategy for dynamically and selectively incorporating demonstrations into each context.

The authors' experiments demonstrate that our methods combine to dramatically outperform standard fine-tuning
procedures in this low resource setting, achieving up to 30% absolute improvement, and 11% on average across all tasks.
The authors' approach makes minimal assumptions on task resources and domain expertise, and hence constitutes a strong task-agnostic method for few-shot learning.

