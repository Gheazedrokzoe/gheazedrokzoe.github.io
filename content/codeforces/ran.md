---
title: In Rainbows2
draft: false
tags:
  - example-tag
---

一些 CodeForces 题目的简要题解。


### CF1805D A Wide, Wide Graph

*1900

有诈骗题那味了。不难发现/证明一个点不与其他点联通当且仅当它没有出边。换根 dp 求出以每个点为根的最大深度即可。

<details>

<summary>点击查看代码</summary>

```cpp
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
vector <int> g[MAX];

int f[MAX], f3[MAX];
int f2[MAX];

void dfs(int u, int fa){
    for(int v : g[u]){
        if(v == fa) continue;
        dfs(v, u);
        if(f[v] + 1 > f[u]){
            f3[u] = f[u];
            f[u] = f[v] + 1;
        }else{
            f3[u] = max(f3[u], f[v] + 1);
        }
    }
}

void dfs2(int u, int fa, int premx){
    f2[u] = max(f[u], premx);
    for(int v : g[u]){
        if(v == fa) continue;
        if(f[v] + 1 == f[u]){
            dfs2(v, u, max(premx + 1, f3[u] + 1));
        }else{
            dfs2(v, u, max(premx + 1, f[u] + 1));
        }
    }
}

int ret[MAX];

void solve(){
    int n = read();
    for(int i = 1; i < n; i++){
        int u = read(), v = read();
        g[u].pb(v), g[v].pb(u);
    }
    dfs(1, 1);
    f2[1] = f[1];
    dfs2(1, 1, -inf);
    for(int i = 1; i <= n; i++) ret[f2[i] + 1]++;
    int now = 1;
    for(int i = 1; i <= n; i++){
        now += ret[i];
        now = min(now, n);
        write(now), put();
    }
    endl;
}

signed main(){
    int t = 1;
    while(t--)  solve();
    return 0;
}
```

</details>

### CF1819C The Fox and the Complete Tree Traversal

*2400 简单题。首先链肯定可以解决（黑白染色），通过手玩满多叉树的情况不难发现

你在链上面挂深度 $\geq 2$ 的子树一定无解。否则一样黑白染色即可。

<details>

<summary>点击查看代码</summary>

```cpp
// Problem: The Fox and the Complete Tree Traversal
// URL: https://www.luogu.com.cn/problem/CF1819C
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
const int MAX = 2e5 + 10;

vector <int> g[MAX];
int dis[MAX], pre[MAX];

void dfs(int u, int fa){
	for(int v : g[u]){
		if(v == fa)	continue;
		dis[v] = dis[u] + 1;
		pre[v] = u;
		dfs(v, u);
	}
}

bool vis[MAX];
int cnt;

int work(int u, int fa){
	for(int v : g[u])	if(v != fa)	return work(v, u) + 1;
	return 1;
}

vector <int> A, B;

void solve(){
	int n = read();
	for(int i = 1; i < n; i++){
		int u = read(), v = read();
		g[u].pb(v), g[v].pb(u);
	}
	dfs(1, 1);
	int rt = 0;
	for(int i = 1; i <= n; i++)	if(dis[i] > dis[rt])	rt = i;
	dfs(rt, rt);
	int rt2 = 0;
	for(int i = 1; i <= n; i++)	if(dis[rt2] < dis[i])	rt2 = i;	
	int now = rt2;
	vector <int> g2;
	g2.pb(now);
	vis[now] = 1;
	while(now != rt){
		now = pre[now];
		g2.pb(now);
		vis[now] = 1;
	}
	for(int i = 1; i <= n; i++){
		if(!vis[i])	continue;
		cnt = 0;
		for(int v : g[i]){
			if(!vis[v])	cnt = max(cnt, work(v, i));
		}
		if(cnt >= 2){
			puts("No");
			exit(0);
		}
	}
	for(int i = 0; i < g2.size(); i++){
		int u = g2[i];
		if(i % 2){
			A.pb(u);
			for(int v : g[u])	if(!vis[v])	B.pb(v);
		}else{
			B.pb(u);
			for(int v : g[u])	if(!vis[v])	A.pb(v);
		}
	}
	puts("Yes");
	reverse(B.begin(), B.end());
	for(int i = 0; i < A.size(); i++)	write(A[i]), put();
	for(int i = 0; i < B.size(); i++)	write(B[i]), put();
	endl;
}

signed main(){
	int t = 1;
	while(t--)	solve();
	return 0;
}
```

</details>

### CF2026E Best Subsequence
*2500
考虑网络流，每个 $a_i$ 向他的 $1$ 进制位连正无穷的边。源点向 $a_i$ 连流量为 1 的边，进制位向汇点连流量为 1 的边。然后就是最小割。

<details>

<summary>点击查看代码</summary>

