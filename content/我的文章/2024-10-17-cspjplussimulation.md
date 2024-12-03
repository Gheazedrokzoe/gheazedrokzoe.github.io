---
title: '2024-10-17-cspjplussimulation'
date: 2024-10-17 19:06:29
tags: [题解]
published: true
hideInList: false
feature: 
isTop: false
---
# 学弟的 Div2 模拟赛 T4 题解

好题。

首先考虑怎么做到 $O(nq)$。要找到一种刻画方式来判断一组边是否使得给定点被染色。

我找到了这样一种方式：对于一条 $x_i < x_{i+1}$ 的边，如果给定点在他的上方，那么将其权值设为 1。对于一条 $x_i > x_{i+1}$ 的边，如果给定点在他的下方，那么将其权值设为 1。对于一条 $x_i = x_{i+1}$ 的边，这是细节，自己思考。当然如果在直线上那么权值也是 1。于是我们可以简单的发现有解当且仅当有两条对边权值均为 1。这样做显然是对的。


下面是一张题解中的图片。

![](https://WRuperD.github.io/post-images/1729163798070.bmp)

这样你就会 $O(nq)$ 了。由于是强制在线所以数据结构之类的基本就 GG 了。于是你考虑发掘更多的性质。~~如果你在调试的时候输出了答案序列~~ 或者仔细思考有~~有惊人的注意力~~你就会发现所有的权值为 1 的边编号是连续的。仔细想想~~自证不难~~非常有道理。所以你的答案序列就非常好看了。

接下来讲讲我的蠢笨做法。

分类讨论，如果答案序列首尾不相同，那么直接二分就好了！

如果首位相同，再 $\operatorname{check}$ 一下 $n/2+1$ 这个位置。如果和序列开头权值是相同的，那么如果全为 1 答案就是 Yes  否则就是 No（这个可以考虑存在答案长度）。如果不同的话，那么显然的就又变成了第一种情况，直接二分就好了！

做完了，时间复杂度 $O(n\log n)$。

**一定一定要注意 $x_i = x_{i+1}$ 这部分的细节。**

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
const int MAX = 1e5 + 10;

struct node{
	int x, y;
}; node a[MAX];

const double eps = 1e-10;

int n;

bool check(int i, int x, int y){
	int now = i, now2 = i + 1;
	if(i == n)	now2 = 1;
	bool fl = 0;
	if(a[now].x > a[now2].x){
		fl = 1;
	}else if(a[now].x == a[now2].x){
		int now3 = now - 1;
		if(now3 < 1){
			now3 = now2 + 1;	
			fl = (a[now2].x > a[now3].x);
			now = now2;
			now2 = now3;
		}else{
			fl = (a[now3].x > a[now].x);
			now2 = now;
			now = now3;
		}
		
	}
	double k = double(a[now].y - a[now2].y) / double(a[now].x - a[now2].x);
	double b = double(a[now].y) - k * double(a[now].x);
	if(abs(double(k) * x + b - y) <= eps)	return true;
	else if((double(k) * x + b <= y) ^ fl)	return true;
	else return false;
}

void solve(){
	n = read();
	for(int i = 1; i <= n; i++){
		a[i].x = read(), a[i].y = read();
	}
	int q = read();
	int pre = 0;
	while(q--){
		int x = read(), y = read();
		x = x ^ (pre * pre * pre);
		y = y ^ (pre * pre * pre);
		bool fl1 = check(1, x, y), fl2 = check(n, x, y);
		if(fl1 and !fl2){
			int l = 1, r = n;
			int ans = 1;
			while(l <= r){
				int mid = (l + r) >> 1;
				if(check(mid, x, y)){
					l = mid + 1;
					ans = mid;
				}else{
					r = mid - 1;
				}
			}
			if(ans > n / 2){
				puts("Yes");
				pre++;
			}else{
				puts("No");
			}
		}else if(!fl1 and fl2){
			int l = 1, r = n;
			int ans = 1;
			while(l <= r){
				int mid = (l + r) >> 1;
				if(!check(mid, x, y)){
					l = mid + 1;
					ans = mid;
				}else{
					r = mid - 1;
				}
			}
			if(ans < n / 2){
				puts("Yes");
				pre++;
			}else{
				puts("No");
			}
		}else{
			bool fl3 = check(n / 2 + 1, x, y);
			if(fl3 == fl1){
				if(fl1){
					puts("Yes");
					pre++;
				}else{
					puts("No");
				}
			}else{
				int l = n / 2 + 1, r = n, ansr = n / 2 + 1;
				while(l <= r){
					int mid = (l + r) >> 1;
					if(check(mid, x, y) == fl3){
						l = mid + 1, ansr = mid;
					}else{
						r = mid - 1;
					}
				}
				l = 1, r = n / 2 + 1;
				int ansl = n / 2 + 1;
				while(l <= r){
					int mid = (l + r) >> 1;
					if(check(mid, x, y) == fl3){
						ansl = mid;
						r = mid - 1;
					}else{
						l = mid + 1;
					}
				}
				if(fl3){
					if((ansr - ansl + 1) > n / 2){
						puts("Yes");
						pre++;
					}else{
						puts("No");
					}
				}else{
					if((ansr - ansl + 1) < n / 2){
						puts("Yes");
						pre++;
					}else{
						puts("No");
					}
				}
			}
		}
	}
}

signed main(){
	int t = 1;
	while(t--)	solve();
	return 0;
}
```