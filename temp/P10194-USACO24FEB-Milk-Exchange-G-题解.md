---
title: P10194 [USACO24FEB] Milk Exchange G 题解
date: 2024-02-28 17:24:00
tags: 题解
categories:
    - [算法基础,前缀和/差分]
    - [数据结构,单调栈]
description: P10194，洛谷蓝
---

{% folding cyan, 更新历史 %}

{% timeline 更新历史, blue %}
<!-- timeline 2024-02-28 -->
发布博客
<!-- endtimeline -->
{% endtimeline %}

{% endfolding %}

> 开始打比赛只剩 $3$ h 了，晚了 $1$ min 提交，痛失铂金……

> [洛谷传送门](https://www.luogu.com.cn/problem/P10194)

## 题意

有长度为 $n$ 的**环形**序列 $a$，初始时 $b=a$，每次操作令 $b_i=\min(b_{i-1},a_i),\forall\ i \in [1,n]$。

求前 $n$ 次操作，每次操作后 $b$ 数组的和。

## 思路

这里使用 `3 8 9 2 5 4 7 1 6` 这个样例来进行模拟。

由于奶牛是排成一圈的，所以可以将容量最小的奶桶轮换到第一个。此时，牛奶的量变为 `1 6 3 8 9 2 5 4 7`。

这时容易发现，第 $i$ 个奶桶的牛奶量在 $j$ 次操作后，会变成 $\min\limits_{k=\max(i-j+1,1)}^i \{a_k\}$。

这时候仍然不好观察，每次操作后每个奶桶的牛奶量变化，但是当我们把所有每次操作后牛奶量的表画出来时，就变得方便多了：

| 操作 | $\color{red}1$|$\color{purple}2$|$\color{yellow}3$|$\color{brown}4$|$\color{blue}5$|$\color{green}6$|$\color{gray}7$|$\color{orange}8$|$\color{pink}9$|
| :----------: | :----------: | :----------: | :----------: | :----------: | :----------: | :----------: | :----------: | :----------: | :----------: |
|$0$|$\color{red}1$|$\color{purple}6$|$\color{yellow}3$|$\color{brown}8$|$\color{blue}9$|$\color{green}2$|$\color{gray}5$|$\color{orange}4$|$\color{pink}7$|
|$1$|$\color{red}1$|$\color{red}1$|$\color{yellow}3$|$\color{yellow}3$|$\color{brown}8$|$\color{green}2$|$\color{green}2$|$\color{orange}4$|$\color{orange}4$|
|$2$|$\color{red}1$|$\color{red}1$|$\color{red}1$|$\color{yellow}3$|$\color{yellow}3$|$\color{green}2$|$\color{green}2$|$\color{green}2$|$\color{orange}4$|
|$3$|$\color{red}1$|$\color{red}1$|$\color{red}1$|$\color{red}1$|$\color{yellow}3$|$\color{green}2$|$\color{green}2$|$\color{green}2$|$\color{green}2$|
|$4$|$\color{red}1$|$\color{red}1$|$\color{red}1$|$\color{red}1$|$\color{red}1$|$\color{green}2$|$\color{green}2$|$\color{green}2$|$\color{green}2$|
|$5$|$\color{red}1$|$\color{red}1$|$\color{red}1$|$\color{red}1$|$\color{red}1$|$\color{red}1$|$\color{green}2$|$\color{green}2$|$\color{green}2$|
|$6$|$\color{red}1$|$\color{red}1$|$\color{red}1$|$\color{red}1$|$\color{red}1$|$\color{red}1$|$\color{red}1$|$\color{green}2$|$\color{green}2$|
|$7$|$\color{red}1$|$\color{red}1$|$\color{red}1$|$\color{red}1$|$\color{red}1$|$\color{red}1$|$\color{red}1$|$\color{red}1$|$\color{green}2$|
|$8$|$\color{red}1$|$\color{red}1$|$\color{red}1$|$\color{red}1$|$\color{red}1$|$\color{red}1$|$\color{red}1$|$\color{red}1$|$\color{red}1$|

可以发现，除了 $1$ 号奶桶容量在表中构成了一个完整的三角形，其它奶桶容量都构成了一个平行四边形。我们要求的答案，就是每行数的总和。

对于 $i$ 号桶对应的平行四边形，其对每一行产生的贡献，可以分为三段：一段公差为 $a_i$ 的等差数列，一段公差为 $0$ 的等差数列，一段公差为 $-a_i$ 的等差数列。而区间加等差数列是一个十分经典的问题，两次差分即可解决，如果不会请出门右转 [P4231 三步必杀](https://www.luogu.com.cn/problem/P4231)。

显然，我们只要知道出 $i$ 号桶对应平行四边形的底（$a$）和高（$h$），就能计算出要加的是哪三段等差数列。下面考虑平行四边形的底和高如何计算。我们用 $i=3$ 号桶对应的平行四边形举例。

| 操作 | ${}1$|${}2$|${}3$|${}4$|${}5$|${}6$|${}7$|${}8$|${}9$|
| :----------: | :----------: | :----------: | :----------: | :----------: | :----------: | :----------: | :----------: | :----------: | :----------: |
|$0$|${\color{blue}{\blacksquare}}1$|${}6$|${\color{purple}{\blacksquare}}\color{yellow}3$|${}8$|${}9$|${\color{orange}{\blacksquare}}2$|${}5$|${}4$|${}7$|
|$1$|${}1$|${}1$|${\color{green}{\blacksquare}}\color{yellow}3$|$\color{yellow}3$|${}8$|${}2$|${}2$|${}4$|${}4$|
|$2$|${}1$|${}1$|${\color{red}{\blacksquare}}1$|$\color{yellow}3$|$\color{yellow}3$|${}2$|${}2$|${}2$|${}4$|
|$3$|${}1$|${}1$|${}1$|${}1$|$\color{yellow}3$|${}2$|${}2$|${}2$|${}2$|
|$4$|${}1$|${}1$|${}1$|${}1$|${}1$|${}2$|${}2$|${}2$|${}2$|
|$5$|${}1$|${}1$|${}1$|${}1$|${}1$|${}1$|${}2$|${}2$|${}2$|
|$6$|${}1$|${}1$|${}1$|${}1$|${}1$|${}1$|${}1$|${}2$|${}2$|
|$7$|${}1$|${}1$|${}1$|${}1$|${}1$|${}1$|${}1$|${}1$|${}2$|
|$8$|${}1$|${}1$|${}1$|${}1$|${}1$|${}1$|${}1$|${}1$|${}1$|

$$a=dis({\color{purple}{\blacksquare}},{\color{green}{\blacksquare}})=dis({\color{purple}{\blacksquare}},{\color{red}{\blacksquare}})-1=dis({\color{purple}{\blacksquare}},{\color{blue}{\blacksquare}})-1=i-pre_i=3-pre_3=2$$

$$d=dis({\color{purple}{\blacksquare}},{\color{orange}{\blacksquare}})-1=nex_i-i=nex_3-3=3$$

其中 $pre_i$ 和 $nex_i$ 分别表示在 $i$ 之前最后一个小于等于 $a_i$ 的下标，以及在 $i$ 之后第一个小于 $a_i$ 的下标。这两个数组可以通过单调栈求得。

理由也很好解释：$i$ 号桶在 $i-pre_i$ 次操作后，就只能剩下 $a_{pre_i}$ 升牛奶；$i$ 号桶的奶在传到 $nex_i$ 时，就只能剩下 $a_{nex_i}$ 升牛奶。

所以我们可以求出 $i$ 号桶对应的底为 $i-pre_i$，对应的高为 $nex_i-i$。然后就能求出三个区间并进行区间加等差数列的操作。

这里有一个小细节：为什么 $pre_i$ 算的是小于等于 $a_i$，而 $nex_i$ 算的是小于 $a_i$。其实为了不重不漏地填数，两个必须是一个小于，一个小于等于。但由于我们开始时默认 $1$ 号桶构成的是一个三角形，而如果 $pre_i$ 算的是小于 $a_i$，那么若再碰到和 $a_1$ 相等的 $a_i$，就会加上一个超出表格的梯形，并且还会重复加一个单元格，所以 $pre_i$ 算的必须是小于等于。

## 代码

```cpp
#include <bits/stdc++.h>
using namespace std;
typedef long long ll;//不开 long long 见祖宗
const ll N=5e5+5;
ll n,a[N<<1];
ll pre[N],nex[N];
ll mi=1e9,mik;
ll st[N],r;
ll p[N];//差分数组
void change(ll l,ll r,ll x,ll d){//将从 l 到 r 的数加上以 x 为首项，d 为公差的等差数列
	if(l>r) return;
	ll y=x+(r-l)*d;
	p[l]+=x,p[l+1]-=x;
	p[l+1]+=d,p[r+1]-=d;
	p[r+1]-=y,p[r+2]+=y;
}
int main(){
	#ifndef ONLINE_JUDGE
	freopen("test.in","r",stdin);
	freopen("test.out","w",stdout);
	#endif
	ios::sync_with_stdio(false); cin.tie(nullptr); cout.tie(nullptr);
	cin>>n;
	for(ll i=1;i<=n;i++){
		cin>>a[i],a[i+n]=a[i];
		if(mi>a[i]) mi=a[i],mik=i;
	}
	for(ll i=1;i<=n;i++) a[i]=a[i+mik-1];//将最小值移到第一个

	r=0,st[r]=1;//单调栈求 pre 数组
	for(ll i=2;i<=n;i++){
		while(r>=1&&a[st[r]]>a[i]) r--;
		pre[i]=st[r],st[++r]=i;
	}
	r=0,st[r]=n+1;//单调栈求 nex 数组
	for(ll i=n;i>=2;i--){
		while(r>=1&&a[st[r]]>=a[i]) r--;
		nex[i]=st[r],st[++r]=i;
	}

	change(1,n,a[1],a[1]);
	for(ll i=2;i<=n;i++){
		ll A=i-pre[i],D=nex[i]-i;//平行四边形的底为 A，高为 D
		if(A<=D){//分类讨论
			change(1,A-1,a[i],a[i]);
			change(A,D,a[i]*A,0);
			change(D+1,A+D-1,a[i]*(A-1),-a[i]);
		}
		else{
			change(1,D,a[i],a[i]);
			change(D+1,A-1,a[i]*D,0);
			change(A,A+D-1,a[i]*D,-a[i]);
		}
	}

	for(ll i=1;i<=n;i++) p[i]+=p[i-1];//做两次前缀和还原数组
	for(ll i=1;i<=n;i++) p[i]+=p[i-1];
	for(ll i=2;i<=n;i++) cout<<p[i]<<"\n";
	cout<<p[n]<<"\n";//n 次操作后的结果和 n-1 次操作的结果相同
	return 0;
}
```