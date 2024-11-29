---
title: 'Public NOIP Round #8 (Div. 1, 提高) 题解'
date: 2024-11-23 14:36:41
tags: []
published: true
hideInList: false
feature: 
isTop: false
---
膜拜 yhm.

### A
最神秘的一集。讲讲我的垃圾做法。

看到 ![](https://WRuperD.github.io/post-images/1732344792867.PNG) 还题面就告诉你要 bitset,时限还是两秒钟，出题人直接演都不带演的，肯定是要你去疯狂的平衡一些东西然后套上 bitset 优化！

### $m \leq 200$
这个就直接对于每一位去看前缀和就可以了，时间复杂度 $O(nm+mk)$。

```cpp
namespace subtask1{
	int s[MAX];
	void solve(){
		for(int i = 1; i <= n; i++){
			for(int j = 1; j <= m; j++){
				if(a[to(i, j)])	s[to(i, j)] = 1;
				if(i == 1)	continue;
				s[to(i, j)] += s[to(i - 1, j)];
			}
		}
		int k = read();
		int X = read(), Y = read(), Z = read();
		int l = 1, r = n;
		int A = 1, B = n;
		int ret = 0;
		while(k--){
			int ans = 0;
			for(int i = 1; i <= m; i++){
				int cnt = s[to(r, i)] - s[to(l - 1, i)];
				if(cnt == 0 or cnt == r - l + 1){
					ans++;
				}
			}
			ret += ans;
			A = (A * X + ans * Y + Z) % n + 1;
			B = (B * Y + ans * Z + X) % n + 1;
			l = min(A, B), r = max(A, B);
		}
		write(ret), endl;
	}
}
```

#### $n \leq 400$
这个也不难。有用的询问只有至多 $n^2$ 个所以 $k$ 看着大实际小。那么你直接搞一个 $O({n^2m\over \omega})$ 的 bitset 优化就搞完

``` cpp
namespace subtask2{
	bitset <100001> bt[405];
	bitset <100001> bt2[405];
	
	int ans[405][405];
	
	void solve(){
		for(int i = 1; i <= n; i++){
			for(int j = 1; j <= m; j++){
				bt[i][j] = a[to(i, j)];
				bt2[i][j] = (a[to(i, j)] ^ 1);
			}
		}
		for(int i = 1; i <= n; i++){
			bitset <100001> now;
			for(int j = i; j >= 1; j--){
				now = now | bt[j];
				ans[j][i] += (m - now.count());
			}
		}
		for(int i = 1; i <= n; i++){
			bitset <100001> now;
			for(int j = i; j >= 1; j--){
				now = now | bt2[j];
				ans[j][i] += (m - now.count());
			}
		}
		int k = read();
		int X = read(), Y = read(), Z = read();
		int l = 1, r = n;
		int A = 1, B = n;
		int ret = 0;
		while(k--){
			int Ans = ans[l][r];
			ret += Ans;
			// write(l), put(), write(r), put(), write(Ans), endl;
			A = (A * X + Ans * Y + Z) % n + 1;
			B = (B * Y + Ans * Z + X) % n + 1;
			l = min(A, B), r = max(A, B);
		}
		write(ret), endl;
	}
}
```

#### $n,m \leq 5000$
这下 $n,m$ 都不太大了。那你直接搞一个 st 表套 bitset 优化一下。搞搞搞，直接就做到了 $O({n \log n m \over \omega})$ 的复杂度。同时这时候空间也不会超。

``` cpp
namespace subtask3{
	const int MAX2 = 5e3 + 10;
	bitset <2505> f[14][MAX2];
	bitset <2505> f2[14][MAX2];
	int lg2[MAX];
	
	void solve(){
		for(int i = 1; i <= n; i++){
			for(int j = 1; j <= m; j++){
				f[0][i][j] = a[to(i, j)];
				f2[0][i][j] = (a[to(i, j)] ^ 1);
			}
		}
		for(int i = 1; i < 14; i++){
			for(int j = 1; j + (1ll << i) - 1 <= n; j++){
				f[i][j] = f[i - 1][j] | f[i - 1][j + (1ll << (i - 1))];
			}
		}
		for(int i = 1; i < 14; i++){
			for(int j = 1; j + (1ll << i) - 1 <= n; j++){
				f2[i][j] = f2[i - 1][j] | f2[i - 1][j + (1ll << (i - 1))];
			}
		}
		for(int i = 2; i <= n; i++){
			lg2[i] = lg2[i >> 1] + 1;
		}
		int k = read();
		int X = read(), Y = read(), Z = read();
		int l = 1, r = n;
		int A = 1, B = n;
		int ret = 0;
		while(k--){
			int Ans = 0;
			int len = (r - l + 1);
			len = lg2[len];
			Ans += m - (f[len][l] | f[len][r - (1ll << len) + 1]).count();
			Ans += m - (f2[len][l] | f2[len][r - (1ll << len) + 1]).count();
			// int Ans = ans[l][r];
			ret += Ans;
			A = (A * X + Ans * Y + Z) % n + 1;
			B = (B * Y + Ans * Z + X) % n + 1;
			l = min(A, B), r = max(A, B);
		}
		write(ret), endl;
	}
}
```

然后你就成功在 T1 上面浪费了 1h。


