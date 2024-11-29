---
title: 'UOJ#77 A+B Problem'
date: 2024-01-15 23:22:33
tags: [题解]
published: true
hideInList: false
feature: 
isTop: false
---
纪念一下自己在UOJ上通过的第一道除了 A+B problem 和 Quine 之外的题。

[题目链接](https://uoj.ac/problem/77)

~~不对啊，这道也是 A+B problem~~

### 正文

看到什么黑白染色，分别得到代价，容易想到最小割。

建图是长这样的： 对于每一个点 $i$ 我们连边 $(s,i,b_i), (i,t,w_i)$.考虑那个什么奇怪的点的限制，对于每一个点建一个虚点 $i$ 然后对于所有满足条件的 $j$ 连边 $(i',j,inf)$.这样连边正确性显然。

BTW,如果限制是方格 $j$ 是白色怎么做啊?/yun 求教。

### 优化
这道题图比较复杂，貌似不能模拟网络流？（谁会能不能教教我啊qwq）

由于上面的边数是 $n^2$ 的，完全跑不了，所以考虑优化建图，减少边数。考虑一个点是向一个区间进行连边，那么显然直接线段树优化建图即可。这里可能需要用到主席树，原因是需要 $n$ 个版本的树的节点。边数降到了 $n \log n$ 

<details>
<summary>点击查看代码</summary>

```
// Problem: #77. A+B Problem
// Contest: UOJ
// URL: https://uoj.ac/problem/77
// Memory Limit: 48 MB
// Time Limit: 2000 ms
// 
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
const int MAX = 1e5 + 10;

int psz = 2;

struct flow{
	struct node{
		int v, w, cp;
	}; vector <node> g[MAX];
	
	int dis[MAX];
	
	bool bfs(int s, int t){
		for(int i = 1; i <= psz; i++)	dis[i] = mininf;
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
		if(dis[t] == mininf)	return 0;
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


int a[MAX], b[MAX], w[MAX], l[MAX], r[MAX], p1[MAX];
int p[MAX], p2[MAX];
int lsh[MAX];
int rt[MAX];

int s[MAX << 5], ls[MAX << 5], rs[MAX << 5]; 

void pushup(int x){
	s[x] = s[ls[x]] + s[rs[x]];
}

void upd(int l, int r, int pos, int prex, int val, int &x){
	if(!x)	x = ++psz;
	s[x] = s[prex], ls[x] = ls[prex], rs[x] = rs[prex];
	if(l == r){
		s[x]++;
		g.add_edge(x, p[val], inf);
		g.add_edge(x, prex, inf);
		return ;
	}
	int mid = (l + r) >> 1;
	if(pos <= mid)	ls[x] = 0;
	else rs[x] = 0;
	if(pos <= mid)	upd(l, mid, pos, ls[prex], val, ls[x]);
	else  upd(mid + 1, r, pos, rs[prex], val, rs[x]);
	if(ls[x])	g.add_edge(x, ls[x], inf);
	if(rs[x])	g.add_edge(x, rs[x], inf);
	pushup(x);
}

void query(int l, int r, int dl, int dr, int val, int &x){
	if(!x)	return ;
	if(dl <= l and r <= dr){
		g.add_edge(p2[val], x, inf);
		return ;
	}
	int mid = (l + r) >> 1;
	if(dl <= mid)	query(l, mid, dl, dr, val, ls[x]);
	if(dr > mid)	query(mid + 1, r, dl, dr, val, rs[x]);
}

void solve(){
	int n = read();
	int s = 1, t = 2;
	int ans = 0;
	for(int i = 1; i <= n; i++){
		a[i] = read(), b[i] = read(), w[i] = read(), l[i] = read(), r[i] = read(), p1[i] = read();
		p[i] = ++psz, p2[i] = ++psz;
		ans += b[i] + w[i];
		g.add_edge(s, p[i], b[i]), g.add_edge(p[i], t, w[i]);
		g.add_edge(p[i], p2[i], p1[i]);
		lsh[i] = a[i];
	}
	sort(lsh + 1, lsh + n + 1);
	int n2 = unique(lsh + 1, lsh + n + 1) - lsh - 1;
	for(int i = 1; i <= n; i++){
		a[i] = lower_bound(lsh + 1, lsh + n2 + 1, a[i]) - lsh;
		l[i] = lower_bound(lsh + 1, lsh + n2 + 1, l[i]) - lsh;
		r[i] = upper_bound(lsh + 1, lsh + n2 + 1, r[i]) - lsh - 1;
	}
	for(int i = 1; i <= n; i++){
		if(r[i] >= l[i])	query(1, n2, l[i], r[i], i, rt[i - 1]);
		rt[i] = 0;
		upd(1, n2, a[i], rt[i - 1], i, rt[i]);
	}
	while(g.bfs(s, t)){
		for(int i = 1; i <= psz; i++)	g.cur[i] = 0;
		ans -= g.aug(s, inf, t);
	}
	write(ans), endl;
}

signed main(){
	int t = 1;
	while(t--)	solve();
	return 0;
}
```
</details>
