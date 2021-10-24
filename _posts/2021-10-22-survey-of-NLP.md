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

<u>***updating***</u>

## Summary

The emergence of pre-trained models(PTMs) has brought natural language processing to a new era.
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

These neural models alleviate(减轻) the ***feature engineering*** problem. Non-neural NLP methods rely on the discrete handcrafted features, while neural methods use low-dimensional and dense vectors(distributed representation) to implicitly(含蓄地) represent the syntactic(句法的) or semantic(语义的) features of the language.

Therefore, neural methods make it easy for people to dvelop various NLP systems.
{:.success}

PTMs can learn universal language representations on large corpus, avoiding training a new model from scratch.

***first generation PTMs*** aim to learn good word embeddings.

These models are no longer needed since they are usually very shallow for computational efficiencies.
- Skip-Gram
- GloVe

They are context-free and fail to capture higher-level concepts in context, such as polysemous disambiguation(多义词消歧), syntactic structures, semantic roles, anaphora(指代).

***second generation PTMs*** focus on learning contextual word embeddings.
- [CoVe][1]
- [ELMo][2]
- [OpenAI GPT][3]
- [BERT][4]

These learned encoders are still needed to represent words in context by downstream tasks.

------------------

## Background

### Language Representation Learning

Speaking of language, a good representation should capture the implicit linguistic rules and common sense knowledge hiding in text data.

Distributed representation: describe the meaning of a piece of text by low-dimensional real-valued vectors. Each dimension has no corresponding sense.
{:.success}

The embedding changes according to the context it appears in.

#### Non-contextual Embeddings

a look up table $$E \in \mathbb{R}^{D_v \times \mathcal{V}}$$

There are two limitations.
- static embeddings. Fail to model polysemous words.
- out-of-vocabulary problem

#### Contextual Embeddings

The contextual representation depends on the whole text.

$$ [h_1,h_2,...,h_T] = f_{enc}(x_1,x_2,...,x_T) $$

where $$f_{enc}$$ is neural encoder.

### Neural Contextual Encoders

- sequence models
    - convolutional models: capture the meaning of a word by aggregating the local information from its neighbors.
    - recurrent models: capture the contextual representations of words with short memory, such as [LSTMs][5] and [GRUs][6]. Bidirectional operations are used to collect information from both sides of a word, but its performance is often affected by the `long-term dependency problem`{:.warning}.
- non-sequence models: learn with a pre-defined tree or graph structure between words. `How to build a good graph structure?`{:.warning} `The structure heavily depends on expert knowledge or external NLP tools.`{:.warning}
    - fully-connected self-attention model: [Transformer][7]. Also need positional embeddings, layer normalization, residual connections, and position-wise feed-forward network layers, etc. `Easy to overfit on small or modestly-sized datasets.`{:.warning}

The Transformer has become the mainstream architecture of PTMs due to its powerful capacity.
{:.info}


### Why Pre-training?

The number of model parameters :arrow_up: , 
much larger dataset is needed to prevent overfitting during training parameters. And it is extremely expensive to build large-scale labeled NLP dataset.

Models can first learn a good representation from the huge unlabeled corpus and then use them for other tasks.

**Advantages:**
1. Learn universal language representations that can help with downstream tasks.
{:.info}
2. Provide a better model initialization, lead to a better generalization performance and speed up convergence(收敛) on the target task.
{:.info}
3. Pre-training can be regarded as a kind of regularization to avoid overfitting on small datasets.
{:.info}

### A Brief History

1. pre-trained word embeddings: Continuous Bags-of-Words(CBOW) and Skip-Gram
  - They are simple but still can learn high quality word embeddings to capture the latent syntactic and semantic similarities among words.
  - word2vec, GloVe
  - **Context Independent** and mostly trained by shallow models, the rest of the model still needs to be learned from scratch.
2. pre-trained contextual encoders
  - LSTM, Seq2Seq
  - Transformer
  - ULMFiT, BERT (fine-tuning)

Fine-tuning has become the mainstream approach to adapt PTMs for downstream tasks.

## Overview of PTMs

### Pre-training Tasks

challenging & substantial(大量) training data

- supervised learning: to learn a function that maps input to output
- unsupervised learning: to find intrinsic knowledge from unlabeled data (clusters, densities, latent representations)
- self-supervised learning: a blend(混合) of the above two (MLM: Masked Language Model)
  1. Language Modeling (LM)
    - A classic probabilistic density estimation problem.

      $$ p(X_{1:T}) = \prod_{t=1}^{T} p(x_t | X_{0:t-1}) $$

      where $$x_0$$ is the beginning token of the sequence.
      Train with maximum likelihood estimation (MLE).

      *problem: It can only encode the leftward context tokens and itself.* -> bidirectional LM (BiLM)

  2. Masked Language Modeling (MLM)
    - The mask \[MASK] does not appear in the fine-tuning phase.
      *problem: This will create a mismatch between the pre-training phase and the fine-tuning phase.
  3. Permuted Language Modeling (PLM)
  4. Denoising Autoencoder (DAE)
  5. Contrastive Learning (CTL)
  6. Others

This is the graph of loss functions of all these tasks.
![graph](/images/loss_function.jpg)




[1]: https://arxiv.org/abs/1708.00107
[2]: https://arxiv.org/abs/1802.05365
[3]: https://s3-us-west-2.amazonaws.com/openai-assets/research-covers/language-unsupervised/language_understanding_paper.pdf
[4]: https://arxiv.org/abs/1810.04805
[5]: https://arxiv.org/abs/2105.06756
[6]: https://arxiv.org/abs/1412.3555
[7]: https://arxiv.org/abs/1706.03762