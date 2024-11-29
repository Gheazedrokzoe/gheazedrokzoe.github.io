---
title: '2024-10-21-solution-ucup-season3-stage13-sendai-problemO'
date: 2024-10-21 20:29:08
tags: [题解]
published: true
hideInList: false
feature: 
isTop: false
---
# The 3rd Universal Cup. Stage 13: Sendai Problem O Sub Brackets 题解
没想到这辈子还能在 ucup 场上场切中档题。成功拿到了 **8点** 开的队中的首 A.

不知道为啥 AT 上是 *2785。

这个题大致就是你发现两个区间的交长度为偶数一定可以。构造形如 ()()()() 这样就行了。而如果交的长度为奇数就一定不行。这个可以写一个爆搜验证一下。

注意到 $n \leq 500$ 所以我们直接暴力检查性质，暴力建图跑一个最小割就可以过了。

```cpp
// Problem: P - Sub Brackets
// URL: https://atcoder.jp/contests/tupc2023/tasks/tupc2023_p
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
const int MAX = 1010;

struct node{
	int l, r;
}; node a[MAX];

int psz = 2;	

struct flow{
	struct node{
		int v, w, cp;
	}; vector <node> g[MAX];
	
	int dis[MAX];
	
	bool bfs(int s, int t){
		for(int i = 1; i <= psz; i++)	dis[i] = inf;
		dis[s] = 0;
		queue <int> q;
		q.push(s);
		while(!q.empty()){
			int u = q.front();
			q.pop();
			for(auto V : g[u]){
				if(V.w > 0 and dis[V.v] > dis[u] + 1){
					dis[V.v] = dis[u] + 1;
					q.push(V.v);
				}
			}
		}
		if(dis[t] == inf)	return 0;
		return 1;
	}
	
	int cur[MAX];
	
	int aug(int u, int now, int t){
		if(u == t)	return now;
		int ans = 0;
		for(int &i = cur[u]; i < g[u].size(); i++){
			int v = g[u][i].v, w = g[u][i].w, cp = g[u][i].cp;
			if(dis[v] != dis[u] + 1)	continue;
			int ret = aug(v, min(w, now), t);
			g[u][i].w -= ret, g[v][cp].w += ret;
			now -= ret, ans += ret;
			if(now <= 0)	break;
		}
		return ans;
	}
	
	void add_edge(int u, int v, int w){
		g[u].pb(node{v, w, g[v].size()});
		g[v].pb(node{u, 0, g[u].size() - 1});
	}
}; flow g;

int ins[MAX], ous[MAX];

void solve(){
	int n = read(), m = read();
	for(int i = 1; i <= m; i++){
		a[i].l = read(), a[i].r = read();
	}	
	int s = 1, t = 2;
	// psz = 3;
	for(int i = 1; i <= m; i++){
		ins[i] = ++psz; ous[i] = ++psz;
	}
	for(int i = 1; i <= m; i++){
		for(int j = 1; j <= m; j++){
			int L = max(a[i].l, a[j].l);
			int R = min(a[i].r, a[j].r);
			int len = max(0ll, R - L + 1);
			if(len % 2){
				g.add_edge(ins[i], ous[j], inf);
			}
		}
	}
	for(int i = 1; i <= m; i++){
		g.add_edge(s, ins[i], 1);
		g.add_edge(ous[i], t, 1);
	}
	int ans = 0;
	while(g.bfs(s, t)){
		for(int i = 1; i <= psz; i++)	g.cur[i] = 0;
		ans += g.aug(s, inf, t);
	}
	// write(ans), endl;
	write(m - ans / 2), endl;
}

signed main(){
	int t = 1;
	while(t--)	solve();
	return 0;
}
```