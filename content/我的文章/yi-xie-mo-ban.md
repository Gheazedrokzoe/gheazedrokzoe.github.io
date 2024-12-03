---
title: ' 一些模板'
date: 2023-11-08 11:28:52
tags: [学习笔记]
published: true
hideInList: false
feature: 
isTop: false
---
备忘录

### 数数题

```cpp

int preasd[MAX];
int inv123[MAX];
int F114[MAX];

int quickPower(int a,int b,int p){int base=a,ans=1;while(b){if(b&1)ans*=base,ans%=p;base*=base;base%=p;b>>=1;}return ans;}
 
int c2(int n, int m){
	if(m > n)	return 0;
	return preasd[n] * F114[m] % mod * F114[n-m] % mod;
}

signed main(){
	preasd[0] = F114[0] = 1;
	preasd[1] = inv123[1] = F114[1] = 1;
	for(int i = 2; i < MAX; i++){
		preasd[i] = 1ll * preasd[i - 1] * i % mod;
		inv123[i] = 1ll * inv123[mod % i] * (mod - mod / i) % mod;
		F114[i] = 1ll * F114[i - 1] * inv123[i] % mod;
	}
}
```

## 网络流

```cpp
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
};
      
struct min_max_flow{
	int cur[MAX], dis[MAX];
	int vis[MAX];
	struct Edge{
		int v, w, c, cp;
	}; 
	
	vector <Edge> g[MAX];
	
	void add_edge(int u, int v, int w, int c){
		Edge e1 = Edge{v, w, c, g[v].size()};
		Edge e2 = Edge{u, 0, -c, g[u].size()};
		g[u].push_back(e1);
		g[v].push_back(e2);
	}
	
	bool bfs(int s, int t)
	{
		queue<int> q;
		for(int i = 0; i <= psz; i++){
			dis[i] = inf;
		}
		memset(vis, 0, sizeof(vis));
		vis[s] = true;
		dis[s] = 0;
		q.push(s);
		while(!q.empty()){
			int u = q.front();
			q.pop(); vis[u] = false;
			int l = g[u].size();
			for(int i = 0; i < l; i++)
			{
				int v = g[u][i].v, r = g[u][i].w, c = g[u][i].c;
				if(r and dis[u] + c < dis[v]){
					dis[v] = dis[u] + c;
					if(!vis[v]){
						vis[v] = true;
						q.push(v); 
					} 
				}
			}
		} 
		return dis[t] != inf; 
	}
	
	int aug(int u, int l, int &cost, int t){
		if(u == t)	return l;
		vis[u] = true;
		int f = 0;
		for(int &i = cur[u]; i < g[u].size(); i++){
			int v = g[u][i].v, r = g[u][i].w, c = g[u][i].c;
			if(dis[v] != dis[u] + c or !r or vis[v])	continue;
			int d = aug(v, min(r, l), cost, t);
			g[u][i].w -= d;
			g[v][g[u][i].cp].w += d;
			f += d, l -= d;
			cost += d*c;
			if(!l)	break;
		}
		vis[u] = false;
		return f;
	}
};       
```



## 3*3矩阵

```cpp

struct Matrix{
	int n = 3, m = 3;
	long long a[3][3];
	Matrix operator *(const Matrix &x) const{
		Matrix c;
		c.a[0][0] = a[0][0] * x.a[0][0] + a[0][1] * x.a[1][0] + a[0][2] * x.a[2][0];
		c.a[0][1] = a[0][0] * x.a[0][1] + a[0][1] * x.a[1][1] + a[0][2] * x.a[2][1];
		c.a[0][2] = a[0][0] * x.a[0][2] + a[0][1] * x.a[1][2] + a[0][2] * x.a[2][2];
		c.a[1][0] = a[1][0] * x.a[0][0] + a[1][1] * x.a[1][0] + a[1][2] * x.a[2][0];
		c.a[1][1] = a[1][0] * x.a[0][1] + a[1][1] * x.a[1][1] + a[1][2] * x.a[2][1];
		c.a[1][2] = a[1][0] * x.a[0][2] + a[1][1] * x.a[1][2] + a[1][2] * x.a[2][2];
		c.a[2][0] = a[2][0] * x.a[0][0] + a[2][1] * x.a[1][0] + a[2][2] * x.a[2][0];
		c.a[2][1] = a[2][0] * x.a[0][1] + a[2][1] * x.a[1][1] + a[2][2] * x.a[2][1];
		c.a[2][2] = a[2][0] * x.a[0][2] + a[2][1] * x.a[1][2] + a[2][2] * x.a[2][2];
		return c;
	}
	Matrix operator +(const Matrix &x) const{
		Matrix c;
		c.clear2();
		c.a[0][0] = a[0][0] + x.a[0][0], c.a[0][1] = a[0][1] + x.a[0][1], c.a[0][2] = a[0][2] + x.a[0][2];
		return c;
	}
	
	void clear2(){
		n = m = 3;
		a[0][0] = a[0][1] = a[0][2] = a[1][0] = a[1][1] = a[1][2] = a[2][0] = a[2][1] = a[2][2] = 0;
		return ;
	}
	
	void clear(){
		n = m = 3;
		a[0][0] = a[0][1] = a[0][2] = a[1][0] = a[1][1] = a[1][2] = a[2][0] = a[2][1] = a[2][2] = 0;
		a[0][0] = a[1][1] = a[2][2] = 1;
		return ;
	}
	void print(){
		for(int i = 0; i <= 2; i++){
			for(int j = 0; j <= 2; j++){
				write(a[i][j]), put(); 
			}endl;
		}endl;
	}
};

Matrix MatrixQuickPower(Matrix a, int b, int mod){
	Matrix ans = a;
	Matrix base = a;
	b--;
	while(b){
		if(b & 1)	ans = ans * base;
		base = base * base;
		b >>= 1;
	}
	return ans;
}

```
## 逆天快速gcd


```cpp
inline int gcd(int a, int b) {int az = __builtin_ctz(a), bz = __builtin_ctz(b), z = (az > bz) ? bz : az, t; b >>= bz; while (a) a >>= az, t = a - b, az = __builtin_ctz(t), a = t < 0 ? -t : t, b = a < b ? a : b; return b << z;}
```

## 枚举子集

```cpp
for(int S = (x - 1) & x; S; S = x & (S - 1))
```

## 一种不容易被卡掉的 pair 哈希

```cpp
static ull splitmix64 (ull x) {
  // http://xorshift.di.unimi.it/splitmix64.c
  x += 0x9e3779b97f4a7c15;
  x = (x ^ (x >> 30)) * 0xbf58476d1ce4e5b9;
  x = (x ^ (x >> 27)) * 0x94d049bb133111eb;
  return x ^ (x >> 31);
}

ull safe_hash (ull x) {
  static const ull base = chrono::steady_clock::now().time_since_epoch().count();
  return splitmix64(x + base);
}

int To(pair <int, int> A){
	int x = A.first;
	int y = A.second;
	// return x + y;
	return (safe_hash(x) + safe_hash(y)) ;
}
```