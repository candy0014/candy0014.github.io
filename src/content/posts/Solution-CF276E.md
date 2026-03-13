---
title: 'CF276E Little Girl and Problem on Trees 题解'
published: 2023-09-06
description: ' '
series: 'Solution'
tags: ['数据结构','树状数组']
---

>[洛谷传送门](https://www.luogu.com.cn/problem/CF276E)
>
>[CF 传送门](https://codeforces.com/problemset/problem/276/E)

## 题意

给定一颗以 $1$ 号节点为根的树，除了根节点的其它节点的度数不超过 $2$。

树有点权，初始均为 $0$。

需要实现两种操作：

- 将离 $u$ 节点 $d$ 单位以内的节点点权加上 $x$。

- 查询 $u$ 节点的点权。

## 思路

题意看着挺含蓄，什么节点度数不超过 $2$，就是 $1$ 号节点下面挂了一堆链，如下图为样例 $2$ 的图。

![1.png](https://picbed.candy0014.icu/posts/Solution-CF276E/pic1.png)

我们不妨将它横着画出来。

![2.png](https://picbed.candy0014.icu/posts/Solution-CF276E/pic2.png)

考虑第一种操作即修改操作如何维护。

例如我们要将样例 $2$ 中，将离 $5$ 节点 $3$ 单位以内的节点点权加上 $1$，那么我们要修改的部分分为以下蓝、紫、绿 $3$ 个区域。

![3.png](https://picbed.candy0014.icu/posts/Solution-CF276E/pic3.png)

其中蓝色部分，即 $1$ 号节点部分，单独开一个变量保存即可。而绿色区域，即位于自身那条链内的部分，可以对每条链开一个树状数组来实现。

而紫色部分，由于是每条链的前几个节点全部加 $x$，所以可以开一个全局的树状数组来实现。

为了不重复加，绿色区域的左端点要和紫色区域的右端点取 $\max$。

第二种操作即查询，直接将 $u$ 节点在链内树状数组所对应的值，加上它全局树状数组所对应的值即可。

最后，这两种操作，若操作的对象 $u$ 是 $1$ 号节点，则需要特殊处理。

对每一个分支开一个树状数组，若直接每个树状数组大小都开到 $n$，显然空间很容易炸，所以可以用 vector 来代替数组。

当然也可以给每个分支分配一个区间，最后开一个树状数组 ~~，但是懒得写了~~ 。

其它的细节可以看代码。

## 代码

```cpp
#include <bits/stdc++.h>
using namespace std;
int n,ca;
int head[100005],tot;
struct EDGE{
	int nex,to;
} e[200005];
void add(int u,int v){
	e[++tot].nex=head[u];
	e[tot].to=v;
	head[u]=tot;
}
int cnt,ans1,de[100005],id[100005];
//分别代表：1节点后的子树个数，1节点的答案，每个点在树状数组中的位置即其深度，每个点所在树状数组编号 
vector<int> sum[100005];//若干个树状数组，其中sum[0]代表全局加的树状数组 
void dfs(int u,int fa,int idd){//预处理de,id,sum数组 
	sum[idd].push_back(0);
	de[u]=de[fa]+1;
	id[u]=idd;
	for(int i=head[u];i;i=e[i].nex){
		int v=e[i].to;
		if(v==fa) continue;
		dfs(v,u,idd);
	}
}
//树状数组板板 
int query(int x,int id){
	int tmp=0;
	while(x){
		tmp+=sum[id][x];
		x-=(x&(-x));
	}
	return tmp;
}
void update(int x,int k,int id){
	while(x<sum[id].size()){
		sum[id][x]+=k;
		x+=(x&(-x));
	}
}
void change(int l,int r,int k,int id){
	update(l,k,id),update(r+1,-k,id);
}
int main(){
	scanf("%d%d",&n,&ca);
	for(int i=1,u,v;i<n;i++){
		scanf("%d%d",&u,&v);
		add(u,v),add(v,u);
	}
	for(int i=head[1];i;i=e[i].nex){
		dfs(e[i].to,1,++cnt);
	}
	for(int i=1;i<=n;i++){//预处理全局树状数组 
		sum[0].push_back(0);
	}
	for(int i=0;i<=cnt;i++){//由于vector下标从0开始，所以要再加一位 
		sum[i].push_back(0);
	}
	while(ca--){
		int op,v,x,d;
		scanf("%d%d",&op,&v);
		if(op==1){//查询 
			if(v==1){//特判1号节点 
				printf("%d\n",ans1);
			}
			else{
				printf("%d\n",query(de[v],0)+query(de[v],id[v]));//树状数组内标记加上全局标记 
			}
		}
		else{//修改 
			scanf("%d%d",&x,&d);
			if(v==1){//特判1号节点 
				change(1,min(d,n),x,0);
				ans1+=x;
				continue;
			}
			int tmp=min(n,d-de[v]);//紫色区域右端点 
			int l=max(tmp+1,max(0,de[v]-d));//绿色区域左端点，注意要和tmp+1取max 
			int r=min((int)sum[id[v]].size()-1,de[v]+d);//绿色区域右端点 
			//各种修改 
			if(l<=r) change(l,r,x,id[v]);
			if(tmp>0) change(1,tmp,x,0);
			if(tmp>=0) ans1+=x;
		}
	}
	return 0;
}
```