---
title: Control Prefix
categories: nlp
tags: 
    - nlp
    - transformer
    - papers
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

This work considers dynamic prompts, and is inspired by how traditional controlled generation methods utilize controllable attributes to generate target sentences with desired qualities.

## Control Prefixes

This work considers seq-to-seq tasks where the objective is to model the conditional probability P(Y|X) with X and Y representing the tokenized input and output sequences respectively.
This paper adopts T5-large and BART-large as the underlying pre-trained language model with parameters φ; and as we consider fixed-LM methods, φ always remains frozen.

# Intuition

We believe having fixed PLM parameters that capture broad natural language understanding, shared task-specific parameters which specify the task itself, and attribute-level parameters which integrate input-level information has a range of benefits.