```cpp
// Problem: Best Subsequence
// URL: https://www.luogu.com.cn/problem/CF2026E
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

int id[MAX];
int a[MAX];

void solve(){
	int n = read();
	for(int i = 1; i <= n; i++){
		a[i] = read();
	}
	psz = 2;
	int s = 1, t = 2;
	for(int i = 1; i <= 60; i++){
		id[i] = ++psz;
		g.add_edge(id[i], t, 1);
	}
	for(int i = 1; i <= n; i++){
		int now = ++psz;
		g.add_edge(s, now, 1);
		for(int j = 1; j <= 60; j++){
			if(a[i] & (1ll << (j - 1))){
				g.add_edge(now, id[j], inf);
			}
		}
	}
	int ans = 0;
	while(g.bfs(s, t)){
		for(int i = 1; i <= psz; i++)	g.cur[i] = 0;
		ans += g.aug(s, inf, t);
	}
	write(n - ans), endl;
	for(int i = 1; i <= psz; i++)	g.g[i].clear();
}

signed main(){
	int t = read();
	while(t--)	solve();
	return 0;
}
```

</details>

### CF1815D XOR Counting
*2600
有点虚高。 $n,m$ 大的吓人，而且 $m$ 居然不是 $\sum m\leq 10^5$ 于是果断打表。发现 $m>3$ 的情况都一样，而且明显可以 $O(1)$ 算出。只剩下 $m=2$ 的情况了。

这部分不太容易。发现如果 $n$ 为奇数那么最后一位一定为 1。启发了一个分治的算法。设 $f_i,g_i$ 表示 $n=i$ 时候的答案和值的方案数。那么转移有：

$$f_i = 2f_{i-1\over 2} + g_{i-1\over 2}, i \nmid 2$$
$$f_i = 2f_{i\over 2} + 2f_{i\over 2-1}, i \mid 2$$

<details>

<summary>点击查看代码</summary>

```cpp
// Problem: XOR Counting
// URL: https://www.luogu.com.cn/problem/CF1815D
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

map <int,int> f, g;

void dfs(int x){
	if(f.count(x))	return ;
	if(x % 2){
		dfs(x / 2);
		f[x] = (2 * f[x / 2] % mod + g[x / 2]) % mod, g[x] = g[x / 2]; 
	}else{
		dfs(x / 2);dfs(x / 2 - 1);
		f[x] = 2 * (f[x / 2] + f[x / 2 - 1]) % mod;
		g[x] = (g[x / 2] + g[x / 2 - 1]) % mod;
	}
}

void solve(){
	int n = read(), m = read();
	f[0] = 0, g[0] = 1;
	if(m == 1){
		write(n % mod), endl;
	}else if(m > 2){
		if(n % 2){
			write((1 + n) % mod * ((n + 1) % mod) % mod * quickPower(4, mod - 2, mod) % mod), endl;
		}	
		else{
			write((1 + n / 2 % mod) % mod * (n / 2  % mod) % mod), endl;
		} 
	}else{
		dfs(n);
		write(f[n]), endl;
	}
}

signed main(){
	int t = read();
	while(t--)	solve();
	return 0;
}
```
</details>

### CF212D Cutting a Fence
*2500
考虑一个数的贡献。然后差分就过了。

<details>
<summary>点击查看代码</summary>

```cpp
// Problem: Cutting a Fence
// URL: https://www.luogu.com.cn/problem/CF212D
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
const int MAX = 1e6 + 10;

int a[MAX];
int L[MAX], R[MAX];

int s[MAX], s2[MAX];

void solve(){
	int n = read();
	for(int i = 1; i <= n; i++){
		a[i] = read();
	}
	stack <pair <int, int> > stc;
	stc.push(make_pair(-1, 0));
	for(int i = 1; i <= n; i++){
		while(stc.top().first > a[i])	stc.pop();
		L[i] = stc.top().second;
		stc.push(make_pair(a[i], i));			
	}
	while(!stc.empty())	stc.pop();
	stc.push(make_pair(-1, n + 1));
	for(int i = n; i >= 1; i--){
		while(stc.top().first >= a[i])	stc.pop();
		R[i] = stc.top().second;
		stc.push(make_pair(a[i], i));	
	}
	for(int i = 1; i <= n; i++){
		int len = R[i] - L[i] - 1;
		int add = min(i - L[i], R[i] - i);
		// write(i), put(), write(len), put(), write(add), endl;
		s2[1] += a[i], s2[1 + add] -= a[i];
		s2[len + 2] += a[i], s2[len + 2 - add] -= a[i];
	}
	int now = 0;
	for(int i = 1; i <= n; i++){
		now += s2[i];
		s2[i] = now;
	}
	now = 0;
	for(int i = 1; i <= n; i++){
		now += s2[i];
		s2[i] = now;
	}
	int q = read();
	while(q--){
		int i = read();
		printf("%.9lf\n", double(s2[i]) / double(n - i + 1));	
	}
	
}
signed main(){
	int t = 1;
	while(t--)	solve();
	return 0;
}
```
</details>

