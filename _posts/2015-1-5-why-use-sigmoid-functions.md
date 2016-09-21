---
layout: post
title: 为何使用Sigmoid函数作为激活函数
category: blog
tag: Machine Learning, Deep Learning
---

在机器学习/深度学习中经常使用到sigmoid函数或者类似sigmoid曲线的函数作为激活函数，激活函数的用处主要是为了增加神经网络模型的非线性，试想网络中的隐藏层依然是线性的，那么最终输入输出之间只是矩阵相乘，依然是线性的。

那么选择这些函数作为激活函数的理由是什么呢？

### 1. sigmoid函数作为激活函数理由

典型的激活函数如sigmoid函数如下:

$$
f(x)=\frac{1}{1+e^{-x}}
$$

其他也有很多类似的激活函数，类似的6种激活函数如图所示:

![sigmoid_function](/images/2015-1-5-why-use-sigmoid-functions/active_function.png)

可以发现这些激活函数的共性：
* 将输入数据映射到0-1或-1 - 1之间；
* 单调递增，连续可导，且导数容易计算；

因为多数数据会呈现倒钟型的概率分布，其概率密度函数也为倒钟型，对其积分则表示概率值，而对倒钟型曲线进行积分运算则有类似激活函数这样的“S”型曲线。

### 2. 使用ReLU作为激活函数

ReLu函数更为贴近实际生物神经元的物理反应，即对正向刺激有反应，反向刺激忽略。ReLU函数为$h=max(0,x)$。

这样做的好处为:

* 减少梯度消失的可能性。当x>0时，ReLU导数为常数；而使用sigmoid函数时，其导数值会随着x绝对值递增而逐渐减小，导致梯度逐渐减小消失。
* 另一个好处是增加网络稀疏性，当x<0时，ReLU函数值为0，当这样的值在网络中越多，稀疏性越强；而sigmoid函数则没有这样的稀疏性。

稀疏性更为接近生物神经元的性质，并且ReLU能有较快的学习速率。较为平滑的激活函数为:

$$
  Softplus=log(1+e^x)
$$

这是Sigmoid函数的原函数，比ReLU更为平滑，如图所示。

![softplus_function](/images/2015-1-5-why-use-sigmoid-functions/softplus.png)
