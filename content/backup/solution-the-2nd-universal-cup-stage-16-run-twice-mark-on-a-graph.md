---
title: 'solution-The 2nd Universal Cup. Stage 16: Run Twice-Mark on a Graph'
date: 2024-01-25 23:19:24
tags: [题解]
published: true
hideInList: false
feature: 
isTop: false
---
# The 2nd Universal Cup. Stage 16: Run Twice-Mark on a Graph

[link](https://qoj.ac/contest/1465/problem/4829)

最搞笑的一集。

### 搞笑解法
蚌埠住了。由于oj肯定是连续测你的程序并跑两遍，于是便有了一个搞笑的做法：判断程序开始时time(0)的奇偶性，然后sleep到下一秒再结束。这样子你就有50%的概率通过此题。

```cpp
#import<time.h>
main(){int t=time(0);puts(t&1?"mark 0":"ok");while(t==time(0));}
```
这样你就在一条边都不动的情况下过了这道题。

从信息论的角度上来讲，这是图灵奖级别的。

### 一个简单但是不知道正确性的解法
找到原图中度数最大的 5 个点，将他们用五次操作连成一个环。在新图中他们也是度数最大的 5 个点。这样我们就可以辨别是否是我们操作过的图。

感性理解一下，度数最大5个点被随机连在一起的概率不是很大？

### 一个官方解法
考虑在原图整出一个5阶无向完全图K5。显然的，自然随机生成出k5的概率非常低。我们在原图中随机搞5个点，直到他们之间原本有5个边，然后我们把它加成k5弄回去。这样我们就做到了辨认。

如何在图中找到k5？考虑你直接在每个点爆搜，剪一下枝，复杂度是 $E(K1)+E(K2)+E(K3)+E(K4)+E(K5)$ 的，毛估估一下在合理范围内。