---
title: 'The Darkside Of The Moon'
date: 2024-11-13 19:07:00
tags: []
published: true
hideInList: true
feature: /post-images/the-darkside-of-the-moon.bmp
isTop: false
---
联考题解。

## 11.18
破防了。gap 好大。

### arthur
 点边容斥即可。

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
void solve(){
	int n = read();
	int ans = 0;
	for(int i = 1; i <= n; i++)	ans += (n - i + 1) * i;
	for(int i = 1; i < n; i++){
		int u = read(), v = read();
		if(u > v)	swap(u, v);
		ans -= u * (n - v + 1);
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

### barry
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

## 11.22
睡了一上午。

### A，B
。。。。。

### C
不难发现原问题等价于出边纯色的点的数量。如果转换为区间加减求 0 的个数的话就输输输了。这是业界难题！正解是利用重链剖分的性质考虑只有重儿子和父亲边的这个贡献。对于不是这样的修改，暴力重构！很有前途，但是。

永不屈服，快跑！

### D 
一眼 CDQ 分治。