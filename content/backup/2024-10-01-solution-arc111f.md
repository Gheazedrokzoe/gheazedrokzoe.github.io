---
title: '2024-10-01-solution-arc111f'
date: 2024-10-01 19:24:32
tags: [题解]
published: true
hideInList: false
feature: 
isTop: false
---
## [ARC111F] Do you like query problems? 题解

怎么拖到现在才写。

推式子题目。

首先套路的转为概率与期望，每一位的贡献显然是独立的，于是我们直接拆贡献。

首先来研究一下操作 $1,2$ 对 $a_i$ 的影响。手玩一下发现这相当于有 $1\over2$ 的概率 $a_i$ 不变，$1\over2$ 的概率等概率变为 $[1,m-1]$ 个数中的任意一个数字。这很好，我们可以将操作 $1,2$ 一视同仁了。

于是我们可以直接算出 $a_i$ 在 $j$ 次操作后的期望数值。设其为 $g_{i,j}$。那么有：

$$ 
\begin{aligned}
 g_{i,0}  &= 0\\
g_{i,j}  &=  p_i \cdot {2m \over {2m+1}} \cdot {1\over2} \cdot {m-1 \over 2} + (1-p_i \cdot {2m \over {2m+1}} \cdot {1\over2} \cdot {m-1 \over 2}) g_{i,j-1}\\

g_{i,j}  &=  {p_i m \over {2m+1}} \cdot {m-1 \over 2} + (1-{p_i m \over {2m+1}}) g_{i,j-1}

\end{aligned}
$$

显然有封闭形式。（自己做的时候就止步于此了。） 考虑设 $h_i = 1-{p_i m \over {2m+1}}$ 则可以推出封闭形式：

$$
g_{i,j} = {1\over2}(m-1)(1-{h_i}^j)
$$


接着我们考虑算出 $a_i$ 在一次操作中被选出来的概率，那么有 $$p_i = {2i(n-i+1)\over n(n+1)}$$

于是我们就可以列出最后的期望等于：
 
$$\sum \limits_{i=1} \limits^{n} {p_i \over 2m+1} \sum \limits_{j=0} \limits^{q-1} g_{i,j} = \sum \limits_{i=1} \limits^{n} {p_i \over 2m+1} \cdot {(m-1)\over2} \cdot(q-{h_i^q - 1 \over h_i - 1})$$

然后就推完了。

参考题解： [link](https://www.luogu.com.cn/article/sbkra7zm)。

``` cpp
// Problem: [ARC111F] Do you like query problems?
// URL: https://www.luogu.com.cn/problem/AT_arc111_f
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
const int mod = 998244353;
int quickPower(int a,int b,int p){int base=a,ans=1;while(b){if(b&1)ans*=base,ans%=p;base*=base;base%=p;b>>=1;}return ans;}
void solve(){
	int n = read(), m = read(), q = read();
	int base = quickPower(n * (n + 1) / 2 % mod, mod - 2, mod);
	int base2 = quickPower((2 * m + 1) % mod, mod - 2, mod);
	int inv2 = quickPower(2, mod - 2, mod);
	int ans = 0;
	for(int i = 1; i <= n; i++){
		int px = i * (n - i + 1) % mod * base % mod;
		// write(px), endl;
		int now = px * base2 % mod * (m - 1) % mod * inv2 % mod;
		int now2 = q;
		int rx = (mod + 1 - px * m % mod * base2 % mod);
		now2 = (now2 + mod - (quickPower(rx, q, mod) - 1 + mod) % mod * quickPower((rx - 1 + mod) % mod, mod - 2, mod) % mod);
		now2 %= mod;
		now = now * now2 % mod;
		ans = (ans + now) % mod;
	}
	int tot = quickPower((n * (n + 1) / 2) % mod * (2 * m + 1) % mod, q, mod);
	ans = ans * tot % mod;
	write(ans), endl;
}

signed main(){
	int t = 1;
	while(t--)	solve();
	return 0;
}
```