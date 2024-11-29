---
title: 'A Moon Shaped Pool'
date: 2024-10-30 15:58:53
tags: [题解,学习笔记]
published: true
hideInList: false
feature: /post-images/a-moon-shaped-pool.jpg
isTop: true
---

ucup 做题记录。

## The 3rd Universal Cup. Stage 15: Chengdu

### A. Arrow a Row
简单构造，吃了罚时，谢罪！
结尾必须是至少连续三个以上的 >,不然无解。然后从右往左依次填即可。

<details>
<summary>点击查看代码</summary>

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
void solve(){
	string s;
	cin >> s;
	int n = s.length();
	s = " " + s;
	if(s[1] == '-' or s[n] == '-'){
		puts("No");
		return ;
	}
	int cnt = 0;
	int mk = n + 1;
	for(int i = n; i >= 1; i--){
		if(s[i] == '-'){
			if(cnt)	{
				mk = i;
				break;
			}
			continue;
		}
		else{
			cnt++;
		}
	}
	if(cnt == n or cnt <= 2){
		puts("No");
		return ;
	}
	int mk2 = 0;
	for(int i = 1; i <= mk; i++){
		if(s[i] == '>'){
			mk2 = i;
			break;
		}
	}
	if(!mk2){
		puts("No");
		return ;
	}
	vector <pair<int,int>> ans;
	int mk3 = 0;
	for(int i = n - 2; i >= mk; i--){
		if(s[i] == '>' and s[i + 1] == '>' and s[i + 2] == '>'){
			ans.pb(make_pair(mk2, i + 2));
			mk3 = i + 2;
		}
	}
	for(int i = mk2 + 1; i <= mk; i++){
		if(s[i] == '>')	ans.pb(make_pair(i, mk3));
	}
	cout << "Yes ";
	write(ans.size()), endl;
	for(int i = 0; i < ans.size(); i++){
		write(ans[i].first), put(), write(ans[i].second - ans[i].first + 1), endl;
	}
	return ;
}

signed main(){
	int t = read();
	while(t--)	solve();
	return 0;
}
```
</details>

### B. Athlete Welcome Ceremony


## The 3rd Universal Cup. Stage 16: Nanjing
Also named as: GDFTS（校内选拔赛）。教练说我校有三个队的 Ec Final 打星名额。第一次打线下 XCPC 就是  Final。牛大了。但是我怎么这场会 0 道题。给队友磕头谢罪了！

### A. Hey, Have You Seen My Kangaroo?
只能说不愧是袋鼠题了。我个人心目中这场最好的题目。

首先是合并次数一定小于 $nm$。

考虑分类讨论，先考虑整 $k$ 轮后的情况。暴力枚举每一个袋鼠 $k$ 次操作后会在哪里，形成一颗外向基环树。所以第 $i$ 个位置上的袋鼠只会存在树高整轮。基环上的就会一直存在。不妨设第 $i$ 个袋鼠所代表的树高为 $h_i$。不妨设一直存在的袋鼠树高为正无穷。

然后你考虑模拟前 $k$ 轮操作，发现对于每一次有两个位置合并，这次合并操作一定只会在 $min(h_u,h_v)$ k 整轮中出现。合并后的 $h$ 取更大的值。 暴力记录下这些有贡献的时间（注意总数不超过 $nm$)。然后就可以知道所以的答案了！

<details>
<summary>点击查看代码</summary>

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
const int MAX = 2e5 + 10;

int n, m, k;

char a[MAX];
int b[MAX];

int g[MAX], h[MAX], deg[MAX];
int ret[MAX];

int to(int x, int y){
	return (x - 1) * m + y;
}

pair <int,int> to2(int x){
	return make_pair((x - 1) / m + 1, (x - 1) % m + 1);
}

bool chk(int x, int y){
	if(x <= 0 or y <= 0 or x > n or y > m or !b[to(x,y)])	return false;
	return true;
}

inline int nxt(int x, int y){
	pair <int,int> now = to2(x);
	if(a[y] == 'U'){
		now.first--;
	}
	if(a[y] == 'D'){
		now.first++;
	}
	if(a[y] == 'L'){
		now.second--;
	}
	if(a[y] == 'R'){
		now.second++;
	}
	if(chk(now.first, now.second)){
		return to(now.first, now.second);
	}
	return x;
}

int p[MAX];


void solve(){
	n = read(), m = read(), k = read();
	for(int i = 1; i <= k; i++){
		cin >> a[i];
	}
	for(int i = 1; i <= n * m; i++){
		char ch;
		cin >> ch;
		b[i] = ch - '0';
	}
	for(int i = 1; i <= n * m; i++){
		if(!b[i])	continue;
		int now = i;
		for(int j = 1; j <= k; j++){
			now = nxt(now, j);
		}
		g[i] = now;
		deg[now]++;
		// write(to2(i).first), put(), write(to2(i).second), putchar('-'), putchar('>');
		// write(to2(now).first), put(), write(to2(now).second), endl;
	}
	queue <int> que;
	for(int i = 1; i <= n * m; i++)	if(!deg[i] and b[i])	que.push(i);
	while(!que.empty()){
		int u = que.front();
		que.pop();
		deg[g[u]]--;
		h[u]++;
		h[g[u]] = max(h[g[u]], h[u]);
		if(!deg[g[u]])	que.push(g[u]);
	}
	for(int i = 1; i <= n * m; i++){
		if(deg[i]){
			h[i] = inf;
		}
	}
	vector <int> ans;
	vector <pair<int,int>> now;
	for(int i = 1; i <= n * m; i++)	if(b[i])	now.pb(make_pair(i,i));
	for(int i = 1; i <= k; i++){
		for(auto u : now){
			u.first = nxt(u.first, i);
			if(p[u.first]){
				int D = min(h[u.second], h[p[u.first]]);
				if(D == inf)	{
					// write(i), put();
					// endl;
					// puts("wtf?");
					continue;
				}
				for(int j = 1; j <= D; j++){
					ans.pb((j - 1) * k + i);
				}
				if(h[u.second] > h[p[u.first]]){
					p[u.first] = u.second;
				}
			}else{
				p[u.first] = u.second;
			}
		}
		now.clear();
		for(int j = 1; j <= n * m; j++){
			if(p[j]){
				now.pb(make_pair(j, p[j]));
			}
			p[j] = 0;
		}
	}	
	sort(ans.begin(), ans.end());
	int now2 = 0;
	for(int i = 1; i <= n * m; i++)	if(b[i]) now2++;
	for(int i = 0; i <= n * m; i++)	ret[i] = inf;
	ret[now2] = 0;
	for(int i = 0; i < ans.size(); i++){
		int mk = i;
		for(int j = i + 1; j < ans.size(); j++){
			if(ans[j] != ans[i])	break;
			mk = j;
		}
		now2 -= (mk - i + 1);
		ret[now2] = ans[i];
		i = mk;
	}
	for(int i = 1; i <= n * m; i++){
		ret[i] = min(ret[i], ret[i - 1]);
	}
	for(int i = 1; i <= n * m; i++){
		if(ret[i] == inf)	puts("-1");
		else write(ret[i]), endl;
	}
}

signed main(){
	int t = 1;
	while(t--)	solve();
	return 0;
}
```
</details>
我觉得考虑整 $k$ 轮操作这一点实在是太酷了。想不到啊！