### CF1889C2 Doremy's Drying Plan (Hard Version)
*2600
设 $f_{i,j}$ 表示前 $i$ 个点钦定点 $i$ 不被覆盖最大答案。则有 $f_{i,j} = \max f_{k,j-cst} + 1$ 其中 $cst$ 为包含 $i$ 但不包含 $k$ 的方案数。考虑优化，发现 $cst \leq k$ 所以最多只有 $k+1$ 段转移。使用 st 表以做到 $nk^2$。代码实在是不想写了，破防了。

### CF1685C Bring Balance
*2600
感受出来了。首先直觉告诉我们答案上界非常少。不难发现上界为 2。具体的，设 $i$ 为前缀和最大的点，那么反转 $[1,i],[i+1,2n]$ 一定可行。只需要看怎么得到答案为 1 的。发现反转序列一定要包含最左边的小于 0 的和最右边小于 0 的。那么贪心的选左边最大的和右边最大的反转看看行不行。

<details>

<summary>点击查看代码</summary>

```cpp
// Problem: Bring Balance
// URL: https://www.luogu.com.cn/problem/CF1685C
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
const int MAX = 1e6 + 10;

char a[MAX];
int s[MAX];

void solve(){
	int n = read();
	bool fl = 0;
	for(int i = 1; i <= 2 * n; i++){
		a[i] = getchar();
		while(a[i] != '(' and a[i] != ')')	a[i] = getchar();
		s[i] = s[i - 1] + ((a[i] == '(') ? 1 : -1);
		fl |= (s[i] < 0);
	}
	if(!fl)	{
		puts("0");
		return ;
	}
	int l = -1, r = -1;
	for(int i = 1; i <= 2 * n; i++){
		if(s[i] < 0){
			if(l == -1)	l = i;
			r = i;
		}
	}
	int rl = 0;
	for(int i = 1; i <= l; i++)	if(s[i] > s[rl])	rl = i;
	int rr = 2 * n;
	for(int i = 2 * n; i >= r; i--)	if(s[i] > s[rr])	rr = i;
	l = rl + 1, r = rr;
	int mk = 0;
	for(int i = 1; i <= 2 * n; i++)	if(s[i] >= s[mk])	mk = i;
	for(int i = 1; i <= (r - l + 1) / 2; i++){
		swap(a[l + i - 1], a[r - i + 1]);
	}
	fl = 0;
	for(int i = 1; i <= 2 * n; i++){
		s[i] = s[i - 1] + ((a[i] == '(') ? 1 : -1);
		fl |= (s[i] < 0);
	}
	if(!fl){
		puts("1");
		write(l), put(), write(r), endl;
		return ;
	}
	puts("2");
	write(1), put(), write(mk), endl;
	write(mk + 1), put(),write(2 * n), endl;
}

signed main(){
	int t = read();
	while(t--)	solve();
	return 0;
}
```

</details>

### CF1893D Colorful Constructive
*2600

感觉对上脑电波的难度有点高。考虑把每个架子划分成若干个长度为 $c_i$ 的架子。每个新架子要求所有元素不同。不难发现这和合法方案形成了双射。然后你就贪心的填就好了。

<details>
<summary> 点击查看代码 </summary>

