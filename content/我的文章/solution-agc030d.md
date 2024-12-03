---
title: solution-agc030d
date: 2024-02-18 15:29:42
tags:
  - 题解
  - atcoder
  - agc
published: true
hideInList: false
feature: 
isTop: false
---
见过相同 Trick 之后貌似也不是太难？

相似题：CF838D

套路的，算期望乘以总方案数。计算期望是简单的，是所有逆序对产生的概率相加。算概率是简单的，直接 DP 即可。

[code](https://atcoder.jp/contests/agc030/submissions/50412449)