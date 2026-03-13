---
title: 'CF178D3 Magic Squares 题解'
published: 2023-09-06
description: ' '
series: 'Solution'
tags: ['递归/分治']
---

>[洛谷传送门](https://www.luogu.com.cn/problem/CF178D3)
>
>[CF 传送门](https://codeforces.com/problemset/problem/178/D3)

## 题意

给你 $n^2$ 个数，用这些数构成一个 $n$ 阶幻方，保证有解。

$n$ 阶幻方的定义：一个 $n$ 阶矩阵，满足每一行，每一列，每一条对角线的和都相等。

## 思路

注意到 $n \le 4$，自然想到爆搜。但普通爆搜是 $(n^2)!$ 的，肯定过不了，所以想到剪枝。

先是一个最能够想到的剪枝：

由于保证有解，所以可以算出每一行，每一列，每一条对角线的和。

那么每一行和每一列的最后一个数都不需要搜索，而是直接通过前面的算出。

于是我们爆搜的区域就从 $n^2$ 变成了 $(n-1)^2$。

然后你写出了代码，满怀信心地交上去，发现你成功的在第 18 个点 T 掉了。

~~通过获取第 18 个点的数据放在本地跑，~~ 我们发现它 T 的不是太严重，就多了不到一秒，所以需要一些玄学的优化。

设最终幻方为 $p$，由于 $\sum_{i=1}^{n} p_{i,1}=\sum_{i=1}^{n} p_{i,n-i+1}$，即第一列的和等于副对角线的和，那么 $\sum_{i=1}^{n-1} p_{i,1}=\sum_{i=1}^{n-1} p_{i,n-i+1}$，从而 $p_{n-1,2}=\sum_{i=1}^{n-1} p_{i,1}-\sum_{i=1}^{n-2} p_{i,n-i+1}$。

## 代码

```cpp
#include <bits/stdc++.h>
using namespace std;
typedef long long ll;
int n,m,a[20],sum;//sum代表每一行，每一列，每一条对角线的和 
int vis[114520];//哈希后的桶 
int p[10][10];//最终矩幻方 
int Hash(int x){//恶臭的哈希函数 
	return ll(1ll*(x+1e8)*(x+1e8))%114514;
}
void dfs(int x,int y){
	if(x==n){//最后一行的填数 
		int tmp1=0,tmp2=0,tmp3=0,tmp4=0;//分别代表：忽略第n行的数，主对角线的和，副对角线的和，第一列的和，第n列的和
		for(int i=1;i<n;i++){
			tmp1+=p[i][i],tmp2+=p[i][n-i+1],tmp3+=p[i][1],tmp4+=p[i][n];
		}
		if(tmp1!=tmp4||tmp2!=tmp3){//判断不可能情况 
			return;
		}
		for(int i=1;i<=n;i++){//填最后一行的数 
			int tmp=sum;
			for(int j=1;j<n;j++){
				tmp-=p[j][i];
			}
			int tmpp=Hash(tmp);
			if(!vis[tmpp]){//找不到，回溯 
				for(int j=1;j<i;j++){
					vis[Hash(p[n][j])]++;
				}
				return;
			}
			vis[tmpp]--,p[n][i]=tmp;
		}
		printf("%d\n",sum);//成功找到解 
		for(int i=1;i<=n;i++){
			for(int j=1;j<=n;j++){
				printf("%d ",p[i][j]);
			}
			printf("\n");
		}
		exit(0);
	}
	if(y==n){//最后一列的填数 
		int tmp=sum;
		for(int i=1;i<n;i++){
			tmp-=p[x][i];
		}
		int tmpp=Hash(tmp);
		if(!vis[tmpp]){//找不到，回溯 
			return;
		}
		vis[tmpp]--,p[x][n]=tmp;
		dfs(x+1,1);
		vis[tmpp]++;
		return;
	}
	if(x==n-1&&y==2){//位置(n-1,2)的填数 
		int tmp=0;
		for(int i=1;i<n;i++){
			tmp+=p[i][1];
		}
		for(int i=1;i<n-1;i++){
			tmp-=p[i][n-i+1];
		}
		int tmpp=Hash(tmp);
		if(!vis[tmpp]){//找不到，回溯 
			return;
		}
		vis[tmpp]--,p[x][y]=tmp;
		dfs(x,y+1);
		vis[tmpp]++;
		return;
	}
	for(int i=1;i<=m;i++){
		int tmpp=Hash(a[i]);
		if(vis[tmpp]){
			vis[tmpp]--,p[x][y]=a[i];
			dfs(x,y+1);
			vis[tmpp]++;
		}
	}
}
int main(){
	scanf("%d",&n);
	m=n*n;
	for(int i=1;i<=m;i++){
		scanf("%d",&a[i]);
		sum+=a[i];
		vis[Hash(a[i])]++;
	}
	sum/=n;
	dfs(1,1);
	return 0;
}
```