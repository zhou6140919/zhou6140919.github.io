---
title: Zero- and Few-Shot NLP with Pretrained Language Models
categories: nlp
tags: 
    - nlp
    - zero-shot
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

Zero- and Few-Shot NLP with Pretrained Language Models

<!--more-->

See [original paper](https://aclanthology.org/2022.acl-tutorials.6) for more details.

## Introduction

### Why important

In many situations, labelled data may be costly or otherwise difficult to procure.
Finetuning, the predominant training paradigm, exhibits poor performance in low-data regimes.

### Historical Methods

- data augmentation
- semi-supervised learning
- consistency training
- co-training

### Few-shot learning

Reformulating tasks as complete-the-sentence problems. 
> **No need finetuning**

- GPT-3
- incontext learning
- scoring LM outputs

**Conclusion**
1. In-context learning performance is highly correlated with term frequencies during pretraining.
> Reasoning: 24 appears 100x times more than 23 in data.
    What is 24 times 18 ?

    432 :heavy_check_mark: 

    What is 23 times 18 ?

    462 :heavy_multiplication_x:
2. In-context learning does not necessitate correct input-label mapping.

    GPT-3 (175B) on Classification task

    | Demo | Macro-F1 |
    | --- | --- |
    | No Demos | ~44% |
    | Demo w/ gold labels | ~56% |
    | Demo w/ random labels | ~56% |

    Input and label distributions matter independently.

    a. Removing correct input distribution significantly drops performance.
    b. Removing correct label space significantly drops performance.

### Prompt-based fintuning

LM weights can be updated.

- pattern exploiting training (PET)
- automate the task of prompt-construction (in vocabulary or embedding space)

### Pretraining

- casual vs. masked
- encoder-only vs. decoder-only vs.encoder-decoder
- training data

### Meta-training

Train the LM to adapt to zero- and few-shot use cases.
A variety of work has demonstrated that transfer learning is extremely effective when trained on a diverse set of tasks and prompts.

### Benchmarks

FLEX, BIG-Bench, CrossFit

