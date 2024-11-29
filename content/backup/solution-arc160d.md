---
title: 'solution-arc160d'
date: 2024-02-20 19:12:39
tags: [题解]
published: true
hideInList: false
feature: 
isTop: false
---
一道没有那么难但是我还是不会(甚至一开始就想歪了)的计数题。

考虑逆序操作，在一个全零的序列中选择一个数加 $k$ 或连续 $k$ 个数加一。显然这种计数题关键在于如何去重。我们考虑使得每一种最终序列对应唯一的操作序列。发现如果你对于一个长度为 $k$ 的区间进行了 $k$ 次区间加一和你对于其中每一个数分别单点 $+k$ 是等价的。考虑设 $b_i$ 表示在 $[i,i+k-1]$ 操作区间加一的操作次数，则我们可以钦定所有 $\forall i \in [1,n-k+1],b_i < k$。当然还有单点加，我们设 $b_{n-k+1+i}$ 表示在位置 $i$ 进行单点加的操作次数。最后我们只需要对满足条件的不同 $b$ 序列进行简单计数即可。综上，$b$ 序列应满足以下条件：

- $\forall i \in [1,n-k+1],b_i < k$。
- $\sum\limits_{i=1}\limits^{n}b_i = {m\over k}$


计数是简单的，容斥一下，钦定有 $i$ 位不满足条件，相当于先钦定 $i$ 位放有 $k$ 个操作，然后将剩下 ${m\over k} - ik$ 个操作分给 $2n-k$ 个位置，插板法，则有：

$$ans = \sum\limits_{i=0}\limits^{i=n-k+1} (-1)^{i} {n - k + 1 \choose i} {{m\over k} - ik + 2n-k \choose 2n-k}$$

[code](https://atcoder.jp/contests/arc160/submissions/50471291)