---
title: ARC *2000~*2800 乱做
date: 2024-01-28 20:28:28
tags:
  - 学习笔记
  - 题解
  - atcoder
published: true
hideInList: false
feature: 
isTop: false
---
开坑。

## ARC 158

finished on 2024.1.26

### [ARC158D] Equation

不好玩的随机化题。有一说一第一次见这么随机的。

考虑设左边柿子为 $F(x,y,z)$ 右边为 $G(x,y,z)$ ，考虑你直接去随机一组 $(x,y,z)$，此时设 $F(x,y,z) = tG(x,yz)$ 则有 $F({x\over t},{y\over t},{z\over t}) = G({x\over t},{y\over t},{z\over t})$。在模意义下这都很好算。但是可能会有一些情况是 $G = 0$ 或者 $x,y,z$ 有相同的数。继续随机即可。

### [ARC158E] All Pair Shortest Paths

https://wruperd.github.io/post/solution-arc158e/

*2600

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

$$w_2 = \sum\limits_{i=l}\limits^{mid}\sum\limits_{j=mid+1}\limits^{r} \min (p_i+p_j,0)$$
$$ = \sum\limits_{i=l}\limits^{mid}\sum\limits_{j=mid+1}\limits^{r}(p_i+p_j)[p_i>-p_j] $$

这玩意相当好弄，只需要排序二分一下做完了。

总的复杂度 $O(n\log^2n)$

[code](https://atcoder.jp/contests/arc158/submissions/49651392 "code")

Some more experiences P7482 不条理狂诗曲

## ARC157

### [ARC157D] YY Garden

haven't written yet

### [ARC157E] XXYX Binary Tree

虚高 *2800，如果放模拟赛的话人均场切了。

首先，这题的关键点这是一颗 **二叉树** 洛谷没有翻译出来。![/wul](https://www.emojiall.com/images/60/qq/1f926.gif)

读一下题目容易发现有一个不存在父亲和儿子同时是 Y 这个很强的限制。这启发了我们去看有关于 Y 的限制。发现给你了个什么 YX 的数量，这是什么？这个显然是非叶子节点的 Y 的数量的两倍。再看 XY 的数量是什么，这个也显然是非根节点 Y 的数量的两倍。知道了这些性质，再分类讨论一下根节点是否是 Y 你就知道了叶子节点上 Y 的个数了！

现在整道题的限制只剩下两个了,一个是在非叶子节点放置 Y 的数量和在叶子节点放置 Y 的数量。由于这题只是让你验证是否有解而非计数，所以你设 $f_{u,j,0/1}$ 表示在节点 $u$ 子树中选了 $j$ 个叶子为 Y,有没有选择当前节点时最多选择多少个非叶子节点放 Y。直接转移即可。

时间复杂度 $O(n^2)$

[code](https://atcoder.jp/contests/arc157/submissions/49673088)

## ARC156

finished on 2024.1.29

### [ARC156C] Tree and LCS

不好玩的构造题。zlt没场切，不写题解了。
https://atcoder.jp/contests/arc156/submissions/49684957

### [ARC156D] Xor Sum 5

*2500

参考https://www.luogu.com.cn/blog/CFA-44/solution-at-arc156-d

众所周知的是，异或两次等于没有。

考虑对于每一个序列找出双射，发现对于一个序列 $A$ 如果它不是一个回文序列则一定可以将他反过来，设其为 $A'$,则 $A \oplus A' =0$，且 $A$ 与 $A'$ 构成双射。因此，我们只需要统计所有为回文序列的 $A$。

设 $f_i$ 为长度为 $i$ 的所有 $A$ 的异或和。

对于一个偶回文序列，显然有 $f_{2i} = 2f_i$，但是对于一个奇回文序列就不太好搞了。考虑加一维，设 $f_{i,j}$ 表示所有长度为 $i$ 序列 $A$ 的 $j + \sum\limits_{k=1}\limits^{i}a_{A_i}$ 的值异或和。现在感觉可以做了一点。

对于偶回文串：$f_{2i,j} = 2f_{i,\lfloor{j\over2}\rfloor}+[nj \mod2=0]$。$[nj \mod2=0]$ 的意义留给读者自行思考。

对于奇回文串，我们枚举中间那个数是什么，然后递归至偶回文串情况：
$f_{2i,j} = \bigoplus\limits_{k=1}\limits^{n} 2f_{i,\lfloor{(j+a_k)\over2}\rfloor}+[n(j+a_k) \mod2=0]$。

特判一下 $i=1$ 的情况。

[code](https://atcoder.jp/contests/arc156/submissions/49786369)

## ARC155

最自闭的一集。

## ARC154

finished on 2024.1.29

### [ARC154D] A + B > C ?

只有一道，没想到吧!/cf

非常简单的一题，但是我又玩泥巴了。直接考虑排序，显然最多有 $n\log n$ 次比较，可以过。怎么比较两个数的大小？先用 $n-1$ 次问出 1 在哪里即可。

懒得写排序？stable_sort。

[code](https://atcoder.jp/contests/arc154/submissions/49792953)

### ARC113

#### [ARC113E] Rvom and Rsrev

[link](https://wruperd.github.io/post/solution-arc113e/)

模拟赛题，大力分讨即可？具体看上面 link。

[code](https://atcoder.jp/contests/arc113/submissions/49809035)