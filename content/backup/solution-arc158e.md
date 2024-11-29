---
title: 'solution-arc158e'
date: 2024-01-25 23:18:28
tags: [题解]
published: true
hideInList: false
feature: 
isTop: false
---
# [ARC158E] All Pair Shortest Paths

还是挺牛逼的一题。但是为什么其他题解都说很板？看来还是我太菜了，见的题太少了。

主要参考 @TeneryTree

![image](https://img2024.cnblogs.com/blog/2655720/202401/2655720-20240125213240515-1263584201.png)


首先考虑 CDQ 分治，只考虑处理 $[l,mid]$ 中的到 $[mid+1,r]$ 这些点的路径和。

由于列数 $m=2$ 所以我们考虑设 $f_{i,0/1}$ 为左边的点 $(i,0/1)$ 到 $(mid,0)$ 的最短路距离，右边的点 $(i,0/1)$ 到 $(mid+1,0)$ 的最短路距离。设 $g_{i,0/1}$ 为左边的点 $(i,0/1)$ 到 $(mid,1)$ 的最短路距离，右边的点 $(i,0/1)$ 到 $(mid+1,1)$ 的最短路距离。显然这两玩意每次直接 $O(n)$ 递推即可。

然后考虑一下跨区间的贡献是什么。设贡献为 $w$ 则有：

$$ w = 2\sum\limits_{i=l}\limits^{mid}\sum\limits_{j=mid+1}\limits^{r} \min ( f_{i,0/1} + f_{j,0/1},g_{i,0/1} + g_{j,0/1})$$

这玩意看着好像不太好直接计算。也不太知道怎么想到的，考虑这样子做：

$$ w = 2\sum\limits_{i=l}\limits^{mid}\sum\limits_{j=mid+1}\limits^{r} \{ \min ( f_{i,0/1} + f_{j,0/1} - g_{i,0/1}- g_{j,0/1},0) + g_{i,0/1} + g_{j,0/1} \} $$

然后拎出去!

$$ w = 2\sum\limits_{i=l}\limits^{mid}\sum\limits_{j=mid+1}\limits^{r} \{ \min ( f_{i,0/1} + f_{j,0/1} - g_{i,0/1}- g_{j,0/1},0)\} + 4\sum\limits_{i=l}\limits^{mid}g_{i,0/1}\cdot(r-mid) + 4\sum\limits_{i=mid+1}\limits^{r}g_{i,0/1}\cdot(mid-l+1)$$

现在这个式子好看多了qwq。后面那一整串直接计算即可了。而前面那一串取值也只有两种情况，非常好搞。设前面那一串值为 $2w_2$，一下省略 0/1 这一维。设 $p_i = f_i - g_i$ 则有：

$$w_2 = \sum\limits_{i=l}\limits^{mid}\sum\limits_{j=mid+1}\limits^{r}  \min (p_i+p_j,0)$$
$$ = \sum\limits_{i=l}\limits^{mid}\sum\limits_{j=mid+1}\limits^{r}(p_i+p_j)[p_i>-p_j] $$

这玩意相当好弄，只需要排序二分一下做完了。

总的复杂度 $O(n\log^2n)$


[code](https://atcoder.jp/contests/arc158/submissions/49651392 "code")