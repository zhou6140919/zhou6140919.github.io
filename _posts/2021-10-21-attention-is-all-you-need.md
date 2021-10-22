---
title: Attention is All You Need
tags: nlp attention
sidebar:
  nav: Paper-notes
article_header:
  type: overlay
  theme: dark
  background_color: '#203028'
  background_image:
    gradient: 'linear-gradient(135deg, rgba(34, 139, 87 , .4), rgba(139, 34, 139, .4))'
    src: /screenshot.jpg
---




###### `Attention is All You Need`{:.success}


<!--more-->


See [Paper](https://arxiv.org/abs/1706.03762) for more details.
See [Transformer Codes](http://nlp.seas.harvard.edu/2018/04/03/attention.html) for more details.


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

---------------------------

LSTM: see [Sequence to Sequence Learning with Neural Networks][1]

Gated recurrent neural networks: see [Empirical evaluation of gated recurrent neural networks on sequence modeling.][2]



**Reached the boundaries of recurrent language models and encoder-decoder architectures.**

RNN: Factor computation along the symbol positions of the input and output sequences. 
`Parallelization precluded.`{:.warning}
{:.warning}
Recent works has been achieved significant improvements in computational efficiency through factorization(因数分解) tricks and conditional computation. The fundamental constraint of sequential computation however remains.


Attention mechanism used before transformer are used in conjunction(结合) with a recurrent network.

The number of operations required to relate signals from two arbitrary input or output position grows in the distance between positions. Linearly for ConvS2S and logarithmically for ByteNet. 
`This makes it more difficult to learn dependencies between distant positions.`{:.warning}
{:.warning}

In Transformer this is reduced to a constant number of operations, albeit(尽管) at the cost of reduced effective resolution due to averaging attention-weighted positions, an effect we counteract(抵消) with Multi-Head Attention.
{:.succuss}


Self-attention: is an attention mechanism relating different positions of a single sequence in order to compute a representation of the sequence.

>是一种将单个序列的不同位置关联起来以计算序列表示的注意力机制。


End-to-end memory networks are based on a recurrent attention mechanism instead of sequence-aligned recurrence and have been shown to perform well on simple-language question answering and language modeling tasks.
{:.info}

---------------------------

## Model Architecture

Encoder maps an input sequence of symbol representations to a sequence of continuous representations. Decoder then generates an output sequence of symbols one element at a time. At each step the model is auto-regressive, consuming the previously generated symbols as additional input when generating the next.



![structure of Transformer](http://nlp.seas.harvard.edu/images/the-annotated-transformer_14_0.png)

This is the structure of Transformer.



The output of each sub-layer is LayerNorm(x + Sublayer(x)), where Sublayer(x) is the function implemented by the sub-layer itself. To facilitate these residual connections, all sub-layers in the model, as well as the embedding layers, produce outputs of dimension $$d_{model} = 512$$.

LayerNorm: see [Layer Normalization][3]


We also modify the self-attention sub-layer in the decoder stack to prevent positions from attending to subsequent positions. This masking, combined with fact that the output embeddings are offset by one position, ensures that the predictions for position i can depend only on the known outputs at positions less than i. 

这种掩码与输出嵌入偏移一个位置的事实相结合，确保位置 i 的预测只能依赖于小于 i 位置的已知输出。

### Attention Function

The output is computed as a weighted sum of the values, where the weight assigned to each value is computed by a compatibility(相关性) function of the query with the corresponding key.

We compute the attention function on a set of queries simultaneously, packed together into a matrix Q. The keys and values are also packed together into matrices K and V.
{:.success}

$$
Attention(Q,K,V)=softmax((QK^T)/√(d_k))V
$$

Dot product is much faster and more space-efficient, since it can be implemented using highly optimized matrix multiplication code.
{:.info}

We suspect that for large values of $$d_k$$, the dot products grow large in magnitude, pushing the softmax function into regions where it has extremely small gradients. To counteract this effect, we scale the dot products by $$1/√(d_k )$$.

(the variance grows from 1 to dk when q and k dot product.

### Multi-Head Attention

On each of these projected versions of queries, keys and values we then perform the attention function in parallel, yielding $$d_v$$-dimensional output values. These are concatenated and once again projected, resulting in the final values.

Multi-head attention allows the model to jointly attend to information from different representation subspaces at different positions.

$$
MultiHead(Q,K,V)=Concat(head_1,…,head_h)
$$

$$
where\ head_i=Attention(QW_i^Q,KW_i^K,VW_i^V)
$$

>Cross attention:
>Queries from previous decoder layer, keys and values come from the output of the encoder.
>This allows every position in the decoder to attend over all positions in the input sequence.


>Feed-Forward Networks
>FFN(x) = max(0, xW1 + b1 )W2 + b2
>ReLU activation between two linear transformations.512->2048->512

>Input embeddings add positional embeddings.
>Using sine and cosine functions.

--------------------------

## Advantages

One is the total computational complexity per layer. 
{:.info}

Another is the amount of computation that can be parallelized, as measured by the minimum number of sequential operations required.
{:.info}

The third is the path length between long-range dependencies in the network. Learning long-range dependencies is a key challenge in many sequence transduction tasks. One key factor affecting the ability to learn such dependencies is the length of the paths forward and backward signals have to traverse in the network. The shorter these paths between any combination of positions in the input and output sequences, the easier it is to learn long-range dependencies.
{:.info}

| Layer Type | Complexity per Layer | Sequential Operations | Maximum Path Length |
| :---: | :---: | :---: | :---: |
| Self-Attention | $$O(n^2 · d)$$ | $$O(1)$$ | $$O(1)$$ |
| Recurrent | $$O(n · d^2)$$ | $$O(n)$$ | $$O(n)$$ |
| Convolution | $$O(n^2 · d)$$ | $$O(1)$$ | $$O(log_k(n))$$ |
| Self-Attention(Restricted) | $$O(r · n · d)$$ | $$O(1)$$ | $$O(n/r)$$ |


----------------------------------------------

  [1]: https://arxiv.org/abs/1409.3215
  [2]: https://arxiv.org/abs/1412.3555
  [3]: https://arxiv.org/abs/1607.06450


## References
- [The Illustrated Transformer - Jay Alammar](https://jalammar.github.io/illustrated-transformer/)
- [The Annotated Transformer - Alexander M. Rush](https://jalammar.github.io/illustrated-transformer/)

