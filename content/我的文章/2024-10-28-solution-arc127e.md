---
title: 2024-10-28-solution-arc127e
date: 2024-10-28 10:00:13
tags:
  - 题解
  - atcoder
  - arc
published: true
hideInList: false
feature: 
isTop: false
---
# [ARC127E] Priority Queue 题解

感觉非常的高妙啊。

大致就是不妨钦定最后剩下的序列有序。

后面再补。

```cpp
// Problem: [ARC127E] Priority Queue
// URL: https://www.luogu.com.cn/problem/AT_arc127_e
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
const int MAX = 5e3 + 10;
const int mod = 998244353;

int a[MAX];
int f[MAX][MAX];

void solve(){
	int n = read(), m = read();
	int n2 = 0, now = 0;
	for(int i = 1; i <= n + m; i++){
		int op = read();
		if(op == 1)	a[++n2] = ++now;
		else n2--;
	}
	for(int i = 0; i <= now; i++)	f[0][i] = 1;
	for(int i = 1; i <= n2; i++){
		for(int j = 1; j <= a[i]; j++){
			f[i][j] = (f[i - 1][j - 1] + f[i][j - 1]) % mod;
		}
		for(int j = a[i] + 1; j <= now; j++)	f[i][j] = f[i][a[i]];
	}
	write(f[n2][a[n2]]), endl;
}

signed main(){
	int t = 1;
	while(t--)	solve();
	return 0;
}
```