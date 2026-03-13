---
title: CF963B Destruction of a Tree 题解
date: 2023-07-21 12:26:00
tags: 题解
categories:
    - [算法基础,递归/分治]
    - [算法基础,贪心]
description: CF963B，洛谷绿，CF 200
---

{% folding cyan, 更新历史 %}

{% timeline 更新历史, blue %}
<!-- timeline 2023-07-21 -->
发布博客
<!-- endtimeline -->
<!-- timeline 2024-02-28 -->
然而已经降绿了。。
<!-- endtimeline -->
{% endtimeline %}

{% endfolding %}

> 作为一道为数不多的自己想出来的紫题 ~~（实际可能只有绿）~~ ，决定写篇题解记录一下。

> [洛谷传送门](https://www.luogu.com.cn/problem/CF963B)
>
> [CF 传送门](https://codeforces.com/problemset/problem/963/B)

# 题意

给定一颗 $n$ 个节点的树，每次可以删去任意一个度数为偶数的点。问能否通过若干次操作，把这棵树删光。

# 思路

容易发现，若一个节点 $u$ 的度数为偶数，且在以 $u$ 为根的这颗子树中，除了 $u$ 的每一个点的度数都为奇数，则可以直接按照 dfs 遍历的顺序删点。

很明显，如果存在这样一个节点 $u$，那么直接删去以 $u$ 为根的子树，一定是最优的。

感性理解一下，如果不把 $u$ 删去，那么 $u$ 的所有祖先都无法按照这种方式删去子树。

所以我们可以用深搜来求解。用 $work(u)$ 函数表示：将以 $u$ 为根的子树删光，或者使得这棵子树内的每一个点度数均为奇数的删数方案记录。

在 $work(u)$ 函数中，先将 $u$ 的每个儿子节点都进行 $work$ 的操作。操作之后若 $u$ 的度数为偶数，就删光这棵子树。

最后，如果仍有点没被删除，就说明无解。

# 代码

```cpp
#include <bits/stdc++.h>
using namespace std;
int n;
int du[200005],vis[200005];
// du数组存每个点度数，vis数组为 1/0 代表此节点 有/无 被删去 
int head[200005],tot;
struct EDGE{int nex,to;}s[400005];
void add(int u,int v){
	s[++tot].nex=head[u],s[tot].to=v;
	head[u]=tot;
	du[u]++;
}
vector<int>ans;
void dfs(int u,int fa){
	vis[u]=1,ans.push_back(u);
	for(int i=head[u];i;i=s[i].nex){
		int v=s[i].to;
		if(v==fa||vis[v]) continue;//已经删掉的子节点不需要递归 
		dfs(v,u);
	}
}
void work(int u,int fa){
	for(int i=head[u];i;i=s[i].nex){
		int v=s[i].to;
		if(v==fa) continue;
		work(v,u);
	}
	if(du[u]%2==0){
		dfs(u,fa);//删光以u节点为根的子树 
		du[fa]--;//由于以u节点为根的子树已经处理完了，所以只需改变u父亲的度数即可 
	}
}
int main(){
	scanf("%d",&n);
	for(int i=1,u;i<=n;i++){
		scanf("%d",&u);
		if(u) add(i,u),add(u,i);
	}
	work(1,0);
	if(!vis[1]){ 
		printf("NO\n");
		return 0;
	}
	printf("YES\n");//若1号节点被删除了，其余肯定都被删除了 
	for(int i=0;i<n;i++) printf("%d\n",ans[i]);
	return 0;
}
```