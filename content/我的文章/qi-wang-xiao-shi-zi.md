---
title: 期望小柿子
date: 2024-01-20 19:05:32
tags:
  - 学习笔记
  - 鲜花
published: true
hideInList: false
feature: 
isTop: false
---
定义式 $E(x) = \sum \limits_{i}P(x=i)*i$

即随机变量 $x$ 结果为 $i$ 的概率乘上结果。

拆开来 $E(x) = \sum \limits_{i>0}P(x=i)*i + \sum \limits_{i<0}P(x=i)*i$

只看前一部分，$E(x) = \sum \limits_{i\geq0}P(x=i)*i$。

将 $i$ 看成若干个 $P_i$ 相加。

得到 $E(x) = \sum\limits_{i=1}\limits^{\infty}\sum \limits_{j=i}\limits^{\infty}P(x=j)$

单独看 $\sum \limits_{j=i}\limits^{\infty}P(x=j)$ 这部分。这相当于 $P(x\geq i)$.

至此，于是我们得到了一个相当好看的式子：

$E(x) = \sum \limits_{i \geq 0}P(x > i) - \sum\limits_{i \leq 0}P(x<i)$

这也就是7月份集训时zz大佬课件里面我一直搞不懂的东西。

**期望具有线性性**

$E(ax+by) = aE(x)+bE(y)$

$P_i$