### B. Birthday Gift
做了一个小时红温了。然后 dieselhuang 一眼秒了。磕头。

反转偶数位置的 0/1，发现变成了消除相邻的一对不一样的。然后就是一定能删到只剩一种数了！所以只跟数量有关!

<details>
<summary>点击查看代码</summary>

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
const int MAX = 2e5 + 10;

// int f[MAX][3][2];

void solve(){
	string s;
	cin >> s;
	int n = s.length();
	s = " " + s;
	int cnt = 0, cnt2 = 0, cnt3 = 0;
	for(int i = 1; i <= n; i++){
		if(i % 2){
			if(s[i] == '0')	cnt++;
			else if(s[i] == '1')	cnt2++;
			else cnt3++;
		}else{
			if(s[i] == '1')	cnt++;
			else if(s[i] == '0')	cnt2++;
			else cnt3++;
		}
	}
	// write(cnt), put(), write(cnt2), put(), write(cnt3), endl;
	int chk = abs(cnt - cnt2);
	if(chk >= cnt3){
		write(chk - cnt3), endl;
	}else{
		cnt3 -= chk;
		if(cnt3 % 2)	puts("1");
		else puts("0");
	}
}

signed main(){
	int t = read();
	while(t--)	solve();
	return 0;
}
```
</details>

### C. Topology
我不~~喜欢~~会做这种题目。

以下貌似是在复读官方题解。

设 $f_{i,j}$ 表示不考虑 $i$ 子树内拓扑序的情况下 $i$ 在第 $j$ 位的方案数。一个经典结论就是一颗树的拓扑序个数为 $n! \over \prod \limits_{i=1}^{n}siz_i$。所以求出 $f$ 即可得到答案。

状态数正确。考虑如何转移？

这里是从 $f_{fa,x} \to f_{u,y}$ 的转移。

有两个系数。一部分是你要去钦定 $u$ 子树内的节点在拓扑序内的位置。这部分系数为 $n-y \choose siz_u - 1$。另外一部分是钦定  $fa$ 子树中不在 $u$ 子树中的点的拓扑序数量（可以用是上面的公式算）。

然后就是一个前缀和的形式。

时间复杂度 $O(n^2)$。

### E. Left Shifting 3
 签到题。

最多左移 7 次。

<details>
<summary>点击查看代码</summary>

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
const int MAX = 2e5 + 10;

char s[MAX];

void solve(){
	int n = read(), k = read();
	for(int i = 1; i <= n; i++)	cin >> s[i];
	int ans = 0;
	for(int i = 1; i <= n - 6; i++){
		if(s[i] == 'n' and s[i + 1] == 'a' and s[i + 2] == 'n' and s[i + 3] == 'j' and s[i + 4] == 'i' and s[i + 5] == 'n' and s[i + 6] == 'g'){
			ans++;
		}
	}
	for(int j = 1; j <= min(k, 7ll); j++){
		for(int i = 1; i < n; i++){
			swap(s[i], s[i + 1]);
		}
		int cnt = 0;
		for(int i = 1; i <= n - 6; i++){
			if(s[i] == 'n' and s[i + 1] == 'a' and s[i + 2] == 'n' and s[i + 3] == 'j' and s[i + 4] == 'i' and s[i + 5] == 'n' and s[i + 6] == 'g'){
				cnt++;
			}
		}
		ans = max(ans, cnt);
	}
	write(ans), endl;
}

signed main(){
	int t = read();
	while(t--)	solve();
	return 0;
}
```
</details>


### G. Binary Tree
简单题。膜拜 yinhee 首杀。

拎出树的中心。如果度数为 0 或 1 那么直接问吧。否则问大小最大的两颗子树的根。不难发现问题至少会被缩小至 $n\over 2$ 解决。

### I. Bingo
 不会一点 poly.

 但是可以归约到给你 $n \cdot m$ 的方格选 $k$ 个格子涂黑要求每行每列必须至少有一个黑格子的方案数。需要知道 $k \leq 200000$ 的所有值。

 ### J. Social Media
 简单讨论一下就行了。

 ### K. Strips
 直接贪心然后调整一下。