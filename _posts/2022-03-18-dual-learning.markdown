---
title: Dual Learning for Machine Translation
categories: nlp
tags: 
    - nlp
    - machine translation
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

Dual Learning for Machine Translation

<!--more-->

See [original paper](http://arxiv.org/abs/1611.00179) for more details.

## Abstract

Problem: Tens of millions of bilingual sentence pairs are needed for training neural machine translation models. However, such parallel data are costly to collect in practice and thus are usually limited in scale, which may constrain the related research and applications.

Solution: We develop a dual-learning mechanism, which can enable an NMT system to automatically learn from unlabeled data through a dual-learning game.

Explanation: Any machine translation task has a dual task; the primal and dual tasks can form a closed loop, and generate informative feedback signals to train the translation models, even if without the involvement of a human labeler. In the dual-learning mechanism, we use two agents to represent the model of the primal task and the dual task respectively, then ask them to teach each other through a reinforcement learning process. Based on the feedback signals (the language model likelihood of the output and the reconstruction error of the original sentence after the primal and dual translations), we can iteratively update the two models until convergence. We call this approach dual-NMT.

Conclusion: Experiments show that dual-NMT works very well on English↔French translation; especially, by learning from monolingual data (with 10% bilingual data for warm start), it achieves a comparable accuracy to NMT trained from the full bilingual data for the French-to-English translation task.

## Related Works

1. monolingual corpora inthe target language are used to train a language model, which is then integrated with the MT models trained from parallel bilingual corpora to improve the translation quality.
> Do not fundamentally address the shortage of parallel training data.

2. pseudo(假的) bilingual sentence pairs are generated from monolingual data by using the translation model trained from aligned parallel corpora, and then these pseudo bilingual sentence pairs are used to enlarge the training data for subsequent learning.
> There is no guarantee on the quality of the pseudo bilingual sentence pairs.

By using dual-learning mechanism, the monolingual data can play a similar role to the parallel bilingual data, and significantly reduce the requirement on parallel bilingual data during the training process.
Specifically, this mechanism can be described as the following two-agent communication game.

1. The first agent, who only understands one language A, sends a message in A to the second agent through a noisy channel, which converts the message from A to language B using a translation model.
2. The second agent, who only understand language B, receives the translated message in B. She (how the gender is decided?? XD) checks the message and notifies the first agent *whether it is a natural sentence in B* (the second agent may not be able to verify the correctness of the translation). Then she sends the received message back to the first agent through another noisy channel, which converts the received message from B back to A using another translation model.
3. After receiving the message from the second agent, the first agent checks it and notifies the second agent whether the message she receives is consistent with her original message. Through the feedback, both agents will know whether the two communication channels (and thus the two translation models) perform well and can improve them accordingly.
4. The game can also be started from the second agent with an original message in B, and then the two agents will go through a symmetric process and improve the two channels according to the feedback.

## Methodology

Consider two monolingual corpora $$D_A$$ and $$D_B$$ which contain sentences from language A and B respectively. These two corpora are not necessarily aligned with each other, and they may even have no topical relationship with each other at all. The goal is to improve the accuracy of the two models by using monolingual corpora instead of parallel corpora. 

Assume we already have two well-trained language models LMA and LMB, each of which takes a sentence as input and outputs a real value to indicate how confident the sentence is a natural sentence in its own language. Here the language models can be trained either using other resources, or just using the monolingual data $$D_A$$ and $$D_B$$.
