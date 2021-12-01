---
title: A Pre-trained Model Approach to NLP
categories: nlp
tags: 
    - nlp
    - PTM
    - book
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

Book: 自然语言处理：基于预训练模型的方法
Author: 车万翔

<!--more-->

## Basic Neural Network in NLP

### Multi-layer Perceptron

```python
import torch
from torch import nn
from torch.nn import functional as F

class MLP(nn.Module):

    def __init__(self, input_dim, hidden_dim, num_class):
        super(MLP, self).__init__()
        self.linear1 = nn.Linear(input_dim, hidden_dim)
        self.activate = F.relu
        self.linear2 = nn.Linear(hidden_dim, num_class)
    
    def forward(self, inputs):
        hidden = self.linear1(inputs)
        activation = self.activate(hidden)
        outputs = self.linear2(activation)
        probs = F.softmax(outputs, dim=1)
        return probs
mlp = MLP(input_dim=4, hidden_dim=5, num_class=2)
inputs = torch.rand(3, 4)
probs = mlp(inputs)
print(probs)
# tensor([[0.5231, 0.4769],
#         [0.5167, 0.4833],
#         [0.5166, 0.4834]], grad_fn=<SoftmaxBackward>)
```

### Convolutional Neural Network

In a multilayer perceptron, each element input to each layer needs to be multiplied by an independent parameter (weight), which is also called a fully connected or dense layer. However, this is not appropriate for certain types of tasks, such as image recognition tasks, where each pixel is assigned an independent parameter, and the recognition result may change significantly if the position of the object to be recognized is slightly moved. Similar problems exist in natural language processing tasks. For example, for the sentiment classification tasks, the sentiment polarity of sentences is often determined by individual words or phrases, and the positions of these decisive words or phrases in sentences are not fixed. It is difficult to capture this critical local information with full connected layer.