```cpp
// Problem: Colorful Constructive
// URL: https://www.luogu.com.cn/problem/CF1893D
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

int a[MAX], b[MAX], c[MAX];
vector <int> ans[MAX];
int cnt[MAX];
int vis[MAX];

void solve(){
	int n = read(), m = read();
	for(int i = 1; i <= n; i++){
		a[i] = read();
		cnt[i] = 0;
	}
	for(int i = 1; i <= n; i++){
		cnt[a[i]]++;
	}
	for(int i = 1; i <= m; i++){
		b[i] = read();
		ans[i].clear();
	}
	vector <pair<int,int>> A;
	for(int i = 1; i <= m; i++){
		c[i] = read();
		for(int j = 1; j <= b[i] / c[i]; j++)	A.pb(make_pair(c[i], i));
		if(b[i] % c[i])	A.pb(make_pair(b[i] % c[i], i));
	}
	sort(A.begin(), A.end());
	priority_queue <pair<int,int>> que;
	for(int i = 1; i <= n; i++){
		if(cnt[i])	que.push(make_pair(cnt[i], i));
	}
	for(int i = A.size() - 1; i >= 0; i--){
		vector <pair<int,int>> got;
		int len = A[i].first;
		for(int j = 1; j <= len; j++){
			if(que.empty()){
				puts("-1");
				return ;
			}
			auto u = que.top();
			que.pop();
			u.first--;
			ans[A[i].second].pb(u.second);
			if(u.first)	got.pb(u);
		}
		for(auto u : got)	que.push(u);
	}
	for(int T = 1; T <= m; T++){
		reverse(ans[T].begin(), ans[T].end());
		for(int i = 0; i < b[T] % c[T]; i++)	vis[ans[T][i]] = i + 1, write(ans[T][i]), put();
		for(int i = b[T] % c[T]; i < b[T]; i += c[T]){
			priority_queue <pair<int,int>> que; 
			for(int j = i; j <= i + c[T] - 1; j++){
				if(vis[ans[T][j]])	que.push(make_pair(-vis[ans[T][j]], ans[T][j]));
				else que.push(make_pair(1, ans[T][j]));
			}
			for(int j = i - 1; j >= max(0ll, i - 1 - c[T] + 1); j--){
				vis[ans[T][j]] = 0;
			}
			for(int j = i; j <= i + c[T] - 1; j++){
				vis[que.top().second] = j + 1; 
				write(que.top().second), put();
				que.pop();
			}
		}
		for(int j = b[T] - 1; j >= max(0ll, b[T] - 1 - c[T] + 1); j--)	vis[ans[T][j]] = 0;
		endl;
	}
}

//20 18 12 11 8 15 12 8 20 17 

signed main(){
	int t = read();
	while(t--)	solve();
	return 0;
}
```

</details>

