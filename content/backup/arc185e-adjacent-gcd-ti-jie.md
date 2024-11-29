---
title: '[ARC185E] Adjacent GCD 题解'
date: 2024-10-15 16:04:23
tags: []
published: true
hideInList: false
feature: /post-images/arc185e-adjacent-gcd-ti-jie.gif
isTop: false
---
[本题解同步发表于我的博客中](https://wruperd.github.io/post/arc185e-adjacent-gcd-ti-jie/)

第一次场切 ARC E。提供一种简单思考角度的解法。

首先考虑 $m -1$ 的答案推到 $m$ 的答案。发现 $Ans_{m} = 2Ans_{m-1}+ \sum\limits_{i=1}^{m-1} \operatorname{gcd}(a_i,a_m) \cdot 2^{i-1}$。

观察到 $O(n\sqrt V)$ 可过于是考虑枚举 $\sum \limits_{\operatorname{gcd}(a_i, a_m) = x} x \cdot pos_x$ 其中 $pos_x = \sum \limits_{i=1}^{m-1} [\operatorname{gcd}(a_i,a_m) = x] \cdot i$。

但是发现你没办法快速确定 $pos_x$ 的值。所以你考虑直接容斥。枚举 $a_m$ 的因数 $d$。

$$\sum \limits_{\operatorname{gcd}(a_i, a_m) = x} x \cdot pos_x = \sum \limits_{d| a_i, d | a_m} w_d \cdot pos_d$$

接下来考虑 $w_d$ 的意义。发现如果 $d$ 就是 $\operatorname{gcd}(a_i,a_m)$ 的话那么所有 $d$ 的因数的贡献都会再次被算一遍。所以可以简单的看出:

$$ w_x = x - \sum \limits_{d|x} w_d$$

预处理所有的 $w$ 就做完了。

不是很懂为啥大家都是莫反做法。

```cpp
// Problem: E - Adjacent GCD
// URL: https://atcoder.jp/contests/arc185/tasks/arc185_e
// Writer: WRuperD
// Powered by CP Editor (https://cpeditor.org)
#include<bits/stdc++.h>
using namespace std;
const long long inf = 1e18;
const int mininf = 1e9 + 7;
#define int long long
#define pb emplace_back
inline int read(){int x=0,f=1;char ch=getchar();while(ch<'0'||ch>'9'){if(ch=='-')f=-1;ch=getchar();}while(ch>='0'&&ch<='9'){x=(x<<1)+(x<<3)+(ch^48);ch=getchar();}return x*f;}
inline void write(int x){if(x<0){x=~(x-1);putchar('-');}if(x>9)write(x/10);putchar(x%10+'0');}
#define put() putchar(' ')
#define endl puts("")
const int MAX = 5e5 + 10;
const int MAX2 = 1e5 + 10;

const int mod = 998244353;

int a[MAX];
int w[MAX];

int cnt[MAX];

void solve(){
	
	int n = read();
	// write(w[2]), endl;
	for(int i = 1; i <= n; i++){
		a[i] = read();
	}
	
	int preans = 0;
	int now = 1;
	for(int i = 1; i <= n; i++){
		preans = preans * 2 % mod;
		// write(preans), endl;
		for(int j = 1; j * j <= a[i]; j++){
			if(a[i] % j)	continue;
			preans = (preans + w[j] * cnt[j] % mod) % mod;
			if(a[i] / j != j)	preans = (preans + w[a[i] / j] * cnt[a[i] / j] % mod) % mod;
		}
		for(int j = 1; j * j <= a[i]; j++){
			if(a[i] % j)	continue;
			cnt[j] = (cnt[j] + now) % mod;
			if(a[i] / j != j)	cnt[a[i] / j] = (cnt[a[i] / j] + now) % mod;
		}
		write(preans), endl;
		now = now * 2 % mod;
	}
	
	
}

signed main(){
	for(int i = 1; i <= MAX2; i++){
		w[i] = i;
		if(i == 1)	continue;
		for(int j = 1; j * j <= i; j++){
			if(i % j)	continue;
			w[i] = (w[i] + mod - w[j]) % mod;
			if(i / j != j and j > 1)	w[i] = (w[i] + mod - w[i / j]) % mod;	
		}
	}
	int t = 1;
	while(t--)	solve();
	return 0;
}

```