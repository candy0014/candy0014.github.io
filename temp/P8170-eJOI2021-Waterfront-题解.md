---
title: P8170 [eJOI2021] Waterfront 题解
date: 2024-12-04 19:45:41
tags: 题解
categories:
    - [算法基础,贪心]
    - [算法基础,二分]
description: P8170，洛谷蓝
---

{% folding cyan, 更新历史 %}

{% timeline 更新历史, blue %}
<!-- timeline 2024-12-04 -->
发布博客
<!-- endtimeline -->
{% endtimeline %}

{% endfolding %}

> [洛谷传送门](https://www.luogu.com.cn/problem/P8170)

## 题意

给定两个长为 $n$ 的序列 $a,b$ 及整数 $m,k,x$，定义一轮操作为：对于所有 $a_i\gets a_i+b_i (1\le i\le n)$，然后进行至多 $k$ 次「选择一个 $i$ 满足 $a_i\ge x$，令 $a_i\gets a_i-x$」。最小化 $m$ 轮操作后 $a$ 中的最大值。

## 思路

最小化最大值，容易想到二分答案，于是设问题变为判断答案不大于 $g$ 是否可行。

容易知道答案固定时每个下标的操作总次数是确定的，设 $i$ 下标被操作 $c_i$ 次。

我们对每个下标 $i$，枚举 $1\le j\le c_i$，计算 $a_i$ 的第 $j$ 次操作至少要几轮操作后才可以进行，将其记为 $pos_{i,j}$。

于是原问题变为了：对于每对 $i,j$，都要在 $pos_{i,j}$ 至 $m$ 轮中选择一轮进行操作，并且需要满足每一轮的操作次数都不超过 $k$。这个问题就可以直接用贪心解决了。

直接记 $t_y$ 表示有几个 $pos_{i,j}=y$。从前往后遍历 $t$，维护变量 $s$ 表示当前最少有几个操作需要进行。则对于一个 $y$，$s\gets \max(s+t_y-k,0)$。答案 $g$ 可行当且仅当最后 $s=0$。

那么一次 `check` 的复杂度为 $\sum c$。注意到若 $\sum c_i > m\times k$ 则必定无解，故总复杂度为 $O(\log V\times\sum c) \le O(\log V\times mk)$。

## 代码

```cpp
#include <bits/stdc++.h>
using namespace std;
typedef long long ll;
const int N=10005;
int n,m,k,x;
int a[N],b[N],c[N],d[N],t[N];
// a,b,c,t 的含义如题目或题解中所说，d 数组为第 i 个数在不进行操作的情况下，m 轮后会变成多少
inline int _ceil(int x,int y){
	int g=x/y;
	return g+(g*y!=x);
}
bool check(int g){
	ll cnt=0; // 注意 c 数组的和可能会爆 int
	for(int i=1;i<=n;i++) if(d[i]>g){ // 为避免出现负数，可将本来就满足条件的下标跳过
		c[i]=_ceil(d[i]-g,x);
		if(d[i]-c[i]*x<0) return 0; // 一个数最后不可能在 [0,g] 中的情况
		cnt+=c[i];
	}
	if(cnt>m*k) return 0; // 判掉必定无解的情况
	for(int i=1;i<=m;i++) t[i]=0;
	for(int i=1;i<=n;i++) if(d[i]>g){
		for(int j=1;j<=c[i];j++){
			int pos=1;
			if(b[i]&&a[i]<j*x) pos=_ceil(j*x-a[i],b[i]); // 计算 pos
			t[pos]++;
		}
	}
	int s=0;
	for(int i=1;i<=m;i++) s=max(s+t[i]-k,0);
	return s==0;
}
int main(){
	ios::sync_with_stdio(false); cin.tie(nullptr); cout.tie(nullptr);
	cin>>n>>m>>k>>x;
	for(int i=1;i<=n;i++){
		cin>>a[i]>>b[i];
		d[i]=a[i]+b[i]*m;
	}
	int l=0,r=1.1e8,mid,ans=0; // 二分答案
	while(l<=r){
		mid=(l+r)>>1;
		if(check(mid)) r=mid-1,ans=mid;
		else l=mid+1;
	}
	cout<<ans<<"\n";
	return 0;
}
```