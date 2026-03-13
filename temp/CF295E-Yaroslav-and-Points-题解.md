---
title: CF295E Yaroslav and Points 题解
date: 2024-12-16 19:28:54
tags: 题解
categories:
    - [数据结构,分块]
description: CF295E，洛谷紫，CF 2500
---

{% folding cyan, 更新历史 %}

{% timeline 更新历史, blue %}
<!-- timeline 2024-12-16 -->
发布博客
<!-- endtimeline -->
{% endtimeline %}

{% endfolding %}

> 提供一种分块做法，跑得飞快。

>[洛谷传送门](https://www.luogu.com.cn/problem/CF295E)
>
>[CF 传送门](https://codeforces.com/problemset/problem/295/E)

# 题意

维护数轴上的 $n$ 个点，支持单点移动，以及查询一个值域区间内的所有点对距离和。

保证移动距离不超过 $1000$，且任意时刻任意两点不重合。

# 思路

对于查询，假设所有点已经排好序，则可以将值域区间转化成下标区间，显然可以拆贡献。

设值域区间转化成的下标区间为 $[l,r]$，则 $\sum\limits_{i=l}^{r} x_i\times (i-l) - \sum\limits_{i=l}^{r} x_i\times (r-i)$ 即为所求。

上面的式子还可以继续拆，$\sum\limits_{i=l}^{r} x_i\times (i-l) - \sum\limits_{i=l}^{r} x_i\times (r-i)=2\sum\limits_{i=l}^{r} x_i\times i - (l+r)\sum\limits_{i=l}^{r} x_i$，这启示我们可以使用数据结构维护 $x_i\times i$ 和 $x_i$ 的区间和。

这时候题意中的两点保证就有用了，他保证了在一次修改中，$x$ 数组被打乱的范围不会太大，只有 $1000$。我们完全可以对这个被打乱的区间内的所有数进行暴力修改。

统计一下修改次数和查询次数，发现共有 $10^5\times 1000$ 组单点修改，以及 $10^5$ 组区间查询，则 $O(1) - O(\sqrt{n})$ 的分块是一个很好的选择。

于是这题就做完了，代码非常好写。

# 代码

```cpp
#include <bits/stdc++.h>
using namespace std;
typedef long long ll;
const int len=256;
int th[100005],st[100005],ed[100005];
struct node{ // O(1)-O(sqrt(n)) 的单点改区间和板子
	ll a[100005],sum[100005];
	void change(int x,ll k){
		k-=a[x],a[x]+=k,sum[th[x]]+=k;
	}
	ll query(int l,int r){
		ll ans=0;
		if(th[l]==th[r]){
			for(int i=l;i<=r;i++) ans+=a[i];
			return ans;
		}
		for(int i=l;i<=ed[th[l]];i++) ans+=a[i];
		for(int i=st[th[r]];i<=r;i++) ans+=a[i];
		for(int i=th[l]+1;i<th[r];i++) ans+=sum[i];
		return ans;
	}
}s1,s2; // s1 存 x[i] 的区间和，s2 存 x[i]*i 的区间和
int n,ca,a[100005],c[100005];
int main(){
	ios::sync_with_stdio(0),cin.tie(0),cout.tie(0);
	cin>>n;
	for(int i=1;i<=n;i++){
		th[i]=(i-1)/len+1,ed[th[i]]=i;
		if(th[i]!=th[i-1]) st[th[i]]=i;
	}
	for(int i=1;i<=n;i++) cin>>a[i],c[i]=a[i]; // 由于输入原因要存输入顺序的 c 数组
	sort(a+1,a+n+1);
	for(int i=1;i<=n;i++) s1.change(i,a[i]),s2.change(i,1ll*a[i]*i); // 预处理
	cin>>ca;
	while(ca--){
		int op,x,y;
		cin>>op>>x>>y;
		if(op==1){
			int id=lower_bound(a+1,a+n+1,c[x])-a; // 原数组下标转 x 数组下标
			c[x]+=y,x=id,a[x]+=y;
			int l=x,r=x;
			if(y>0) while(r<n&&a[r]>a[r+1]) swap(a[r],a[r+1]),r++; // 求出发生改变的区间
			else while(l>1&&a[l]<a[l-1]) swap(a[l],a[l-1]),l--;
			for(int i=l;i<=r;i++) s1.change(i,a[i]),s2.change(i,1ll*a[i]*i); // 暴力修改区间
		}
		else{
			x=lower_bound(a+1,a+n+1,x)-a,y=upper_bound(a+1,a+n+1,y)-a-1; // 值域区间转下标区间
			if(x>y){cout<<"0\n";continue;}
			ll ans1=s1.query(x,y),ans2=s2.query(x,y); // ans1 是 x[i] 的区间和，ans2 是 x[i]*i 的区间和
			cout<<2*ans2-(x+y)*ans1<<"\n";
		}
	}
	return 0;
}
```