### CF677D Vanya and Treasure
*2300
 直接考虑 $i,i+1$ 的贡献然后拆绝对值扫描线。

 <details>
 <summary> 点击查看代码 </summary> 

 ```cpp
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
const int MAX = 705;

int a[MAX][MAX];
int dis[MAX][MAX];

vector < pair<int,int>> col[MAX * MAX];

struct Seg{
	int s[MAX << 2], tag[MAX << 2], tag2[MAX << 2];

	void pushup(int x){
		s[x] = min(s[x << 1], s[x << 1 | 1]);
	}
	
	void pushdown(int x, int l, int r){
		if(tag2[x]){
			s[x << 1] = s[x << 1 | 1] = inf;
			tag[x << 1] = tag[x << 1 | 1] = 0;
			tag2[x << 1] = tag2[x << 1 | 1] = 1;
			tag2[x] = 0;
		}
		if(tag[x]){
			s[x << 1] += tag[x], s[x << 1 | 1] += tag[x];
			tag[x << 1] += tag[x], tag[x << 1 | 1] += tag[x];
			tag[x] = 0;
		}
	}
	
	void upd(int l, int r, int pos, int val, int x){
		if(l == r){
			s[x] = val;
			tag[x] = tag2[x] = 0;
			return ;
		}
		int mid = (l + r) >> 1;
		pushdown(x, l, r);
		if(pos <= mid)	upd(l, mid, pos, val, x << 1);
		else upd(mid + 1, r, pos, val, x << 1 | 1);
		pushup(x);
	}
	
	void add(int val){
		pushdown(1, 1, 1);
		s[1] += val;
		tag[1] += val;
	}
	
	void clear(){
		s[1] = inf, tag[1] = 0;
		tag2[1] = 1;
	}
	
	int query(int l, int r, int dl, int dr, int x){
		if(dl <= l and r <= dr)	return s[x];
		int mid = (l + r) >> 1, ans = inf;
		pushdown(x, l, r);
		if(dl <= mid)	ans = min(ans, query(l, mid, dl,  dr, x << 1));
		if(dr > mid)	ans = min(ans, query(mid + 1, r, dl, dr, x << 1 | 1));
		pushup(x);
		return ans;
	}
	
}; Seg  seg1, seg2;



void solve(){
	int n = read(), m = read(), p = read();
	for(int i = 1; i <= n; i++)	for(int j = 1; j <= m; j++)	a[i][j] = read();
	for(int i = 1; i <= n; i++){
		for(int j = 1; j <= m; j++)	dis[i][j] = inf;
	}
	for(int i = 1; i <= n; i++)	for(int j = 1; j <= m; j++)	col[a[i][j]].pb(make_pair(i, j));
	for(int i = 1; i <= p; i++){
		sort(col[i].begin(), col[i].end());
	}
	for(auto u : col[1]){
		dis[u.first][u.second] = u.first - 1 + u.second - 1;
	}
	for(int i = 2; i <= p; i++){
		int l = 0, r = 0;
		seg1.clear(), seg2.clear();
		int lst = 0;
		while(r < col[i].size()){
			if(l == col[i - 1].size()){
				auto u = col[i][r];
				seg1.add(u.first - lst);			
				seg2.add(u.first - lst);
				lst = u.first;
				int ans = inf;
				ans = min(ans, seg1.query(1, m, 1, u.second, 1) + u.second);
				ans = min(ans, seg2.query(1, m, u.second, m, 1) - u.second);		
				dis[u.first][u.second] = ans;
				r++;	
			}else{
				auto u = col[i][r], v = col[i - 1][l];
				if(u < v){
					seg1.add(u.first - lst);			
					seg2.add(u.first - lst);
					lst = u.first;
					int ans = inf;
					ans = min(ans, seg1.query(1, m, 1, u.second, 1) + u.second);
					ans = min(ans, seg2.query(1, m, u.second, m, 1) - u.second);		
					dis[u.first][u.second] = ans;
					r++;	
				}else{
					u = v;
					seg1.add(u.first - lst);			
					seg2.add(u.first - lst);
					lst = u.first;
					seg1.upd(1, m, u.second, dis[u.first][u.second] - u.second, 1);
					seg2.upd(1, m, u.second, dis[u.first][u.second] + u.second, 1);
					l++;	
				}
			}
		}
		r = col[i].size() - 1, l = col[i - 1].size() - 1;
		lst = n + 1;
		seg1.clear(), seg2.clear();
		while(r >= 0){
			if(l < 0){
				auto u = col[i][r];
				seg1.add(lst - u.first);			
				seg2.add(lst - u.first);
				lst = u.first;
				int ans = inf;
				ans = min(ans, seg1.query(1, m, 1, u.second, 1) + u.second);
				ans = min(ans, seg2.query(1, m, u.second, m, 1) - u.second);		
				dis[u.first][u.second] = min(dis[u.first][u.second], ans);
				r--;	
			}else{
				auto u = col[i][r], v = col[i - 1][l];
				if(u > v){
					auto u = col[i][r];
					seg1.add(lst - u.first);			
					seg2.add(lst - u.first);
					lst = u.first;
					int ans = inf;
					ans = min(ans, seg1.query(1, m, 1, u.second, 1) + u.second);
					ans = min(ans, seg2.query(1, m, u.second, m, 1) - u.second);		
					dis[u.first][u.second] = min(dis[u.first][u.second], ans);
					r--;	
				}else{			
					u = v;
					seg1.add(lst - u.first);			
					seg2.add(lst - u.first);
					lst = u.first;
					seg1.upd(1, m, u.second, dis[u.first][u.second] - u.second, 1);
					seg2.upd(1, m, u.second, dis[u.first][u.second] + u.second, 1);
					l--;	
				}
			}
		}
	}
	int ans = inf;
	for(int i = 1; i <= n; i++)	{
		for(int j = 1; j <= m; j++)	{
			if(a[i][j] == p)	ans = min(ans, dis[i][j]);
		}
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

### CF2038G Guess One Character
*1900 但是大聪明题。

首先问一次 1，如果 1 的个数是 0 或者 $n$ 那么我们可以直接断定这个字符串是全 0/1 的。

否则再问一次 11 的个数。用 1 的个数减去 11 的个数即可得到 1 连续段的个数。这很有用。我们再问 01 出现的次数。不难发现，一个以 0 开头的字符串 01 和 1 的连续段个数相等。以 1 开头的字符串 01 比 1 的连续段个数少一。至此我们可以猜出字符串开头的那个是什么。

<details>
<summary> 点击查看代码 </summary>

``` cpp
// Problem: G. Guess One Character
// URL: https://codeforces.com/contest/2038/problem/G
// Writer: WRuperD
// Powered by CP Editor (https://cpeditor.org)

#include<bits/stdc++.h>
using namespace std;
const long long inf = 1e18;
const int mininf = 1e9 + 7;
#define int long long
#define pb emplace_back
void solve(){
	int n;
	cin >> n;
	cout << "1 1" << endl;
	int a;
	cin >> a;
	if(a == n){
		cout << "0 1 1" << endl;
		int ans;
		cin >> ans;
		return ;
	}
	if(a == 0){
		cout << "0 1 0" << endl;
		int ans;
		cin >> ans;
		return ;
	}
	cout << "1 11" << endl;
	int b;
	cin >> b;
	int c = a - b;
	cout << "1 01" << endl;
	int d;
	cin >> d;
	if(d == c){
		cout << "0 1 0" << endl;
		int ans;
		cin >> ans;
		return ;
	}
	cout << "0 1 1" << endl;
	int ans;
	cin >> ans;
	return ;
}

signed main(){
	int t = 1;
	cin >> t;
	while(t--)	solve();
	return 0;
}
``` 

</details>


### CF1975F Set
 *2600 

 神题。题解后面补。

 <details>
 <summary>点击查看代码</summary>
 
 ```cpp
 // Problem: Set
// URL: https://www.luogu.com.cn/problem/CF1975F
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
const int MAX = 21;

int n;
int f[MAX][(1ll << MAX)];
vector <int> ans;

