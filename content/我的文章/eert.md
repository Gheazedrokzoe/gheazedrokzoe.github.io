---
title: eert
date: 2024-01-25 23:21:22
tags:
  - 题解
  - 联考
published: true
hideInList: false
feature: 
isTop: false
---

平凡的，如果 $S_1$ 没有构成一个外向树森林，那么无解。

否则，如果 $S_1$ 构成了一个个外向树，那么我们考虑将他们的顶点相连、合并。如果不行则无解。

怎么搞?直接暴力枚举当前外向及外向树顶点与其他外向树是否有可以连的边。并查集启发式合并即可。

时间复杂度不是 $O(N+M+K)$，还要加一个什么并查集的 $\log$

为什么是对的？

考虑你会写出如下代码
```cpp
for(auto v3 : st[find(v)]){
	if(!mp[u].count(v3)){
		v2 = v3;
		break;
	}
}
```

你一共只会有 $k$ 次继续枚举。


[code](http://www.gdfzoj.com:23380/submission/371193)


如果是这种做法的话，那这道题可以算非常小清新了。

感谢hzx。

---
来点后记。

比赛时我在想什么？我想到了这个均摊 $O(k)$ 的玩意。

但是补题补sb了，想到 Pink Floyd 去了。

所以写题不要上头!!!!

这道题提醒了我们一些补图上走边走点的算法或许可以均摊?