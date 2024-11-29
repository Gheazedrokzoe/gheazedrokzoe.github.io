---
title: '2024-10-02-solution-arc121d'
date: 2024-10-02 19:25:55
tags: [题解]
published: true
hideInList: false
feature: 
isTop: false
---
## [ARC121D] 1 or 2 题解

基本上会了。有趣的点在于转换。

首先你考虑每次只选 $2$ 个糖的答案。这个是好猜的，是 $\max a_i - a_{n-i+1} - \min a_i - a_{n-i+1}$。（ $a_i$ 有序）。

但是可以每次只选一个唐。这个有点棘手。随机想一想，发现只选一个糖相当于选那个唐和一个美味值为 $0$ 的。所以我们可以枚举加入多少个美味值为 $0$ 的糖果。复杂度 $O(n^2\log n)$。

```cpp
// Problem: [ARC121D] 1 or 2
// URL: https://www.luogu.com.cn/problem/AT_arc121_d
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

int a[MAX * 2];

int calc(int n){
	int minn = inf, maxn = -inf;
	for(int i = 1; i <= n / 2; i++){
		minn = min(minn, a[i] + a[n - i + 1]);
		maxn = max(maxn, a[i] + a[n - i + 1]);
	}
	if(n % 2) minn = min(minn, a[n / 2 + 1]), maxn = max(maxn, a[n / 2 + 1]);
	return maxn - minn;
}

void solve(){
	int n = read();
	for(int i = 1; i <= n; i++){
		a[i] = read();
	}	
	sort(a + 1, a + n + 1);
	int ans = calc(n);
	for(int i = 1; i <= n; i++){
		sort(a + 1, a + n + i + 1);
		ans = min(ans, calc(n + i));
	}
	write(ans), endl;
}

signed main(){
	int t = 1;
	while(t--)	solve();
	return 0;
}
```