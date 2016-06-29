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
* maxn是题目给的最大区间,而节点数要开4倍,确切的来说节点数要开大于maxn的最小2x的两倍[戳这里](http://scinart.github.io/acm/2014/03/19/acm-segment-tree-space-analysis/)
* lson和rson分辨表示结点的左儿子和右儿子,由于每次传参数的时候都固定是这几个变量,所以可以用预定于比较方便的表示
* rt表示当前子树的根(root),也就是当前所在的结点。每次叶节点操作的sum数组下标都是rt，不会是l，容易出错
* pushup(int l,int r,int rt)是根据儿子节点向上更新父节点
* pushdown(int l,int r,int rt)是根据父节点向下更新儿子节点
* 整个线段树封装成一个struct，可以当作一个stl使用，减少重名，写题时还能减少思考量
* 有些线段树的题g++会TLE，需要用c++提交。另外，线段树递归太多，容易爆栈，可以用#pragma comment(linker, "/STACK:102400000,102400000")在c++环境下扩栈。

## 点更新区间查询
最最基础也最最重要的线段树,更新时只修改叶子节点,然后通过pushup(int r)函数向上更新

先来个例题：[hdu1166 敌兵布阵](http://acm.hdu.edu.cn/showproblem.php?pid=1166)

线段树功能：update:单点增减 query：区间求和

{% highlight c++ %}
	#include<iostream>
	#include<cstdio>
	using namespace std;
	typedef long long ll;
	#define mem(name,value) memset(name,value,sizeof(name))
	#pragma comment(linker, "/STACK:102400000,102400000")
	#define lson l,m,rt<<1
	#define rson m+1,r,rt<<1|1
	const int maxn=50010;
	
	struct SegTree
	{
	    int sum[maxn<<2];
	
	    void pushup(int l,int r,int rt){
	        sum[rt]=sum[rt<<1]+sum[rt<<1|1];
	    }
	
	    void build(int l,int r,int rt,int a[]){
	        if(l==r){
	            sum[rt]=a[l];
	            return;
	        }
	        int m=(l+r)>>1;
	        build(lson,a);
	        build(rson,a);
	        pushup(l,r,rt);
	    }
	
	    void update(int x,int c,int l,int r,int rt){
	        if(l==r){
	            sum[rt]+=c;
	            return;
	        }
	        int m=(l+r)>>1;
	        if(x<=m) update(x,c,lson);
	        else update(x,c,rson);
	        pushup(l,r,rt);
	    }
	
	    int query(int L,int R,int l,int r,int rt){
	        if(L<=l && r<=R) return sum[rt];
	        int m=(l+r)>>1,ans=0;
	        if(L<=m) ans+=query(L,R,lson);
	        if(m<R) ans+=query(L,R,rson);
	        return ans;
	    }
	}tree;
	
	int n;
	int a[maxn];
	char op[10];
	
	int main()
	{
	    int T,cas=1;
	    cin>>T;
	    while(T--){
	        printf("Case %d:\n",cas++);
	
	        cin>>n;
	        for(int i=1;i<=n;i++) scanf("%d",&a[i]);
	
	        tree.build(1,n,1,a);
	        while(scanf("%s",&op)==1 && op[0]!='E'){
	            int x,y;
	            scanf("%d%d",&x,&y);
	            if(op[0]=='A') tree.update(x,y,1,n,1);
	            else if(op[0]=='S') tree.update(x,-y,1,n,1);
	            else printf("%d\n",tree.query(x,y,1,n,1));
	        }
	    }
	    return 0;
	}
{% endhighlight %}

[hdu1754 I Hate It](http://acm.hdu.edu.cn/showproblem.php?pid=1754)
线段树功能：update：单点替换，query：区间最大值

```C++
#include<iostream>
#include<cstdio>
using namespace std;
typedef long long ll;
#define mem(name,value) memset(name,value,sizeof(name))
#pragma comment(linker, "/STACK:102400000,102400000")
#define lson l,m,rt<<1
#define rson m+1,r,rt<<1|1
const int maxn=200010;

struct SegTree
{
    int mmax[maxn<<2];

    void pushup(int l,int r,int rt){
        mmax[rt]=max(mmax[rt<<1],mmax[rt<<1|1]);
    }

    void build(int l,int r,int rt,int a[]){
        if(l==r){
            mmax[rt]=a[l];
            return;
        }
        int m=(l+r)>>1;
        build(lson,a);
        build(rson,a);
        pushup(l,r,rt);
    }

    void update(int x,int c,int l,int r,int rt){
        if(l==r){
            mmax[rt]=c;
            return;
        }
        int m=(l+r)>>1;
        if(x<=m) update(x,c,lson);
        else update(x,c,rson);
        pushup(l,r,rt);
    }

    int query(int L,int R,int l,int r,int rt){
        if(L<=l && r<=R) return mmax[rt];
        int m=(l+r)>>1,ans=0;
        if(L<=m) ans=max(ans,query(L,R,lson));
        if(m<R) ans=max(ans,query(L,R,rson));
        return ans;
    }
}tree;

int n,q;
int a[maxn];

int main()
{
    while(cin>>n>>q){
        for(int i=1;i<=n;i++) scanf("%d",&a[i]);

        tree.build(1,n,1,a);
        while(q--){
            char op[10];
            int x,y;
            scanf("%s%d%d",&op,&x,&y);
            if(op[0]=='U') tree.update(x,y,1,n,1);
            else printf("%d\n",tree.query(x,y,1,n,1));
        }
    }
    return 0;
}
```


点更新的线段树的思想非常经典，强烈建议深入理解。以上只是纯模板，还可以衍生出一系列的问题，比如：区间求最值大小与位置，求区间最左边为1的位置......可以说线段树可以做到大部分数据结构能做到的。

推荐练习：
[hdu1394 Minimum Inversion Number](http://acm.hdu.edu.cn/showproblem.php?pid=1394)
[hdu2795 Billboard](http://acm.hdu.edu.cn/showproblem.php?pid=2795)
[poj2828 Buy Tickets](http://poj.org/problem?id=2828)
[poj2886 Who Gets the Most Candies?](http://poj.org/problem?id=2886)
先mark，有空再更新一些线段树变化的题。

---

## 成段更新
(通常这对初学者来说是一道坎),需要用到延迟标记(或者说懒惰标记),简单来说就是每次更新的时候不要更新到底,用延迟标记使得更新延迟到下次需要更新or询问到的时候。来看题：

[poj3468 A Simple Problem with Integers](http://poj.org/problem?id=3468)
线段树功能：update：区间增减，query：区间求和

```C++
#include<iostream>
#include<cstdio>
using namespace std;
typedef long long ll;
#define mem(name,value) memset(name,value,sizeof(name))
#pragma comment(linker, "/STACK:102400000,102400000")
#define lson l,m,rt<<1
#define rson m+1,r,rt<<1|1
const int maxn=100010;

struct SegTree
{
    ll sum[maxn<<2],col[maxn<<2];

    void pushup(int l,int r,int rt){
        sum[rt]=sum[rt<<1]+sum[rt<<1|1];
    }

    void pushdown(int l,int r,int rt){
        int m=(l+r)>>1;
        sum[rt<<1]+=(m-l+1)*col[rt];
        col[rt<<1]+=col[rt];
        sum[rt<<1|1]+=(r-m)*col[rt];
        col[rt<<1|1]+=col[rt];
        col[rt]=0;
    }

    void build(int l,int r,int rt,int a[]){
        col[rt]=0;
        if(l==r){
            sum[rt]=a[l];
            return;
        }
        int m=(l+r)>>1;
        build(lson,a);
        build(rson,a);
        pushup(l,r,rt);
    }

    void update(int L,int R,int c,int l,int r,int rt){
        if(L<=l && r<=R){
            sum[rt]+=(ll)c*(r-l+1);
            col[rt]+=c;
            return;
        }
        if(col[rt]) pushdown(l,r,rt);
        int m=(l+r)>>1;
        if(L<=m) update(L,R,c,lson);
        if(m<R) update(L,R,c,rson);
        pushup(l,r,rt);
    }

    ll query(int L,int R,int l,int r,int rt){
        if(L<=l && r<=R) return sum[rt];
        if(col[rt]) pushdown(l,r,rt);
        int m=(l+r)>>1;
        ll ans=0;
        if(L<=m) ans+=query(L,R,lson);
        if(m<R) ans+=query(L,R,rson);
        return ans;
    }
}tree;

int n,q;
int a[maxn];

int main()
{
    while(cin>>n>>q){
        for(int i=1;i<=n;i++) scanf("%d",&a[i]);

        tree.build(1,n,1,a);
        while(q--){
            char op[10];
            int x,y,z;
            scanf("%s",op);
            if(op[0]=='C'){
                scanf("%d%d%d",&x,&y,&z);
                tree.update(x,y,z,1,n,1);
            }else{
                scanf("%d%d",&x,&y);
                printf("%lld\n",tree.query(x,y,1,n,1));
            }
        }
    }
    return 0;
}
```

[poj2528 Mayor’s posters](http://poj.org/problem?id=2528)
题意:在墙上贴海报,海报可以互相覆盖,问最后可以看见几张海报
思路:这题数据范围很大,直接搞超时+超内存,需要离散化:
离散化简单的来说就是只取我们需要的值来用,比如说区间[1000,2000],[1990,2012] 我们用不到[-∞,999][1001,1989][1991,1999][2001,2011][2013,+∞]这些值,所以我只需要1000,1990,2000,2012就够了,将其分别映射到0,1,2,3,在于复杂度就大大的降下来了,所以离散化要保存所有需要用到的值,排序后,分别映射到1~n,这样复杂度就会小很多很多
而这题的难点在于每个数字其实表示的是一个单位长度(并且一个点),这样普通的离散化会造成许多错误(poj这题数据奇弱)
给出下面两个简单的例子应该能体现普通离散化的缺陷:

1-10 1-4 5-10
1-10 1-4 6-10

为了解决这种缺陷,我们可以在排序后的数组上加些处理,比如说[1,2,6,10]
如果相邻数字间距大于1的话,在其中加上任意一个数字,比如加成[1,2,3,6,7,10],然后再做线段树就好了.

线段树功能:update:成段替换 query:简单统计

```C++
#include<iostream>
#include<cstdio>
#include<vector>
#include<algorithm>
#include<cstring>
using namespace std;
typedef long long ll;
#define mem(name,value) memset(name,value,sizeof(name))
#pragma comment(linker, "/STACK:102400000,102400000")
#define lson l,m,rt<<1
#define rson m+1,r,rt<<1|1
const int maxn=10010;

struct SegTree
{
    ll col[maxn<<4];

    void pushdown(int l,int r,int rt){
        col[rt<<1]=col[rt];
        col[rt<<1|1]=col[rt];
        col[rt]=0;
    }

    void build(int l,int r,int rt){
        col[rt]=0;
        if(l==r) return;
        int m=(l+r)>>1;
        build(lson);
        build(rson);
    }

    void update(int L,int R,int c,int l,int r,int rt){
        if(L<=l && r<=R){
            col[rt]=c;
            return;
        }
        if(col[rt]) pushdown(l,r,rt);
        int m=(l+r)>>1;
        if(L<=m) update(L,R,c,lson);
        if(m<R) update(L,R,c,rson);
    }

    void query(int l,int r,int rt,bool vis[]){
        if(l==r){
            vis[col[rt]]=1;
            return;
        }
        if(col[rt]) pushdown(l,r,rt);
        int m=(l+r)>>1;
        query(lson,vis);
        query(rson,vis);
    }
}tree;

int n,q;
int l[maxn],r[maxn],xx[maxn<<2];
bool vis[maxn];

int main()
{
    int T;
    cin>>T;
    while(T--){
        cin>>n;
        int nn=0;
        for(int i=0;i<n;i++){
            scanf("%d%d",&l[i],&r[i]);
            xx[nn++]=l[i];
            xx[nn++]=r[i];
        }

        sort(xx,xx+nn);
        nn=unique(xx,xx+nn)-xx;
        for(int i=nn-1;i>0;i--)
            if(xx[i]!=xx[i-1]+1) xx[nn++]=xx[i-1]+1;
        sort(xx,xx+nn);

        tree.build(1,nn,1);
        for(int i=0;i<n;i++){
            int ll=lower_bound(xx,xx+nn,l[i])-xx+1;
            int rr=lower_bound(xx,xx+nn,r[i])-xx+1;
            tree.update(ll,rr,i+1,1,nn,1);
        }

        mem(vis,0);
        tree.query(1,nn,1,vis);

        int ans=0;
        for(int i=1;i<=n;i++)
            if(vis[i]) ans++;

        cout<<ans<<endl;
    }
    return 0;
}
```

推荐练习：
[poj3225 Help with Intervals](http://poj.org/problem?id=3225)
[poj1436 Horizontally Visible Segments](http://poj.org/problem?id=1436)
[poj2991 Crane](http://poj.org/problem?id=2991)


## 区间合并
## 扫描线

写博客好累，就先这样吧 这几天每天更新一点好了...


