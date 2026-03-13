---
title: 'P12546 [UOI 2025] Convex Array 题解'
published: 2026-01-26
description: ' '
series: 'Solution'
tags: ['动态规划']
---

> **本篇题解时间复杂度不一定正确，若有可以 Hack 此题解或证明复杂度（或证明卡不掉？）请联系笔者。**

> [洛谷传送门](https://www.luogu.com.cn/problem/P12546)

## 题意

给定序列 $a_n$，问能否重排使得坐标点 $(i,a'_i)$ 下凸。

$n\le 3\times 10^5$。

## 思路

题意显然可以转为：先对 $a$ 排序，然后划分成两个子序列（最小值使用两次），使得每个子序列的差分数组不降。

考虑一种不同于其他题解的 dp 设计方案。

设 $dp(i,x_1,x_2,y_1,y_2)$ 表示：是否有对前 $i$ 个元素的划分，使得划分出的第一个子序列，最后一个数的下标为 $x_1$，**且若要满足差分数组不降的性质，下一个元素下标至少为 $y_1$**，$x_2,y_2$ 同理。

于是可以写出一个 状态 $O(n^5)$，转移 $O(1)$ 的 dp，但这太劣了，考虑对 dp 状态维度进行压缩。

> 注意到 $x_1,x_2$ 中至少一者为 $i$；想要在此状态下进行转移，则 $y_1,y_2$ 中至少一者为 $i+1$。压掉可以和 $i$ 相关的其中一个 $x$ 和一个 $y$，状态变为 $O(n^3)$，转移 $O(1)$

> 注意到 $y_1,y_2$ 不全为 $i+1$ 时，转移是唯一的（$i+1$ 只能分给 $y_?$ 等于 $i+1$ 的子序列）。又因为 dp 的初始状态满足 $y_1=y_2=i+1$，故在出现 $y_1,y_2$ 不全为 $i+1$ 时，可直接按此唯一的转移去做，直到 $y_1=y_2=i+1$，再将其记到 dp 数组里。于是可以只记录 $y_1,y_2=i+1$ 的情况，状态变为 $O(n^2)$，转移 $O(n)$。
>
> 但是通过尝试构造数据，可以发现转移复杂度均摊后极难卡满，事实上数据确实也没卡掉。
> 
> 当然这一步应该是有一些复杂度正确的做法的，懒得想了。

> 考虑可行性 dp 的经典优化，注意到只需要记录满足 $dp(i,x)$ 为 $1$ 的最大的 $x$，据此进行转移即可覆盖所有情况，状态变为 $O(n)$，转移最劣 $O(n)$，实际极快。

本来打算写个暴力交一发，但他直接过了？

时间复杂度 $O(n\log n+F(n)\times n)$，其中 $F(n)$ 为转移均摊后的神秘复杂度。

## 代码

```cpp
#include <bits/stdc++.h>
using namespace std;
int n,a[300005],dp[300005];
void work(int x,int y,int i){
	int dx=a[i]-a[x],dy=0;x=i,i++;
	while(i<=n){
		if(a[i]-a[x]>=dx&&a[i]-a[y]>=dy) break;
		if(a[i]-a[x]>=dx) dx=a[i]-a[x],x=i,i++;
		else if(a[i]-a[y]>=dy) dy=a[i]-a[y],y=i,i++;
		else return;
	}
	dp[i-1]=max(dp[i-1],min(x,y));
}
void solve(){
	cin>>n;
	for(int i=1;i<=n;i++) cin>>a[i],dp[i]=0;
	sort(a+1,a+n+1);
	dp[1]=1;
	for(int i=1;i<n;i++) if(dp[i]) work(i,dp[i],i+1),work(dp[i],i,i+1);
	if(dp[n]) cout<<"YES\n";
	else cout<<"NO\n";
}
int main(){
	ios::sync_with_stdio(0);cin.tie(0);cout.tie(0);
	int Ca;cin>>Ca;while(Ca--)solve();
	return 0;
}
```