void dfs(int s, int x){
	if(x == n){
		if(f[x][0] % 2)	 ans.pb(s);
		return ;
	}
	for(int i = 0; i < (1ll << (n - x - 1)); i++)	f[x + 1][i] = f[x][i] & f[x][i | (1ll << (n - x - 1))];
	dfs(s << 1, x + 1);
	for(int i = 0; i < (1ll << (n - x - 1)); i++)	f[x + 1][i] = f[x][i] & (f[x][i | (1ll << (n - x - 1))] >> 1);
	dfs(s << 1 | 1, x + 1);
}

void solve(){
	n = read();
	f[0][0] = 1;
	for(int i = 1; i < (1ll << n); i++){
		f[0][i] = read();
	}
	dfs(0, 0);
	write(ans.size()), endl;
	for(int u : ans)	write(u), endl;
}

signed main(){
	int t = 1;
	while(t--)	solve();
	return 0;
}
```

</details>

### CF1592F1/2 Alice and Recoloring 2
分别是 *2600/*2800。

首先不难发现费用为 1 这个操作很牛逼。直接让选左下和右上的操作🤡了，压根没用。所以只考虑操作右下和左上角。然后是套路地转化（tm又没想到）为差分数组来考虑。时光倒流。不妨令  $a_{i,j} = (c_{i+1,j} + c_{i,j+1} - c_{i + 1,j+1}) \mod 2$。那么全 $a$ 全 0 当且仅当 $c$ 也全 0。然后就转化为选左上的是单点修改，选右下的是修改 $c_{i,j}, c_{n,j}, c_{i,m}, c_{n,m}$.

#### F1
代价为 3。那么只比直接进行操作 1 优秀 1 的代价。所以我们最多进行 1 次右下使得 $c_{n,m} = 0$ 然后就不优了。直接判断存不存在 $c_{i,j},=c_{n,j}= c_{i,m}= c_{n,m}=1$ 即可。这时 $ans = \sum c_{i,j} - 1$ 反之则为 $\sum c_{i,j}$。

<details>
<summary> 点击查看代码 </summary>

```cpp
// Problem: Alice and Recoloring 2
// URL: https://www.luogu.com.cn/problem/CF1592F2
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
const int MAX = 505 + 10;

char a[MAX][MAX];
int b[MAX][MAX];
int c[MAX][MAX];

void solve(){
	int n = read(), m = read();
	for(int i = 1; i <= n; i++)	for(int j = 1; j <= m; j++)	do{a[i][j] = getchar();}while(a[i][j] != 'W' and a[i][j] != 'B');
	for(int i = 1; i <= n; i++)	for(int j = 1; j <= m; j++)	if(a[i][j] == 'B')	b[i][j] = 1;
	int ans = 0;
	for(int i = 1; i <= n; i++)	for(int j = 1; j <= m; j++)	c[i][j] = (b[i][j] + b[i + 1][j] + b[i][j + 1] + b[i + 1][j + 1]) % 2, ans += c[i][j];
	for(int i = 1; i < n; i++)	for(int j = 1; j < m; j++)	if(c[i][j] and c[i][m] and c[n][j] and c[n][m])	write(ans - 1), endl, exit(0);
	write(ans), endl;
	return ;
}

signed main(){
	int t = 1;
	while(t--)	solve();
	return 0;
}
```

 </details>

  #### F2
   代价为 2 那么优秀了 2 代价。意味着可以忽略  $c_{n,m}$ 只在 $c_{i,j} = c_{n,j} = c_{i,m} = 1$ 的时候操作就更优！不难注意到这相当于一行一列只能操作一次。不难发现这是一个二分图最大匹配。可以做到你想做到的复杂度。

<details>
<summary> 点击查看代码 </summary>

```cpp
// Problem: Alice and Recoloring 2
// URL: https://www.luogu.com.cn/problem/CF1592F2
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
const int MAX = 1e3 + 10;

int psz = 2;

struct flow{
	struct node{
		int v, w, cp;
	}; vector <node> g[MAX ];
	
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
}; flow G;

int id[MAX], id2[MAX];

char a[MAX][MAX];
int b[MAX][MAX];
int c[MAX][MAX];

