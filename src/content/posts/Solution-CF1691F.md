---
title: 'CF1691F K-Set Tree 题解'
published: 2024-10-24
description: ' '
series: 'Solution'
tags: ['数学','组合数学']
---

> 提供一种不用 dp 的做法，跑得飞快。

>[洛谷传送门](https://www.luogu.com.cn/problem/CF1691F)
>
>[CF 传送门](https://codeforces.com/problemset/problem/1691/F)

## 题意

给定一棵有 $n$ 个点的树及正整数 $k$，定义 $F(u,S) $为以 $u$ 为根时包含集合 $S$ 中所有点的最小子树的大小，求 $\sum_{u=1}^{n}\sum_{|S|=k}F(u,S)$。

## 思路

先考虑对于一个给定的集合 $S$，有没有什么简单的式子可以表示$\sum_{u=1}^n F(u,S)$。

我们称这样的 $u$ 为关键点：$u\in S$，或者以 $u$ 为根时，至少有两棵子树里有 $\in S$ 的点。显然若 $u$ 为关键点，则 $F(u,S)=n$。如在样例 2 中，若 $S=\{3,7\}$，则点 $3,2,4,7$ 为关键点。

对于关键点之外的点，设它们形成了 $m$ 个连通块，第 $i$ 个连通块大小为 $sz_i$。若 $u$ 在第 $i$ 个连通块中，则 $F(u,S)=n-sz_i$。

考虑所有点的 $F$ 值之和：

- 共有 $n-\sum sz_i$ 个关键点，每个关键点的 $F$ 值为 $n$，共为 $n\times (n-\sum sz_i)=n^2-n\sum sz_i$；
- 第 $i$ 个连通块有 $sz_i$ 个点，每个点的 $F$ 值为 $n-sz_i$，共为 $\sum sz_i\times(n-sz_i)=n\sum sz_i-\sum sz_i^2$；
- 所有点的 $F$ 值之和为 $n^2-n\sum sz_i+n\sum sz_i-\sum sz_i^2=n^2-\sum sz_i^2$。

故问题转化为求每种 $S$ 下的 $\sum sz_i^2$ 之和。发现连通块一共只有 $2(n-1)$ 个（每条树边两侧的连通块），考虑每个连通块的贡献。

先计算以 $u$ 为根的子树作为连通块的次数。对于一种 $S$，这棵子树被作为连通块当且仅当 $fa_u$ 是关键点。记 $SZ_u$ 为以 $1$ 为根时 $u$ 子树的大小。此时有两种情况：

- $fa_u\in S$：此时共有 $\dbinom{n-SZ_u-1}{k-1}$ 种情况；
- $fa_u\not\in S$：此时共有 $\dbinom{n-SZ_u-1}{k}-{\large\sum\limits_{v\in son_{fa_u},v\not=u}}\dbinom{SZ_v}{k}-\dbinom{n-SZ_{fa_u}-1}{k}$ 种情况，即在去掉 $u$ 子树和 $fa_u$ 后的点中取 $k$ 个，然后去掉 $fa_u$ 不是关键点的情况。

以 「全部点去掉 $u$ 子树」作为连通块的次数与上同理。

然后即可算出答案。

注意：可以先预处理出 $p_u={\large\sum\limits_{v\in son_{u}}}\dbinom{SZ_v}{k}$，否则在前一种情况中多次访问父亲的儿子，会被菊花图卡掉。

## 代码

```cpp
#include <bits/stdc++.h>
using namespace std;
typedef long long ll;
const ll mod=1e9+7;
const int N=2e5+5;
ll ksm(ll u,ll v=mod-2){ll tmp=1;u%=mod;while(v) tmp=tmp*((v&1)?u:1)%mod,u=u*u%mod,v>>=1;return tmp;}
ll jie[N],inv[N];
void init(int n=N-3){
	jie[0]=1;
	for(int i=1;i<=n;i++) jie[i]=1ll*jie[i-1]*i%mod;
	inv[n]=ksm(jie[n],mod-2);
	for(int i=n-1;i>=0;i--) inv[i]=1ll*inv[i+1]*(i+1)%mod;
}
ll C(ll u,ll v){
	if(v<0||v>u) return 0;
	return 1ll*jie[u]*inv[v]%mod*inv[u-v]%mod;
}
//---------------------------------------------预处理组合数
int head[N],tot;
struct EDGE{
	int nex,to;
} e[N<<1];
void add(int u,int v){
	e[++tot].nex=head[u],e[tot].to=v;
	head[u]=tot;
}
ll n,k,SZ[N],fa[N],p[N];
ll ans=0;
void dfs(int u,int fat){ //预处理 SZ,p,fa 数组
	SZ[u]=1,fa[u]=fat;
	for(int i=head[u];i;i=e[i].nex){
		int v=e[i].to;
		if(v==fat) continue;
		dfs(v,u);
		SZ[u]+=SZ[v];
		p[u]=(p[u]+C(SZ[v],k))%mod;
	}
}
int main(){
	ios::sync_with_stdio(false); cin.tie(nullptr); cout.tie(nullptr);
	init();
	cin>>n>>k;
	for(int i=1,u,v;i<n;i++){
		cin>>u>>v;
		add(u,v),add(v,u);
	}
	dfs(1,0);
	ll num;
	for(int u=2;u<=n;u++){
		num=C(n-SZ[u]-1,k-1)+C(n-SZ[u]-1,k)-(p[fa[u]]-C(SZ[u],k))-C(n-SZ[fa[u]],k);
		num%=mod;
		ans=(ans+SZ[u]*SZ[u]%mod*num)%mod; //第一种连通块

		num=C(SZ[u]-1,k-1)+C(SZ[u]-1,k)-p[u];
		num%=mod;
		ans=(ans+(n-SZ[u])*(n-SZ[u])%mod*num)%mod; //第二种连通块
	}
	cout<<(n*n%mod*C(n,k)%mod-ans+mod)%mod<<"\n"; //计算答案
	return 0;
}
```