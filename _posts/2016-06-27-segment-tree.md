---
published:  true
layout:     post
title:      "线段树"
subtitle:   "Hello，SegmentTree"
author:     "totoday"
header-img: "img/post-bg-segmenttree.jpg"
header-mask: 0.3
catalog:    true
tags:
    - ACM算法
---

> 博客刚开通，然而最近比较懒散，没学什么新东西，不知道写点什么。正巧要讲一波线段树，就来写一波线段树好了，权当是温故而知新（其实是希望能涨一波访问）。
> 
> 我的线段树学自notonlysuccess的博客，写的非常清晰易懂，不幸的是他的博客已经关掉了。以下内容，如有雷同，纯属抄袭。

## 定义
首先，线段树是一棵二叉“树”（本文的构造方法并不形成完全二叉树）。同时，“线段”两字反映出线段树的另一个特点：每个节点表示的是一个“线段”，或者说是一个区间。事实上，一棵线段树的根节点表示的是“整体”的区间，而它的左右子树也是一棵线段树，分别表示的是这个区间的左半边和右半边。

## 代码风格
* maxn是题目给的最大区间,而节点数要开4倍,确切的来说节点数要开大于maxn的最小2x的两倍
* lson和rson分辨表示结点的左儿子和右儿子,由于每次传参数的时候都固定是这几个变量,所以可以用预定于比较方便的表示
* rt表示当前子树的根(root),也就是当前所在的结点
* pushup(int l,int r,int rt)是根据儿子节点向上更新父节点
* pushdown(int l,int r,int rt)是根据父节点向下更新儿子节点


今晚好像挺晚了，就先这样吧 这几天每天更新一点好了...

线段树开四倍空间[戳这里](http://scinart.github.io/acm/2014/03/19/acm-segment-tree-space-analysis/)


