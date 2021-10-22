---
title: Attention is All You Need
tags: TeXt
sidebar:
  nav: Paper-notes
article_header:
  type: cover
  image:
    src: /screenshot.jpg
---

###### `Attention is All You Need`{:.success}
See [Paper](https://arxiv.org/abs/1706.03762) for more details.
See [Transformer Codes](http://nlp.seas.harvard.edu/2018/04/03/attention.html) for more details.

<!--more-->

## Notes on Attention is All You Need

`Problems:`{:.warning}
Sequence models are based on complex recurrent or convolutional neural networks. RNN aligns the positions to steps in computation time. The inherently(内在地) sequential nature precludes(杜绝) parallelization within training examples, which becomes critical at longer sequence lengths, as memory constraints limit batching across examples.
{:.warning}

`Solution:`{:.success} 
The Transformer, based solely on attention mechanisms, dispensing with(摒弃) recurrence and convolutions entirely.
In this work we propose the Transformer, a model architecture eschewing recurrence and instead relying entirely on an attention mechanism to draw global dependencies between input and output. The Transformer allows for significantly more parallelization and can reach a new state of the art in translation quality after being trained for as little as twelve hours on eight P100 GPUs.
{:.success}

`Method:`{:.info}

`Result:`{:.error}
Experiments on two machine translation tasks show these models to be superior in quality while being more parallelizable and requiring significantly less time to train. Reached SOTA. 28.4 BLEU on the WMT 2014 en-to-german translation task and 41.8 on the WMT en-to-french translation task.
By parsing both large and limited training data, the Transformer generalizes well to other tasks in English.
{:.error}

