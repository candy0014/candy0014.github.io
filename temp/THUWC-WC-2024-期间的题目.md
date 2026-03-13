---
title: THUWC & WC 2024 期间的题目
date: 2024-01-26 21:39:00
tags: 附属文件
hidden: true
description: THUWC & WC 2024 期间的题目
---

{% folding cyan, 更新历史 %}

{% timeline 更新历史, blue %}
<!-- timeline 2024-01-26 -->
发布博客
<!-- endtimeline -->
<!-- timeline 2024-02-02 -->
修改了 DAY 1 T1 的题面错误
<!-- endtimeline -->
{% endtimeline %}

{% endfolding %}

[**THUWC & WC 2024 游记**](https://www.candy0014.icu/2024/01/25/THUWC-WC-2024-%E6%B8%B8%E8%AE%B0/)

# THUWC 2024

## DAY 0

[$\boxed{\text{T1}}$](https://uoj.ac/problem/1)[$\boxed{\text{T2}}$](https://uoj.ac/problem/225)[$\boxed{\text{T3}}$](https://uoj.ac/problem/52)

## DAY 1

### T1

给定 $n \times m$ 的正整数矩阵 $(a)$，以及正整数数组 $c_{1 \sim n}$。

任选 $[1,n]$ 的子集 $p$，求 $\sum\limits_{j=1}^m \max\limits_{i \in p} a_{i,j} - \sum\limits_{i \in p} c_i$ 的最大值。

数据保证 $n \le 3000$，$m \le 15$，$a_{i,j},c_i \le 100$。

### T2

给定长度为 $n$ 的顺时针**环形**序列 $a_{0 \sim n-1}$。

一次操作定义为：等概率随机选取 $0 \le l,r < n$，将 $a_l$ 顺时针至 $a_r$ 中的元素等概率随机打乱。

问 $m$ 次操作后 $a_{0 \sim n-1}$ 的期望值对 $998244353$ 取模后的结果。

数据保证 $n \le 131072$，$m \le 10^9$。

### T3

**此题为交互题。**

交互库生成一个 $n \times m$ 的 01 矩阵 $(a)$。

你可以询问任意矩形内是否有 1。你需要求出 $a_{i,j}=1$ 满足 $i+j$ 最大，求出 $i+j$。

设询问次数为 $x$，每次询问的矩形的长边之和为 $y$，当 $x \le 8192$，$y \le 7.4 \times 10^4$ 时，你的程序可以获得满分。

数据保证 $n,m \le 4096$。

### T4

有一个初始全为 $0$ 的 $n \times m$ 的矩阵 $(a)$，你需要满足 $q$ 次如下操作：

定义 $b_{i,j}$：若 $a_{i,j}=0$，则 $b_{i,j}=\infty$；否则 $b_{i,j}$ 表示从 $a_{i,j+1}$ 向右最长连续 $1$ 的个数。

- `1 x y1 y2`，表示把第 $x$ 行的第 $y1$ 到第 $y2$ 个数赋值为 $1$，并更新 $b$。

- `2 x1 y1 x2 y2`，对于从 $a_{x1,y1}$ 到 $a_{x2,y2}$ 的每一条路径，设其权值为经过的点中 $b$ 值的最小值。求所有路径中权值最大是多少。

设操作 $1$ 的次数为 $q_1$。

数据保证 $n \le 10^5$，$m \le 10^9$，$q \le 3 \times 10^5$，$q_1 \le 10^5$。

## DAY 2

### T1

**此题为工程题（交互题）。**

定义一种游戏“四子棋”：给定一个 $n$ 行 $m$ 列的棋盘，棋盘上有一个给定的格点不能落子。两位玩家轮流操作，每次允许在一个还有空的列上操作，在这一列的最下面一个能落子的空格上，放置自己的棋子。先将自己棋子连成一条长度为 $4$ 的线段（允许横着、竖着、斜着 $45^\circ$）者获胜。

你需要实现一个决策函数：传入当前局面，决策函数需要给出下一步的落子位置。

提交程序后，平台会用你的决策程序，和其他强度不一的决策程序对战。和每个其他决策程序分别对战两次，区别为先后手不同。最后得分为战胜的对战局数。

数据保证 $9 \le n,m \le 12$。

# CodeForces Div2 2024-1-27

[$\boxed{\text{T1}}$](https://codeforces.com/contest/1925/problem/A)[$\boxed{\text{T2}}$](https://codeforces.com/contest/1925/problem/B)[$\boxed{\text{T3}}$](https://codeforces.com/contest/1925/problem/C)[$\boxed{\text{T4}}$](https://codeforces.com/contest/1925/problem/D)[$\boxed{\text{T5}}$](https://codeforces.com/contest/1925/problem/E)[$\boxed{\text{T6}}$](https://codeforces.com/contest/1925/problem/F)