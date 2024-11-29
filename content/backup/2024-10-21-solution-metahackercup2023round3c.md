---
title: '2024-10-21-solution-metahackercup2023round3C'
date: 2024-10-21 16:56:52
tags: []
published: true
hideInList: false
feature: 
isTop: false
---
# 2023Meta Hacker Cup Problem C: Krab-otage 题解
大粪题。但是思路还是比较清新的。

![](https://WRuperD.github.io/post-images/1729501543058.bmp)

上图中，红色格子代表删掉的格子。那么最终答案一定是某一个 $(1,1)$ 到蓝色格子的路径最大值加上某一个绿色格子到 $(r,c)$ 路径的最大值。

于是我们考虑动态规划转移红色位置，转移形式大致是枚举拐角处，然后把所有已经确定是蓝色格子和已经确定是绿色格子的贡献加入答案。分类讨论亿下就行了，很恶心。

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
const int MAX = 1e6 + 10;
const int mod = 998244353;
int quickPower(int a,int b,int p){int base=a,ans=1;while(b){if(b&1)ans*=base,ans%=p;base*=base;base%=p;b>>=1;}return ans;}

int a[MAX];
int prea[MAX];
int cnt0[MAX], cnt1[MAX], cnt2[MAX], cnt3[MAX];
int ans;

void dfs(int l, int r, int x){
	if(x > r){
		bool fl = 0;
		for(int i = l; i <= r; i++){
			for(int j = i + 1; j <= r; j++){
				for(int k = j + 2; k <= r; k++){
					if(a[i] == a[j] or a[i] == a[k] or a[j] == a[k])	continue;
					fl = 1;
					break;
				}
			}
		}
		if(fl)	return ;
		for(int i = l; i <= r; i++){
			for(int j = i + 2; j <= r; j++){
				for(int k = j + 1; k <= r; k++){
					if(a[i] == a[j] or a[i] == a[k] or a[j] == a[k])	continue;
					fl = 1;
					break;
				}
			}
		}
		if(fl)	return ;
		ans++;
		return ;
	}
	if(a[x] == 1)	dfs(l, r, x + 1);
	else if(a[x] == 2)	dfs(l, r, x + 1);
	else if(a[x] == 3)	dfs(l, r, x + 1);
	else{
		a[x] = 1;
		dfs(l, r, x + 1);
		a[x] = 2;
		dfs(l, r, x + 1);
		a[x] = 3;
		dfs(l, r, x + 1);
		a[x] = 0;
	}
}

void solve2(int l, int r){
	ans = 0;
	dfs(l, r, l);
	write(ans), endl;
}

void solve(){
	freopen("sub.in", "r", stdin);
	freopen("sub.out", "w", stdout);
	int n = read(), q = read();
	for(int i = 1; i <= n; i++){
		a[i] = read();
		cnt0[i] = cnt0[i - 1] + (a[i] == 0);
		cnt1[i] = cnt1[i - 1] + (a[i] == 1);
		cnt2[i] = cnt2[i - 1] + (a[i] == 2);
		cnt3[i] = cnt3[i - 1] + (a[i] == 3);
	}
	while(q--){
		int l = read(), r = read();
		if(r - l + 1 <= 4){
			solve2(l, r);
			continue;
		}
		int A = cnt1[r] - cnt1[l - 1], B = cnt2[r] - cnt2[l - 1], C = cnt3[r] - cnt3[l - 1], D = cnt0[r] - cnt0[l - 1];
		int cnt = 0;
		if(A)	cnt++;
		if(B)	cnt++;
		if(C)	cnt++;
		if(cnt == 3){
			puts("0");
			continue;
		}
		if(cnt == 2){
			write(quickPower(2, D, mod)), endl;
		}else if(cnt == 1){
			write((quickPower(2, D + 1, mod) - 1 + mod) % mod), endl;
		}else{
			write((3 * quickPower(2, D, mod) - 3 + mod) % mod), endl;
		}
	}
}

signed main(){
	int t = 1;
	while(t--)	solve();
	return 0;
}
```