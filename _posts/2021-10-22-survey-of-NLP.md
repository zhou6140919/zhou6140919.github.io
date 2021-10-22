---
title: Pre-trained Models for Natural Language Processing A Survey
tags: 
    - nlp
    - survey
    - PTM
    - distributed representation
author: Zhou Tong
sidebar:
  nav: Paper-notes
  avatar: true
  bio: "My experience sharing"
article_header:
  type: overlay
  theme: dark
  background_color: '#203028'
  background_image:
    gradient: 'linear-gradient(135deg, rgba(34, 139, 87 , .4), rgba(139, 34, 139, .4))'
    src: /screenshot.jpg
---
# Notes

<!--more-->

See [Paper](https://arxiv.org/abs/2003.08271) for more details.


## Summary

The emergence of pre-trained models(PTMs) has brought natural language processing to a new ear.
This paper provides a comprehensive review of PTMs for NLP.
- Language representation learning
- Categorize existing PTMs in four perspectives
- How to adapt knowledge of PTMs to downstream tasks
- Potential directions

## Introduction

- convolutional neural networks
- recurrent neural networks
- graph-based neural networks
- attention mechanism

These neural models alleviate(减轻) the *feature engineering* problem. Non-neural NLP methods rely on the discrete handcrafted features, while neural methods use low-dimensional and dense vectors(distributed representation) to implicitly(含蓄地) represent the syntactic(句法的) or semantic(语义的) features of the language.

Therefore, neural methods make it easy for people to dvelop various NLP systems.
{:.success}

PTMs can learn universal language representations on large corpus, avoiding training a new model from scratch.

*first generation PTMs* aim to learn good word embeddings.

These models are no longer needed since they are usually very shallow for computational efficiencies.
- Skip-Gram
- GloVe

They are context-free and fail to capture higher-level concepts in context, such as polysemous disambiguation(多义词消歧), syntactic structures, semantic roles, anaphora(指代).

*second generation PTMs* focuson learning contextual word embeddings.
- CoVe
- ELMo
- OpenAI GPT
- BERT

These learned encoders are still needed to represent words in context by downstream tasks.

------------------

## Background

### Language Representation Learning

Speaking of language, a good representation should capture the implicit linguistic rules and common sense knowledge hiding in text data.

Distributed representation: describe the meaning of a piece of text by low-dimensional real-valued vectors. Each dimension has no corresponding sense.
{:.success}

The embedding changes according to the context it appears in.

#### Non-contextual Embeddings

a look up table $$E \in \mathbb{R}^{D_v \cdot V}$$



