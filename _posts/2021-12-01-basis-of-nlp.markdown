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

Keep updating...

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

To solve the above problem, a very straightforward idea is to use a small dense layer to extract these local features, such as fixed-size pixel areas in the image, N-grams of words in the text, etc. In order to solve the problem that the position of the key information is not fixed, each area of the input can be scanned in turn, this operation is also called convolution operation. Each small, dense layer used to extract local features is also called a convolutional kernel or filter.

The results of the convolution operation can also be further aggregated, a process called pooling. Commonly used pooling operations include maximum pooling, average pooling, and addition pooling. In the case of maximum pooling, the implication is to retain only the most meaningful local features. the most critical N-gram information in the sentence for classification is retained. The advantage of pooling is that it solves the problem of inconsistent input sizes of samples.

If only one convolutional kernel is used, only a single type of local feature can be extracted. Multiple convolution kernels can be used to extract different kinds of local features. Convolution kernels can be constructed in two ways. One is to use different sets of parameters and use different initialization parameters to obtain different convolution kernels. The other is to extract local features of different scales, such as N-grams of different sizes in sentiment classification.

Multiple convolution kernels output multiple features and then pass through a fully connected classification layer to make the final decision. Finally, multiple convolution layers plus pooling layers are stacked to form deeper networks, which are collectively known as convolutional neural networks.

```python
import torch
from torch.nn import Conv1d

conv1 = Conv1d(in_channels=5, out_channels=2, kernel_size=4)
conv2 = Conv1d(in_channels=5, out_channels=2, kernel_size=3)

inputs = torch.rand(2, 5, 6)

print(inputs)

outputs1 = conv1(inputs)
outputs2 = conv2(inputs)

print(outputs1)
print(outputs2)

from torch.nn import MaxPool1d

# get the max value of the output
pool1 = MaxPool1d(kernel_size=3)
pool2 = MaxPool1d(kernel_size=4)

outputs_pool1 = pool1(outputs1)
outputs_pool2 = pool2(outputs2)

print(outputs_pool1)
print(outputs_pool2)

outputs_pool_squeeze1 = outputs_pool1.squeeze(dim=2)
outputs_pool_squeeze2 = outputs_pool2.squeeze(dim=2)

print(outputs_pool_squeeze1)
print(outputs_pool_squeeze2)

outputs_pool = torch.cat((outputs_pool_squeeze1, outputs_pool_squeeze2), dim=1)

from torch.nn import Linear

linear = Linear(in_features=4, out_features=2)
outputs_linear = linear(outputs_pool)
print(outputs_linear)
```

Both multilayer perceptrons and convolutional neural networks are feed-forward neural networks, and information flows in one direction.

### Recurrent Neural Network

When actually using a recurrent neural network, it is necessary to set a limited number of cycles, which is equivalent to stacking multiple feedforward neural networks that share hidden layer parameters.

#### Normal RNN

```python
from torch.nn import RNN

rnn = RNN(input_size=4, hidden_size=5, batch_first=True)

inputs = torch.randn(2, 3, 4)
outputs, h_n = rnn(inputs)
print(outputs)
print(h_n)

print(outputs.size(), h_n.size())
# torch.Size([2, 3, 5]) torch.Size([1, 2, 5])
# batch_size, seq_len, hidden_size; batch_size, hidden_size
```

#### LSTM (Long Short-Term Memory)

In the original recurrent neural network, information is passed to the output layer layer by layer through multiple hidden layers. Intuitively, this will lead to the loss of information; more essentially, it will make it difficult to optimize network parameters. LSTM can better solve this problem.

>In Recurrent Neural Networks (RNN), gradient explosion or gradient disappearance will cause the network to be unstable, making the network unable to learn well from the training data. The best result is that the network cannot learn on long input data sequences.

```python
from torch.nn import LSTM

lstm = LSTM(input_size=4, hidden_size=5, batch_first=True)

inputs = torch.randn(2, 3, 4)
outputs, (h_n, c_n) = lstm(inputs)

print(outputs)
print(h_n)
print(c_n)
print(outputs.size(), h_n.size(), c_n.size())
# torch.Size([2, 3, 5]) torch.Size([1, 2, 5]) torch.Size([1, 2, 5])
```

The sequence-to-sequence model based on the recurrent neural network has a basic assumption that the last hidden state of the original sequence contains all the information of the sequence. However, this assumption is clearly unreasonable. Especially when the sequence is relatively long, it is more difficult to do this.

### Attention Mechanism

Using the state at time s of the source sequence and the state at the previous time in the target sequence as input, calculate the attention score of the source sequence at time s, the length is the length of the source sequence L, and finally use softmax for the attention score of the entire source sequence at each moment The function is normalized to obtain the final attention weight.

$$
\hat{\alpha}_s = attn(h_s, h_{t-1})
$$

$$
\alpha_s = Softmax(\hat{\alpha})_s
$$

