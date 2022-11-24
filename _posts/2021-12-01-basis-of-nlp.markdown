---
title: A Pre-trained Model Approach to NLP
tags: [nlp, books]
description: A book introducing basic methods on natural language processing.
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

#### Self-attention

$$
y_i = \sum_{j=1}^{n}\alpha_{ij}x_j
$$

If $$x_i$$ and $$x_j$$ are more related, the attention value they calculate is greater, and then the contribution of $$x_h$$ to the new representation $$y_i$$ corresponding to $$x_i$$ is greater.

Through the self-attention mechanism, the relationship between two distant moments can be directly calculated. In the recurrent neural network, because the information is transmitted layer by layer along the time, when two moments with greater correlation are farther apart, greater information loss will occur.

But there are several questions need to be considered.
1. When calculating self-attention, the input position information is not considered, and the sequence cannot be modeled.
2. The input vector assumes(承担) three roles at the same time, which makes it difficult to learn.
3. Only the relationship between two input sequence units is considered, and more complex relationships between multiple input sequence units cannot be modeled.
4. The self-attention calculation results are mutually(互相) exclusive(排斥), and it is impossible to pay attention to multiple inputs at the same time.

#### Transformer

1. Incorporate position information
2. Input vector role information
  Use three different parameter matrices to map the input vector $$x_i$$ into three new vectors $$q_i$$, $$k_i$$, and $$v_i$$ which represent Query, Key, and Value, respectively.
  The new output formula is 

  $$
  \hat{\alpha}_{ij} = attn(q_i, k_i)
  $$

  $$
  \alpha_{ij} = Softmax(\hat{\alpha}_i)_j
  $$

  $$
  y_i = \sum_{j=1}^{n}\alpha_{ij}v_j
  $$
3. Multi-head Self-attention

##### Seq2Seq Model Based on Transformer

Compared with recurrent neural networks, transformers can directly model longer-distance dependencies between input sequence units, which makes the transformer more capable of modeling long sequences. In the encoding stage, multi-core computing devices such as GPU can be used to calculate the self-attention model inside the transformer block in parallel, and the recurrent neural network needs to be calculated one by one, so the transformer has a higher training speed.

However, the amount of parameters of the transformer is too large, that is, the three role mapping matrices of the input vector in the self-attention model, the multi-head mechanism leads to the multiplication of the corresponding parameters and the introduction of non-linear multilayer perceptrons. In addition, it is necessary to stack multiple layers of transformer blocks, so that the amount of parameters is multiplied. The huge amount of parameters makes the transformer model difficult to train, so the pre-training model based on large-scale data came into being.

```python
import torch
from torch import nn

# 创建一个Transformer块，每个输入向量、输出向量的维度为4，头数为2
encoder_layer = nn.TransformerEncoderLayer(d_model=4, nhead=2)

# 随机生成输入，三个参数分别为序列的长度、批次的大小和每个输入向量的维度
src = torch.randn(2, 3, 4)

out = encoder_layer(src)
print(out)
# tensor([[[-0.7729,  1.4483,  0.3960, -1.0714],
#          [-0.9167,  0.8501, -1.0701,  1.1367],
#          [0.9654,  0.7987, -1.5431, -0.2210]],

#         [[-0.5101,  0.5713, -1.3366,  1.2754],
#          [1.2021, -0.9338,  0.7733, -1.0416],
#          [0.5644, -0.7968, -1.1125,  1.3449]]],
#        grad_fn= < NativeLayerNormBackward >)

# 然后将多个Transformer块堆叠起来构成完整的encoder
transformer_encoder = nn.TransformerEncoder(encoder_layer, num_layers=6)
out = transformer_encoder(src)
print(out)
# tensor([[[0.9369, -0.0903,  0.7505, -1.5971],
#          [-1.3297, -0.1221, -0.0367,  1.4885],
#          [1.6117, -0.4730, -0.0544, -1.0843]],

#         [[1.2730, -0.3656,  0.5040, -1.4113],
#          [-0.8596, -0.0741, -0.7205,  1.6542],
#          [0.7634, -0.7496,  1.1879, -1.2017]]],
#        grad_fn= < NativeLayerNormBackward >)

# 解码过程类似，先定义然后堆叠
memory = transformer_encoder(src)
decoder_layer = nn.TransformerDecoderLayer(d_model=4, nhead=2)
transformer_decoder = nn.TransformerDecoder(decoder_layer, num_layers=6)
out_part = torch.rand(2, 3, 4)
out = transformer_decoder(out_part, memory)
print(out)
# tensor([[[1.4655, -1.3585, -0.0339, -0.0731],
#          [-1.1449,  1.4500,  0.3671, -0.6722],
#          [-1.0803,  1.5688,  0.1111, -0.5996]],

#         [[-0.7258, -1.1193,  0.4137,  1.4315],
#          [-1.3195,  1.4779,  0.0972, -0.2556],
#          [-0.5749,  1.6023, -1.0496,  0.0222]]],
#        grad_fn= < NativeLayerNormBackward >)
```

### Training Neural Networks

#### Loss Function

Use the loss function to evaluate the quality of a set of parameters. The smaller the value of the loss function, the more similar the model output is to the real output, and it can be considered that the model performs better at this time. However, if the value of the loss function is too small, the model will overfit to the training data set, which is not suitable for new data.

There are many methods that can reach this goal.
- Regularization
- Dropout
- Early Stopping

There are two main loss functions in deep learning: Mean Squared Error (MSE) and Cross-Entropy (CE) loss.

When dealing with classification problems, cross entropy loss is a more commonly used loss function. It learns faster. The cross-entropy loss only depends on the logarithm of the model's predicted probability of the correct category. Essentially, the cross-entropy loss function formula is to find the log-likelihood function in the maximum likelihood of the distribution of multiple types of output results (Bernoulli distribution).
When the model error is larger, the gradient of the loss function is larger, so the model learns faster; when the model error is smaller, the gradient of the loss function is smaller, and the model learns more slowly.
