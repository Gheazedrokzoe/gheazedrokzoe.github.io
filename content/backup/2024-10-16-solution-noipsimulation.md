---
title: '2024-10-16-solution-noipsimulation'
date: 2024-10-16 19:48:28
tags: [题解]
published: true
hideInList: false
feature: 
isTop: false
---
# 联考题目一道 题解

直接来吧！

首先我们有式子 $f_{i,j} = \min(f_{i+1,j},a_i+\max(f_{i+1,j-1},b_i))$ 其中 $b_i$ 递减。

设 $t$ 表示最大的 $j$ 使得 $f_{i,j-1} \leq b_i$。

当 $j < t$ 则

$$
\begin{aligned} 
f_{i,j} &= \min(f_{i+1,j},a_i+b_i) \\
f_{i,j} &= f_{i+1,j}
\end{aligned}
$$

非常好的性质。而且这个 $b_i$ 反过来 $\operatorname{dp}$ 的话就是递增的，而 $f_{i,j} \geq f_{i+1,j}$ 所以 $t$ 也是递增的。

剩下部分的形式为 ：

$$f_{i,j} = \min(f_{i+1,j},a_i+f_{i+1,j-1})$$

这里为了形式同意不妨令 $f_{i + 1,t-1} = b_i$。

然后就是这部分里面 $f_{i,j}$ 显然是满足凸性的。所以考虑类似与 slope trick。维护上凸壳，凸壳上的每一个点为 $(j,f_{i,j})$。那么我们找到第一个满足选择后面那个转移的点，相当于增加一条斜率为 $a_i$ 的边，然后剩下部分整体平移。这可以直接优先队列做到。感觉非常非常的妙啊。

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
const int MAX = 5e3 + 10;

struct node{
	int a, b;
}; node a[MAX];

bool cmp(node x, node y){
	return x.b > y.b; 
}

int n;
int f[MAX];

void solve(){
	n = read();
	for(int i = 1; i <= n; i++){
		a[i].a = read(), a[i].b = read();
	}	
	sort(a + 1, a + n + 1, cmp);
	for(int i = 1; i <= n; i++)	f[i] = inf;
	f[0] = 0;
	priority_queue <int, vector <int>, greater<int>> que;
	que.push(inf);
	int now = 0, t = 0;
	for(int i = n; i >= 1; i--){
		while(que.top() + now <= a[i].b){
			now = now + que.top();
			que.pop();
			f[++t] = now;
		}
		if(que.top() != inf){
			int u = que.top();
			que.pop();
			que.push(u + now - a[i].b);
		}
		que.push(a[i].a), now = a[i].b;
	}
	while(t < n){
		now = now + que.top();
		que.pop();
		f[++t] = now;
	}
	for(int i = 1; i <= n; i++){
		write(f[i]), put();
	}endl;
}

signed main(){
	freopen("chef.in", "r", stdin);
	freopen("chef.out", "w", stdout);
	int t = 1;
	while(t--)	solve();
	return 0;
}
```