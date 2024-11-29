---
title: '2024-10-02-solution-arc121e'
date: 2024-10-02 19:26:20
tags: [题解]
published: true
hideInList: false
feature: 
isTop: false
---
## [ARC121E] Directed Tree 题解

其实不太难的题目。

首先题目条件等价于 $a_i$ 不能是 $i$ 的父亲。显然可以容斥，设 $f_{i,j}$ 表示考虑到了节点 $i$ 钦定 $j$ 个节点不满足条件。发现如果是考虑一个点往它向父亲的链选一个点这样算方案数非常没有前途。然后是一步高妙转换，考虑我们在 $a_i$ 统计答案而不是在 $i$。这样我们设 $f_{u,i}$ 表示 $u$ 子树内 $i$ 个节点不满足条件。显然可以树形背包转移:

$$f_{u,i} \cdot f_{v,j} \to f_{u,i+j}$$

注意这里 $j > 0$。然后我们钦定 $a_i = u$ 产生矛盾。

$$f_{u,i} \cdot (siz_u - 1 - i) \to f_{u,i+1}$$

复杂度是 $O(n^2)$。

```cpp
// Problem: [ARC121E] Directed Tree
// URL: https://www.luogu.com.cn/problem/AT_arc121_e
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
const int MAX = 2e3 + 10;
const int mod = 998244353;

vector <int> g[MAX];
int siz[MAX];
int f[MAX][MAX];
int pre[MAX][MAX];

void dfs(int u){
	siz[u] = 1;
	f[u][0] = pre[u][0] = 1;
	for(int v : g[u]){
		dfs(v);
		for(int j = 0; j <= siz[u]; j++){
			for(int k = 1; k <= siz[v]; k++){
				pre[u][j + k] = (pre[u][j + k] + f[u][j] * f[v][k] % mod) % mod;
			}
		}
		siz[u] += siz[v];
		for(int j = 0; j <= siz[u]; j++) f[u][j] = pre[u][j];
	}
	for(int i = siz[u]; i >= 1; i--){
		f[u][i] = (f[u][i] + f[u][i - 1] * (siz[u] - 1 - (i - 1)) % mod) % mod;
	}
}

int fac[MAX];

void solve(){
	int n = read();
	for(int i = 2; i <= n; i++){
		int u = read();
		g[u].pb(i);
	}	
	dfs(1);
	int pree = 1;
	fac[0] = 1;
	for(int i = 1; i <= n; i++)	fac[i] = fac[i - 1] * i % mod;
	int ans = 0;
	for(int i = 0; i <= n; i++){
		f[1][i] = f[1][i] * fac[n - i] % mod;
		if(i % 2 == 0)	ans = (ans + f[1][i]) % mod;
		else		ans = (mod + ans - f[1][i]) % mod;
	}
	write(ans), endl;
}

signed main(){
	int t = 1;
	while(t--)	solve();
	return 0;
}
```
            