void solve(){
	int n = read(), m = read();
	int s = 1, t = 2;
	for(int i = 1; i < n; i++)	id[i] = ++psz;
	for(int i = 1; i < m; i++)	id2[i] = ++psz;
	for(int i = 1; i < n; i++)	G.add_edge(s, id[i], 1);
	for(int i = 1; i < m; i++)	G.add_edge(id2[i], t, 1);
	for(int i = 1; i <= n; i++)	for(int j = 1; j <= m; j++)	do{a[i][j] = getchar();}while(a[i][j] != 'W' and a[i][j] != 'B');
	for(int i = 1; i <= n; i++)	for(int j = 1; j <= m; j++)	if(a[i][j] == 'B')	b[i][j] = 1;
	for(int i = 1; i <= n; i++)	for(int j = 1; j <= m; j++)	c[i][j] = (b[i][j] + b[i + 1][j] + b[i][j + 1] + b[i + 1][j + 1]) % 2;
	for(int i = 1; i < n; i++)	for(int j = 1; j < m; j++)	if(c[i][j] and c[n][j] and c[i][m])	G.add_edge(id[i], id2[j], 1);
	int ans = 0;
	while(G.bfs(s, t)){for(int i = 1; i <= psz; i++){G.cur[i] = 0;}ans -= G.aug(s, inf, t);}
	c[n][m] ^= (-ans % 2);
	for(int i = 1; i <= n; i++)	for(int j = 1; j <= m; j++)	ans += c[i][j];
	write(ans), endl;
	return ;
}

signed main(){
	int t = 1;
	while(t--)	solve();
	return 0;
}
```

</details>

### CF1572C Paint
*2700

时光倒流答案加一转为 P4170 [CQOI2007] 涂色。题目条件非常特殊。打表观测转移点非常可疑，猜测一定是和开头颜色相同或者与结尾颜色相同的才优秀。过了。。。。。

<details>
<summary>点击查看代码  </summary>

```cpp
// Problem: Paint
// URL: https://www.luogu.com.cn/problem/CF1572C
// Writer: WRuperD
// Powered by CP Editor (https://cpeditor.org)
#include<bits/stdc++.h>
using namespace std;
#define int long long
inline int read(){int x=0,f=1;char ch=getchar();while(ch<'0'||ch>'9'){if(ch=='-')f=-1;ch=getchar();}while(ch>='0'&&ch<='9'){x=(x<<1)+(x<<3)+(ch^48);ch=getchar();}return x*f;}
inline void write(int x){if(x<0){x=~(x-1);putchar('-');}if(x>9)write(x/10);putchar(x%10+'0');}
#define put() putchar(' ')
#define endl puts("")
const int MAX = 3003;
const int inf = 1e18;
int f[MAX][MAX];
int a[MAX];
int cnt[MAX];
int nxt[MAX], lst[MAX];
void solve(){
	int n = read();
	for(int i = 1; i <= n; i++){
		a[i] = read();
		cnt[i] = 0;
	}
	for(int i = 1; i <= n; i++){
		lst[i] = cnt[a[i]];
		cnt[a[i]] = i;
	}
	for(int i = 1; i <= n; i++)	cnt[i] = n + 1;
	for(int i = n; i >= 1; i--){
		nxt[i] = cnt[a[i]];
		cnt[a[i]] = i;
	}
	for(int i = 1; i <= n; i++)	for(int j = i; j <= n; j++)	f[i][j] = inf;
	for(int i = 1; i <= n; i++){
		f[i][i] = 1;
	}
	for(int len = 2; len <= n; len++){
		for(int i = 1; i + len - 1 <= n; i++){
			int j = i + len - 1;
			if(a[i] == a[j]){
				f[i][j] = min(f[i+1][j], f[i][j-1]);
			}else{
				for(int k = i; k <= j; k = nxt[k]){
					f[i][j]  = min(f[i][j], f[i][k] + f[k+1][j]);
				}
				for(int k = j; k >= i; k = lst[k]){
					f[i][j]  = min(f[i][j], f[i][k] + f[k+1][j]);
				}
			}
		}
	}
	write(f[1][n] - 1), endl;
}

signed main(){
	int t = read();
	while(t--)	solve();
	return 0;
}
```

</details> 

### CF1572D Bridge Club
考研直觉的题目。可惜我没有。

首先这肯定是只能二分图最大匹配了。关键在于如何优化？

所以肯定是只能优化建图了。考虑保留最大的一些边，使得一定能找出一组大小为 $k$ 的匹配即可。

<details>
<summary> 点击查看代码 </summary>

```cpp
// Problem: Bridge Club
// URL: https://www.luogu.com.cn/problem/CF1572D
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
const int MAX2 = 12e5 + 10;
const int MAX = 4e4 + 10;
int psz = 2;
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
};  min_max_flow G;
unsigned id[MAX2];
unsigned a[MAX2];

