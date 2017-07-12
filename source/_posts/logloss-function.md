---
title: log loss及sigmoid函数
date: 2017-07-07 22:41:31
tags:
---

sigmoid是一个S型函数，它的函数图像如下

<center>
![](../img/sigmoid_pic.jpg)
</center>

可以看到他是一个单调递增函数，值域是[0,1]，函数定义为
$$
f(t)=\frac1{1+e^{-t}}  \tag{1}
$$
在机器学习中，sigmoid常作为激活函数或者非线性变换函数，首先看看它的导数
$$
f'(t)=(\frac1{1+e^{-t}})'=\frac{1}{1+e^{-t}}*\frac{e^{-t}}{1+e^{-t}}=(1-f(t))f(t)    \tag{2}
$$
再来看看它的其他性质
$$f(-t)=\frac1{1+e^{t}}=\frac{e^t}{1+e^{-t}}=1-f(t) \tag{3}$$
$$\Rightarrow f(t)+f(-t)==1$$

因为它的值域是[0,1]，因此这个函数常用来定义概率分布函数，对于分类问题，如果定义属于某个类别的概率是$f(x)$，则可以用极大似然估计来求得参数值，例如对于二分类，假设$y\in(0,1)$，则似然函数为
$$S(x)=f(x)^y(1-f(x))^{1-y} \tag{4}$$
对似然函数取log后得
$$log(S(x))=ylog(f(x))+(1-y)log(1-f(x)) \tag{5}$$，化简得

$$log(S(x))=ylog(f(x))+(1-y)log(1-f(x)) $$
$$\Rightarrow log(S(x))=y[log(f(x))-log(1-f(x))] + log(1-f(x)) \tag{6}$$
$$\Rightarrow log(S(x))=ylog(\frac {f(x)}{1-f(x)})+ log(1-f(x)) \tag{7} $$
$$\Rightarrow log(S(x))=yw^Tx-log(1+e^{w^Tx}) \tag{8}$$

而如果假设$y\in(-1,1)$，则有$\frac{1+y}{2}\in(0,1)$，同理有
$$log(S(x))=\frac{1+y}{2}w^Tx-log(1+e^{w^Tx}) \tag{9}$$
如果$y=1$，则有
$$log(S(x))=w^Tx-log(1+e^{w^Tx})=-log(1+e^{-w^Tx})  \tag{10}$$
如果$y=-1$，则有
$$log(S(x))=-log(1+e^{w^Tx}) \tag{11}$$
因此，损失对数损失函数可以写为
$$log(S(x))=-log(1+e^{-yw^Tx}) \tag{12}$$

对于$y\in(0,1)$和$y\in(-1,1)$损失函数的形式，也可以这样推导
首先对于$y\in(0,1)$，有概率公式
$$
\begin{aligned}
\mathbb{P}(y=1|z) & =\sigma(z)=\frac{1}{1+e^{-z}}\\
\mathbb{P}(y=0|z) & =1-\sigma(z)=\frac{1}{1+e^{z}}\\
\end{aligned} \tag{13}
$$

如果不考虑y的取值，可以写成
$$\mathbb{P}(y|z)  =\sigma(z)^y(1-\sigma(z))^{1-y} \tag{14}$$
而由于sigmoid的性质，$1-\sigma(z)=\sigma(-z)$，因此，如果$y\in(-1,1)$，在不考虑y的取值时，概率分布函数可以写成
$$
\mathbb{P}(y|z)=\sigma(yz) \tag{15}
$$
即概率分布函数可以写成
$$\mathbb{P}(y|z)  =\frac{1}{1+e^{-yz}} \tag{16}$$