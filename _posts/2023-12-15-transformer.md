---
title: Transformer
author: ygg
date: 2023-12-15 11:30:00 +0800
categories: [Blogging, Tutorial]
tags: [notes]
math: true
---


## 注意力机制

注意力（attention）机制可以很好地处理变长序列中的依赖关系。在本文中采用的注意力机制是点乘注意力，查询（query）和键（key）点乘之后乘一个scaling factor得到的结果经由softmax归一化得到权重，对值（value）V作加权和得到attention。
$$
Attention(Q,K,V)=softmax(\frac{QK^T}{\sqrt{d_k}})V
$$

在向量维度过高时，点乘的结果变大会使得softmax的导数变小，所以一个scaling factor能够保持梯度的稳定性。

$$
E(q_i)=E(k_i)=0,D(q_i)=D(k_i)=1

E(q*k)=E(\sum q_ik_i)=0, D(q*k)=D(\sum q_ik_i)=d_k
$$

## 多头注意力

多头注意力（multi-head attention）类似于CNN中的多通道输出。在点乘注意力公式中，比对query和key的相似度，从value中加权选择，这一过程缺少可学习的参数，不能学到多样的计算模式。多头注意力将QKV投影到h组$d_{model}/h$维的向量，计算投影后低维向量的attention，最后拼接后线性变换得到结果。
$$
MultiHead(Q,K,V)=concat(head_1,head_2...head_h)W^O \\
head_i=Attention(QW_i^Q,KW_i^K,VW_i^V)
$$
其中
$$
W_i^Q\in \mathbb{R}^{d_{model}*d_k},W_i^K\in \mathbb{R}^{d_{model}*d_k},W_i^V\in \mathbb{R}^{d_{model}*d_v},W^O\in \mathbb{R}^{hd_v*d_{model}} $$。

![](/assets/source/multi-attention.png)

## 位置编码

位置编码（positional encoding）是为了记录单个sequence内部单词的位置关系。在使用attention机制的时候，sequence内部单词的顺序不影响最终的输出（仅仅影响输出向量的位置），因而无法感知到单词顺序的影响。

本文中简单地将单词的embedding向量和根据单词位置计算的同维度位置编码向量相加。位置编码由一组三角函数生成。
$$
PE_{(pos,2i)}= \sin(pos/10000^{(2i/d_{model})}) \\
PE_{(pos,2i+1)} = \cos(pos/10000^{(2i+1)/d_{model}})
$$

## 网络结构

![](/assets/source/transformer.jpg)



### Layer-Normalization

对于一个batch的二维数据，$Q.shape=(n, d_{model}, batch)$，n是sequence长度，d_model是特征向量维度，batch-norm的意思是，将一个batch中的向量在feature维度上进行均值为0，方差为1的归一化。而layer-norm的意思是，在batch维度上归一化。由于序列长度不等，在feature维度上计算均值、方差会受到0填充的较大影响，进行layer-normalize将会使推理和训练时的行为保持一致。
![](/assets/source/Figure_1.png)
![](/assets/source/Figure_2.png)

### 自注意力

在encoder中，输入数据被拷贝成三份输入attention mecanism，即Q=K=V。输出的attention是一组输入的加权和，一般而言，向量和自身点积较大，权重较大，和其他向量点积较小，权重也就较小。

在decoder中，由于inference不能看到完整的输入序列，所以它需要前一时刻的输出作为输入来进行序列生成，这被称之为自回归（auto-regressive）。并且，在训练时刻，为了保持和推理行为的一致，使用掩码来屏蔽当前时刻之后的数据的影响。

解码器中还有一个attention模块，它的Q和K是编码器的输入，V来自前一个掩码attention模块，也就是说，解码器在t时刻根据已有的输出${x_1,x_2,...x_{t-1}}$计算attention作为query，从编码器输出的向量中加权选择与query相似的部分（Q=K）。这可以称为cross attention，即encoder-decoder之间的编码信息传递。

### Point-wise feed forward network

对于sequence中的每一个词向量，使用同一个单隐层的MLP进行变换。
$$
FFN(x)=RELU(xW_1+b_1)W_2+b_2
$$

## Discussion: Why self-attention

比较自注意层与递归层和卷积层，后者通常用于将一个可变长度的符号表示序列$(x1，...，xn)$映射到另一个等长序列$(z1，...，zn),x_i,z_i\in \mathbb{R}^ d$，例如典型序列转换编码器或解码器中的隐藏层。使用自注意的原因有三个。

-  每层的总计算复杂度。
- 可以并行化的计算量，以所需的最小串行操作数来衡量。
- 第三个是网络中长距离依赖关系之间的路径长度。学习长程依赖关系是许多序列转换任务的关键挑战。影响学习此类依赖关系能力的一个关键因素是前向和后向信号必须在网络中穿越的路径长度。输入和输出序列中任意位置组合之间的路径越短，学习远距离依赖关系就越容易。

|         Layer Type          | Complexity per Layer | Sequential Operations | Maximum Path Length |
| :-------------------------: | :------------------: | :-------------------: | :-----------------: |
|       Self-Attention        |     $O(n^2 ·d)$      |        $O(1)$         |       $O(1)$        |
|          Recurrent          |     $O(n ·d^2) $     |        $O(n)$         |       $O(n)$        |
|        Convolutional        |   $O(k ·n ·d^2) $    |        $O(1)$         |    $O(logk(n))$     |
| Self-Attention (restricted) |     $O(r ·n ·d)$     |        $O(1)$         |      $O(n/r)$       |

卷积核宽度 $k < n $的单个卷积层无法连接所有输入和输出位置对。在卷积核连续的情况下，这样做需要堆叠$O(n/k) $个卷积层；在扩张卷积的情况下，则需要堆叠$O(logk(n)) $个卷积层，从而增加了网络中任意两个位置之间最长路径的长度。另外，自注意可以产生更多可解释的模型。从注意力分布来看，单个注意力头明显学会了执行不同的任务，并且许多注意力头似乎还表现出了与句子的句法和语义结构相关的行为。

