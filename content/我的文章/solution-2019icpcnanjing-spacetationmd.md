---
title: solution-2019ICPCNanjing-Spacetation
date: 2024-10-01 19:23:34
tags:
  - 题解
  - ucup
published: true
hideInList: false
feature: 
isTop: false
---
## 题解：The 2019 ICPC Asia Nanjing Regional Contest，I. Space Station

模拟赛题目。

不难注意到 $a_i \leq 50$。所以事实上如果你不选 $a_i=0$ 的话只需要最多 $50$ 步就选完了。

所以考虑把所有 $a_i=0$ 的站点出去后（最后乘回来即可，对答案的贡献是固定的。）直接暴力搜索所有的状态。复杂度和 $50$ 的拆分数有关，所以不会太大。大概是在 $5*10^5$ 左右?

实现上的细节:

- 使用自然溢出可过。
- 建议用 unordered_map

```cpp
// Problem: I. Space Station
// URL: https://codeforces.com/gym/103466/problem/I
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

#define ull unsigned long long
const int base = 131, mod = 998244353;

int a[MAX];
int pre[MAX];
int cnt[MAX];

unordered_map <ull, int> mp;

inline int dfs(int x, int dep){
	// write(x), put(), write(dep), endl;
	if(!dep)	return 1;
	if(x >= 50)	return pre[dep];
	int ans = 0;
	ull now = 0;
	for(int i = 1; i <= 50; i++){
		now = (now * (ull)base + (ull)cnt[i]); 
	}
	if(mp.count(now))	return mp[now];
	for(int i = 1; i <= x; i++){
		if(cnt[i]){
			// puts("fk");
			cnt[i]--;
			ans = (ans + (cnt[i] + 1) * (dfs(x + i, dep - 1)) % mod)  % mod;
			cnt[i]++;
		}
	}
	mp[now] = ans;
	return ans;
}

void solve(){
	int n = read();
	pre[0] = 1;
	for(int i = 1; i <= n; i++)	pre[i] = pre[i - 1] * i % mod;
	for(int i = 0; i <= n; i++){
		a[i] = read();
		if(i)	cnt[a[i]]++;
	}
	int ans = dfs(a[0], n - cnt[0]);
	// write(ans), endl;
	for(int i = n; i >= n - cnt[0] + 1; i--)	ans = ans * i % mod;
	write(ans), endl;
}

signed main(){
	int t = 1;
	while(t--)	solve();
	return 0;
}
```