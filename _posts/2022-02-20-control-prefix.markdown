---
title: Control Prefix
categories: nlp
tags: 
    - nlp
    - prompt
    - data-to-text
    - NLG
author: Zhou Tong
sidebar:
  nav: Paper-notes
  avatar: true
article_header:
  type: overlay
  theme: dark
  background_color: '#203028'
  background_image:
    gradient: 'linear-gradient(135deg, rgba(34, 139, 87 , .4), rgba(139, 34, 139, .4))'
    src: /pictures/bg1.jpg
---

Control Prefixes for Text Generation

<!--more-->

## Introduction

### Problem

Larger and larger language models have millions to billions of parameters. This poses a considerable deployment challenge. Moreover, full fine-tuning has been shown to be unnecessarily profligate through overwriting natural language understanding that could otherwise be shared among tasks; it has also been shown that fine-tuned networks do not deviate substantially from the pretrained one in parameter space, implying the existence of parameter efficient alternatives.
Existing controlled generation techniques either aim to generate text with specific target qualities, independent of overall task performance or are methods that have the benefit of updating not only the attributelevel parameters, but adjusting, at the same time, all the LM parameters.

### Solution

We propose the dynamic prompting method CONTROL PREFIXES, which extends prefix-tuning. The prefix-tuning method integrates static task-specific prompts at every layer of a model, adding only 0.1–2% additional parameters to the base LM. With CONTROL PREFIXES we aim to preserve the fixed-LM property, while also allowing datapointspecific attributes to act as guidance signals at the input-level. This is done by employing modular control prefixes, which change alongside the input according to the guidance signal.

## Related Work

**Prompt Learning** [Li and Liang (2021)](https://arxiv.org/abs/2101.00190) discovered that prefix-tuning is more effective than prompt-embedding tuning for text generation. In prefix-tuning, additional trainable key-value pairs, which are fixed across all examples, are used to augment the left context in every attention computation. Therefore, the prompt has constituents at every layer rather than being confined to steer the frozen LM only through the input as in embedding tuning.

**Controlled Generation** [Keskar et al. (2019)](https://arxiv.org/abs/1909.05858) pre-trained a 1.63B parameter model, also alongside conditional control tokens, and demonstrated these learnt to govern style, content, and task-specific behaviour. However, these examples are undesirable in not being fixed-LM techniques--the whole underlying LM can adapt alongside the control tokens. [Nguyen et al., 2016](https://arxiv.org/abs/1612.00005) and [Dathathri et al., 2020](https://arxiv.org/abs/1912.02164) used plug-and-play perturbations of the LM hidden states towards a target attribute. These methods are fixed-LM and are able to control target qualities such as sentiment and topic. However, they are slow at inference time due to requiring multiple passes for a single batch. The shift in conditional probability can also lead to text degeneration.

**Dynamic Prompts** In [Yu et al. (2021)](https://arxiv.org/abs/2103.11070)'s work, they use an attribute alignment function to form dynamic prompts and the prompt does not have a static component and aims to generate text with specific target attributes, independent of task performance. With CONTROL PREFIXES, the intention is to also maximize task-specific performance, which is why we maintain a large static prompt component to specify the task itself.

**Auxiliary scaffold tasks** Incorporating auxiliary scaffold tasks via multitask learning has been previously used for improving span-labeling and text classification (Swayamdipta et al., 2018; Cohan et al., 2019). Cachola et al. (2020) demonstrate that control tokens can be used to effectively incorporate scaffold tasks alongside the main task for BART LARGE .


## Control Prefixes

This work considers seq-to-seq tasks where the objective is to model the conditional probability P(Y|X) with X and Y representing the tokenized input and output sequences respectively.
This paper adopts T5-large and BART-large as the underlying pre-trained language model with parameters φ; and as we consider fixed-LM methods, φ always remains frozen.

# Intuition

We believe having fixed PLM parameters that capture broad natural language understanding, shared task-specific parameters which specify the task itself, and attribute-level parameters which integrate input-level information has a range of benefits.