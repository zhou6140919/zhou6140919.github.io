---
title: Stage-wise Fine-tuning for Graph-to-Text Generation
categories: nlp
tags: 
    - nlp
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

Stage-wise Fine-tuning for Graph-to-Text Generation

<!--more-->

## Abstract

### Problem

Graph-to-text generation using PLMs achieves better performance than structured graph encoders. However, they fail to fully utilize the structure information of the input graph.

PLMs fine-tuned only on the clean data might be more prone to hallucinate factual knowledge.

### Solution

We propose a structured graph-to-text model with a two-step fine-tuning mechanism which first fine-tunes the model on Wikipedia before adapting to the graph-to-text generation. In addition to using the traditional token and position embeddings to encode the KG, we propose a novel tree-level embedding method to capture the interdependency structures of the input graph. This new approach has significantly improved the performance of all text generation metrics for the English WebNLG 2017 dataset.

We first fine-tune our model over noisy RDF graphs and related article pairs crawled from Wikipedia before final fine-tuning on the clean/labeled training set. The additional fine-tuning step benefits our model by leveraging triples not included in the training set and reducing the chances that the model fabricates facts based on the language model. We extend the embedding layer of Transformer-based PLMs with two addtional *triple role* and *tree-level* embeddings to capture graph structure.

## Method

Goal: To generate a text faithfully describing the input graph.

We augment our model with additional position embeddings to capture the structure of the KG.

Input: "|S" "|P" "|O" as special tokens to indicate subjects, relations, objects, respectively.

**Positional Embeddings** We extend the input layer with two position-aware embeddings in addition to the original position embeddings.

![additional-position-embedding](/images/additional-position-embedding.jpg)

Triple Role ID: takes 3 values for a specific triple: 1 for the subject, 2 for the relation, 3 for the object.

Tree level ID: calculates the distance (the number of relations) from the source vertex(顶点) of the RDF graph.

**Two-step Fine-tuning**