---
title: 2024-10-02-solution-arc122d
date: 2024-10-02 19:26:44
tags:
  - 题解
  - atcoder
  - arc
published: true
hideInList: false
feature: 
isTop: false
---
## [ARC122D] XOR Game 题解

弱智题，不知道怎么评的 *2300。

从高位往低位考虑，如果当前位数为 $1$ 的个数为奇数，那么显然答案在这一位为 $1$。相当于求这一位为 $1$ 和为 $2$ 的数两两的最小异或值。使用 01 Tire 解决。

如果为偶数那么直接递归求解。

```cpp
// Problem: [ARC122D] XOR Game
// URL: https://www.luogu.com.cn/problem/AT_arc122_d
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
const int MAX = 4e5 + 10;

int to[MAX * 40][2];
int psz;

int a[MAX];

void insert(int x){
	int now = 0;
	for(int i = 31; i >= 0; i--){
		if(to[now][(((1ll << i) & x) != 0)] == -1){
			to[now][(((1ll << i) & x) != 0)] = ++psz;
			now = psz;
			to[psz][0] = to[psz][1] = -1;
		}else{
			now = to[now][(((1ll << i) & x) != 0)];
		}
	}
}

int check(int x){
	int now = 0, ans = 0;
	for(int i = 31; i >= 0; i--){
		if(to[now][(((1ll << i) & x) != 0)] == -1){
			ans = ans + (1ll << i);
			now = to[now][(((1ll << i) & x) == 0)];
		}else{
			now = to[now][(((1ll << i) & x) != 0)];
		}
	}
	return ans;
}

int solve2(int l, int r, int x){
	if(x < 0)	return 0;
	int mid = 0;
	for(int i = l; i <= r; i++){
		if(!(a[i] & (1ll << x)))	mid = i;
	}
	if(!mid or mid == r){
		return solve2(l, r, x - 1);
	}
	if((r - mid) % 2 == 0){
		return max(solve2(l, mid, x - 1), solve2(mid + 1, r, x - 1));
	}
	psz = 0;
	to[0][1] = to[0][0] = -1;
	for(int i = mid + 1; i <= r; i++){
		insert(a[i]);
	}	
	int ans = inf;
	for(int i = l; i <= mid; i++){
		ans = min(ans, check(a[i]));
	}
	return ans;
}

void solve(){
	int n = read();
	for(int i = 1; i <= 2 * n; i++){
		a[i] = read();
	}
	sort(a + 1, a + 2 * n + 1);
	write(solve2(1, 2 * n, 31)), endl;;
}

signed main(){
	int t = 1;
	while(t--)	solve();
	return 0;
}
```