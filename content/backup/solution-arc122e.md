---
title: 'solution-arc122e'
date: 2024-10-01 19:25:01
tags: [题解]
published: true
hideInList: false
feature: 
isTop: false
---
## [ARC122E] Increasing LCMs 题解

构造题都太神秘了，不看题解都不会做。bonus:抽时间阅读 https://www.luogu.com/article/o9mj50m1

考虑往一个已知序列里加入最后一个数 $a_i$ 需要满足什么条件?发现有 $\operatorname{lcm} \limits_j a_j < \operatorname{lcm} \limits_{j \ne i} a_j$。

显然如果我们从最后一个数开始确定，那前面那个值是确定的。做完了。

当然直接算 $\operatorname{lcm}$ 是非常蠢的。正确的做法是算 $\operatorname{gcd} (a_j,a_i)$ 的 $\operatorname{lcm}$ 与 $a_i$ 的大小关系。

参考题解：[link](https://www.luogu.com.cn/article/7xay01y9).

```cpp
// Problem: [ARC122E] Increasing LCMs
// URL: https://www.luogu.com.cn/problem/AT_arc122_e
// Writer: WRuperD
// Powered by CP Editor (https://cpeditor.org)

#include<bits/stdc++.h>
using namespace std;
const long long inf = 1e18;
const int mininf = 1e9 + 7;
#define int __int128
#define pb emplace_back
inline int read(){int x=0,f=1;char ch=getchar();while(ch<'0'||ch>'9'){if(ch=='-')f=-1;ch=getchar();}while(ch>='0'&&ch<='9'){x=(x<<1)+(x<<3)+(ch^48);ch=getchar();}return x*f;}
inline void write(int x){if(x<0){x=~(x-1);putchar('-');}if(x>9)write(x/10);putchar(x%10+'0');}
#define put() putchar(' ')
#define endl puts("")
const int MAX = 105;
int a[MAX];
int ret[MAX];
bool vis[MAX];

inline int lcm(int x, int y){
	return x * y / __gcd(x, y);
}

void solve(){
	int n = read();
	for(int i = 1; i <= n; i++){
		a[i] = read();
	}
	for(int i = n; i >= 1; i--){
		bool fl = 1; 
		for(int j = 1; j <= n; j++){
			if(vis[j])	continue;
			int now = 1;
			for(int k = 1; k <= n; k++){
				if(vis[k] or k == j)	continue;
				now = lcm(now, __gcd(a[k], a[j]));
			}
			if(now != a[j]){
				vis[j] = 1;
				ret[i] = a[j];
				fl = 0;
				break;
			}
		}
		if(fl){
			puts("No");
			return ;
		}
	}
	puts("Yes");	
	for(int i = 1; i <= n; i++){
		write(ret[i]), put();
	}
	endl;
}

signed main(){
	int t = 1;
	while(t--)	solve();
	return 0;
}
```