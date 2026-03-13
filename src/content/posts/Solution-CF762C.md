---
title: 'CF762C Two strings 题解'
published: 2023-09-18
description: ' '
series: 'Solution'
tags: ['双指针']
---

>[洛谷传送门](https://www.luogu.com.cn/problem/CF762C)
>
>[CF 传送门](https://codeforces.com/problemset/problem/762/C)

## 题意

给你两个字符串 $a$ 和 $b$，你可以在 $b$ 中删去尽量短的子段，使得 $b$ 是 $a$ 的子序列。求出最后的 $b$。

## 思路

~~真是奇了怪了，这种题洛谷题解里竟然没有双指针的做法？~~

首先考虑判断一个字符串 $b$ 是否是另一个字符串 $a$ 的子序列。这个问题相信学过点 OI 的人都会做。

其实在做上面这个题的时候，因为贪心，$b$ 字符串的最右边字符对应在 $a$ 中一定是最靠左的。那么很容易，也可以求出 $b$ 的最左边字符在 $a$ 中最靠右的位置。

现在再看原题，其实就是把 $b$ 字符串分成前后两个部分，其中前一个部分在 $a$ 中尽量靠左，后一个部分在 $a$ 中尽量靠右，并且两个不相交。

然后就好做了，先对 $b$ 的每一个前缀求出「最右边字符对应在 $a$ 中最靠左的位置」，每一个后缀求出「最左边字符对应在 $a$ 中最靠右的位置」，分别用 $L$ 和 $R$ 数组保存。

最后我们要选出两个断点 $l$ 和 $r$，分别代表分成的前后两个部分的右端点和左端点，并且 $L_l<R_r$，同时 $r-l$ 要尽量的小。

所以这个怎么求呢？等等， $L$ 和 $R$ 单调递增？

那不是妥妥的双指针吗？

然后这题就做完了，时间复杂度 $O(n)$。

## 代码

```cpp
#include <bits/stdc++.h>
using namespace std;
int n,m;
string a,b;
int L[100005],R[100005];
int ans=1e9,k1,k2;//最短r-l，以及两个断点的记录 
int main(){
	cin>>a>>b;
	n=a.length(),m=b.length();
	a=" "+a,b=" "+b;
	int r=1;//指针 
	for(int i=1;i<=m;i++){//预处理L数组 
		while(r<=n&&a[p]!=b[i]) r++;
		L[i]=r,r=min(r+1,n+1);
	}
	r=n;
	for(int i=m;i>=1;i--){//预处理R数组 
		while(r>=1&&a[p]!=b[i]) r--;
		R[i]=r,r=max(r-1,0);
	}
	if(L[1]==n+1&&R[m]==0){//这里特判一下，或者在最后特判也行 
		printf("-\n");
		return 0;
	}
	L[0]=0,R[m+1]=n+1;//防越界特判的操作 
	r=1;
	for(int l=0;l<=m;i++){//计算答案，双指针板板 
		if(L[l]==n+1) break;
		r=max(r,l+1);
		while(L[l]>=R[r]){
			r++; 
		}
		if(r-l-1<ans){
			ans=r-l-1,k1=l,k2=r;
		}
	}
	for(int i=1;i<=m;i++){
		if(i<=k1||i>=k2){
			cout<<b[i];
		}
	}
	printf("\n");
	return 0;
}
```