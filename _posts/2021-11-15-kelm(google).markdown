---
title: KELM(Google)
categories: nlp
tags: 
    - nlp
    - knowledge graph
    - knowledge enhancement
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

See [Original Paper](http://arxiv.org/abs/2010.12688) for more details.

In contrast to the many architectures that have been developed to integrate these two sources, our approach converts the KG into natural text, allowing it to be seamlessly integrated into existing language models.

It carries the further advantages of improved factual accuracy and reduced toxicity in the resulting language model. We evaluate this approach by augmenting the retrieval corpus in a retrieval language model and showing significant improvements on the knowledge intensive tasks of open domain QA and the LAMA knowledge probe.

We convert the English Wikidata KG into natural language text. The generated corpus is called KELM Corpus.