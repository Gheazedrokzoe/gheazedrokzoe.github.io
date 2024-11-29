---
title: '2024-10-16-solution-qoj8781'
date: 2024-10-16 19:16:58
tags: [题解]
published: true
hideInList: false
feature: 
isTop: false
---
# The 3rd Universal Cup. Stage 1: St. Petersburg Problem A Element-Wise Comparison 题解

orz yhm.

记录一下这个 Trick。首先这个数据范围就很可以 bitset 乱搞。所以你考虑对于每个位置求出有哪些位置 $a_j > a_i$ 然后是一个区间异或。

有一个简单的方式是注意到如果 $m$ 是固定的话，那么我们可以对序列分块，每 $m,$ 个一组。这样子一个区间异或必定跨过1-2个区间。那么我们直接区间前后缀异或就行了。我觉得是很巧妙的。

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
const int MAX = 5e4 + 10;

bitset <MAX> f[MAX], suf[MAX], nxt[MAX];
int a[MAX], pre[MAX];

void solve(){
	int n = read(), m = read();
	for(int i = 1; i <= n; i++){
		a[i] = read();
		pre[a[i]] = i;
	}
	for(int i = n - 1; i >= 1; i--){		
		f[pre[i]] = f[pre[i + 1]];
		f[pre[i]][pre[i + 1] - 1] = 1;
	}
	// for(int i = 1; i <= n; i++){
		// for(int j = 0; j < n; j++){
			// write(f[i][j]), put();
		// }endl;
	// }
	// endl;
	
	for(int i = 1; i <= n; i++){
		f[i] = (f[i] >> (i));
	}
	// for(int i = 1; i <= n; i++){
		// for(int j = 0; j < n; j++){
			// write(f[i][j]), put();
		// }endl;
	// }
	// endl;
	
	for(int i = 1; i <= n; i++){
		if(i % m == 0)	suf[i] = f[i];
		else suf[i] = (f[i] & suf[i - 1]);
	}
	
	// 0111
	// 1100
	// 0011
	// 1100
	// 0001
	
	for(int i = n; i >= 1; i--){
		if(i % m == 0)	nxt[i] = f[i];
		else nxt[i] = (f[i] & nxt[i + 1]);
	}
	int ans = 0;
	for(int i = 1; i + m - 1 <= n; i++){
		ans += (nxt[i] & suf[i + m - 1]).count();
	}
	write(ans), endl;
}

signed main(){
	int t = 1;
	while(t--)	solve();
	return 0;
}
```