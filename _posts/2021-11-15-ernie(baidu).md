---
title: ERNIE(Baidu)
categories: nlp
tags: 
    - nlp
    - transformer
    - bert
    - knowledge graph
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

<!--more-->

ERNIE: Enhanced Representation through Knowledge Integration

See [original paper](http://arxiv.org/abs/1904.09223) for more details.

ERNIE is designed to learn lauguage representations enhanced by knowledge masking strategies, which includes entity-level masking and phrase-level masking.

- Entity-level strategy masks entities which are usually composed of multiple words.
- Phrase-level strategy masks the whole phrase which is composed of several words standing together as a conceptual unit.

ERNIE has more powerful knowledge inference capacity on a cloze test.

## Introduction

**Problem** The majority of the pre-trained models model the representations by predicting the missing word only through the contexts. These works do not consider the prior knowledge in the sentence.
It is easy to predict the missing word of the entity by word collocations(搭配) inside this entity without the help of long contexts.

**Solution** We take a phrase or an entity as one unit, which is usually composed of several words. All of the words in the same unit are masked during word representation training, instead of only one word or character being masked.
Instead of adding the knowledge embedding directly, ERNIE implicitly learned the information about knowledge and longer semantic dependency to guide word embedding learning. This can make the model have better generalization and adaptability.

## Methods

### Transformer Encoder

For Chinese corpus, we add spaces around every character in the CJK Unicode range and use the WordPiece to tokenize Chinese sentences. 
Same input as BERT.

### Knowledge Integration

- basic level masking: mask 15% of the Chinese characters. For English, 15% words are masked.
- phrase level masking: For English, use lexical analysis and chunking tools to get the boundary of phrases.
- entity level masking

## Experiments

Datasets: Chinese Wikipedia, Baidu Baike, Baidu News, Baidu Tieba.
Pre-processing: Trandition-to-simplified conversion, lowercasing.
Vocabulary: 17,964 unicode characters.

### DLM Task

The DLM task helps ERNIE to learn the implicit relationship in dialogues, which also enhances the model's ability to learn semantic representation. The model architecture of DLM task is compatible with that of the MLM task, thus it is pre-trained alternatively with the MLM task.

### Downstream Tasks

#### Natural Language Inference

The Cross-lingual Natural Language Inference (XNLI) corpus is a crowdsourced collection for the MultiNLI corpus.

#### Semantic Similarity

The Large-scale Chinese Question Matching Corpus (LCQMC) aims at identifying whether two sentences have the same intention.

#### Name Entity Recognition

The MSRA-NER dataset is designed for named entity recognition, which is published by Microsoft Research Asia.

#### Sentiment Analysis

ChnSentiCorp is a dataset which aims at judging the sentiment of a sentence.

#### Retrieval Qustion Answering

The goal of NLPCC-DBQA dataset is to select answers of the corresponding questions.

### Effects

#### Entity-level masking

Using entity-level masking strategy, the performance of the model is further improved. The result also shows that with 10 times larger size of the pre-training dataset, 0.8% performance gain is achieved on XNLI test set.

#### DLM Task

We can see that 0.7%/1.0% of improvement in develop/test accuracy is achieved on this DLM task.

