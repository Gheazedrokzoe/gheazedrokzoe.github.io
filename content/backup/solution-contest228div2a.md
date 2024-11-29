---
title: 'solution-contest2.28(div2)A'
date: 2024-02-28 15:34:15
tags: [题解]
published: true
hideInList: false
feature: 
isTop: false
---
典但是好的构造题。（除了在卡空间和输出格式这两点上。）

观察一下发现每一个区间的答案只和其中的 0、1 数量奇偶有关。考虑当前区间推向下一个区间答案是怎么变化的，发现之和 $i \oplus i + k$ 有关。首先，如果 $2 \nmid n$ 那么显然无解。不然我们考虑构造出所有 $i$ 和 $i+k$ 不相等那么就全对了。  考虑经典结论，$i$ 跳到 $(i+k) \mod n$ 一定会形成若干个长度为 $\gcd(n,k)$ 的环。如果环长为奇数（特殊性质）那直接染就完了。否则手玩一下，感觉让一个环最开始两个数重是很优的，要是这样然还不行，那就真无解了。

```cpp
#include<bits/stdc++.h>
using namespace std;
const long long inf = 1e18;
const int mininf = 1e9 + 7;
#define pb emplace_back
inline int read(){int x=0,f=1;char ch=getchar();while(ch<'0'||ch>'9'){if(ch=='-')f=-1;ch=getchar();}while(ch>='0'&&ch<='9'){x=(x<<1)+(x<<3)+(ch^48);ch=getchar();}return x*f;}
inline void write(int x){if(x<0){x=~(x-1);putchar('-');}if(x>9)write(x/10);putchar(x%10+'0');}
#define put() putchar(' ')
#define endl puts("")
const int MAX = 8488609;

bool a[MAX];

void solve(){
	 int n = read(), k = read();
	 if(n % 2)	puts("-30"), exit(0);
	 int pre = 0;
	 bool fl = 0;
	 for(int i = 0; i < __gcd(n, k); i++){
 		int now = i;
 		bool cur = 1;
 		while(1){
 			a[now] = cur;
 			int to = (now + k) % n;
 			if(to == i){
 				if(a[to] == (cur ^ 1)){
 					break;
 				}else{
 					fl = 1;
 					if(pre <= 0){
 						pre++;
 						break;
 					}else{
 						pre--;
 						a[to] ^= 1;
 						break;
 					}
 				}
 				if(a[to] != (cur ^ 1)){ 
 					puts("-30");
 					return ;
 				}
 				break; 
 			}else{
 				now = to;
 				cur ^= 1;
 			}
 		}
	 }
	if(n == k or (n % 4 != 0 and k % 2 == 0)){
		puts("-30");
		return ;
	}
		
 	 for(int i = 0; i < n; i++)	write(a[i]);
 	endl;
	
}

signed main(){
	int t = 1;
	while(t--)	solve();
	return 0;
}
```