void solve(){
	int n = read(), k = read();
	int s = 1, t = 2;
	int t3 = ++psz;
	for(int i = 0; i < (1ll << n); i++){
		a[i] = read();
	}
	priority_queue < pair<signed, pair<signed,signed>> > que;
		for(int i = 0; i < (1ll << n); i++){
		if(__builtin_popcount(i) % 2)	continue;
			for(int j = 0; j < n; j++){
				int u = i, v = (i ^ (1ll << j));
				que.push(make_pair(a[i] + a[i ^ (1ll << j)], make_pair(u, v)));
			} 
	}
	for(int i = 1; i <= k * (2 * n - 1); i++){
		if(que.empty())	break;
		auto u = que.top();
		que.pop();
		if(!id[u.second.first])	id[u.second.first] = ++psz;
		if(!id[u.second.second])	id[u.second.second] = ++psz;
		int l = id[u.second.first], r = id[u.second.second];
		G.add_edge(l, r, 1, (long long)(-u.first));
	}
	for(int i = 0; i < (1ll << n); i++){
		if(id[i]){
			if(__builtin_popcount(i) % 2){
				G.add_edge(id[i], t3, 1, 0);
			}else{
				G.add_edge(s, id[i], 1, 0);
			}
		}
	}
	G.add_edge(t3, t, k, 0);
	int ans = 0, cost = 0;
	while(G.bfs(s, t)){
		for(int i = 1; i <= psz; i++)	G.cur[i] = 0;
		ans += G.aug(s, inf, cost, t);
	}
	write(-cost), endl;
	
}

signed main(){
	int t = 1;
	while(t--)	solve();
	return 0;
}
```

</details>

### CF1103C Johnny Solving
*2700 古老简单套路题。

套路地，拎出这个图的一颗生成树，如果这颗生成树的直径 $\geq {n \over k}$ 那么就直接做完了。否则让我们来研究一下这棵树的性质。由于保证所以节点度数大于等于三所以我们考虑去研究度数为一的叶子结点的性质。直觉上，树高小，叶子节点个数比较多。不妨设有 $x$ 个叶子节点，那么基于这棵树直径则 ${n\over k}x > n$ 所以 $x > k$ 这也就意味着我们只需要在这些个叶子上面找环。叶子节点两个祖先，怎么找都能找到。

<details>
<summary> 点击查看代码 </summary>

```cpp
// Problem: Johnny Solving
// URL: https://www.luogu.com.cn/problem/CF1103C
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
const int MAX = 3e5 + 10;

vector <int> g[MAX];
vector <int> g2[MAX];
int dep[MAX];
int dis[MAX], pre[MAX];
int Fa[MAX];
vector <int> lf;

void dfs(int u, int fa){
	dep[u] = dep[fa] + 1;
	Fa[u] = fa;
	int cnt = 0;
	for(int v : g[u]){
		if(v == fa)	continue;
		if(dep[v])	continue;
		cnt++;
		dfs(v, u);
		g2[u].pb(v), g2[v].pb(u);
	}
	if(!cnt){
		lf.pb(u);
	}
}


void dfs2(int u, int fa){
	dis[u] = dis[fa] + 1;
	pre[u] = fa;
	int cnt = 0;
	for(int v : g2[u]){
		if(v == fa)	continue;
		dfs2(v, u);
	}
}

void solve(){
	int n = read(), m = read(), k = read();
	for(int i = 1; i <= m; i++){
		int u = read(), v = read();
		g[u].pb(v), g[v].pb(u);
	}
	dfs(1, 1);
	int mk = 0;
	for(int i = 1; i <= n; i++)	if(dep[i] > dep[mk])	mk = i;
	dfs2(mk, mk);
	int mk2 = 0;
	for(int i = 1; i <= n; i++)	if(dis[i] > dis[mk2])	mk2 = i;
	// write(mk2), put(), write(mk), endl;
	if(dis[mk2] >= (n + k - 1) / k){
		puts("PATH");
		vector <int> ans;
		while(mk2 != mk){
			ans.pb(mk2);
			mk2 = pre[mk2];
		}
		ans.pb(mk);
		write(ans.size()), endl;
		for(int u : ans)	write(u), put();
		endl;
		return ;
	}
	puts("CYCLES");
	int cnt = 0;
	for(int u : lf){
		bool fl = 0;
		for(int v : g[u]){
			if(v == Fa[u]){
				continue;
			}
			if((dep[u] - dep[v] + 1) % 3){
				fl = 1;
				write(dep[u] - dep[v] + 1), endl;
				int now = u;
				while(now != v){
					write(now), put();
					now = Fa[now];
				}
				write(v), endl;
				break;
			}
		}
		if(fl){
			cnt++;
			if(cnt == k)	break;
			continue;
		}
		for(int v : g[u]){
			if(v == Fa[u])	continue;
			for(int v2 : g[u]){
				if(v2 == Fa[u] or v2 == v)	continue;
				if(dep[v] < dep[v2])	swap(v,v2);
				write(dep[v] - dep[v2] + 2), endl;
				while(v != v2){
					write(v), put();
					v = Fa[v];
				}
				write(v), put(), write(u), endl;
				break;
			}
			break;
		}
		cnt++;
		if(cnt == k)	break;
	}
}

signed main(){
	int t = 1;
	while(t--)	solve();
	return 0;
}
```

</details>