# P8375 [APIO2022] 游戏（懒标记，但不线段树）

**注意：以下下标均从 $1$ 开始。**

## 铺垫

由于起始传送器，每个点能够**到达**的特殊星球一定是一个**后缀**，每个点能够**被到达**的特殊星球一定是一个**前缀**。

对于每个点 $x$，不妨设前缀为 $[1,L_x]$，后缀为 $[R_x,k]$。

初始时特殊星球 $L_x=R_x=x$，非特殊星球 $L_x=0,R_x=m+1$。

显然，一名游客有可能获得无穷多枚邮票当且仅当存在某一个非特殊星球 $L_x\ge R_x$（以下称这个点**不合法**）。

只有当所有点均合法时程序才不会被终止，所以任意 $L_x<R_x$，将 $[L_x,R_x]$ 看作数组 $[0,m+1]$ 上的区间，发现每个区间的长度均 $\ge 2$。

## 开始

我们考察一条边 $x\to y$ 加入后会改变怎样的 $L,R$：

* * *

首先
$$
\text{ckmx}(L_y,L_x),\text{ckmn}(R_x,R_y)\quad(*)
$$

* * *

其次通过 $x,y$ 的新 $L,R$ 值去更新所有 $x,y$ 分别的出 / 入点。

* * *

我们不妨设 $\text{work}(x,y)$ 表示**更新**（第一次更新即为加入）$x\to y$ 这条**边**，而 $\text{expand}(x)$ 表示**更新**这个**点 $x$ 的所有入边和出边**。

容易发现这两个函数是**互相调用**的。

由 $(*)$ 式可知**每个点的 $[L,R]$ 只缩不伸**，所以复杂度为 $O((n+m)k)$。

## 推敲

若再要优化，则必须推敲 $(*)$ 式具体在干啥。

发现 $(*)$ 式的作用要按照 $x,y$ 的区间位置关系分类讨论。总共有 $6$ 类。

![](https://cdn.luogu.com.cn/upload/image_hosting/kcjokw2n.png)  

发现我们只关心**全局**是否存在 $L_x\ge R_x$ 的点。所以当一次更新时发现**之后的所有更新都不可能出现 $L_x\ge R_x$ 的点**，那我们就终止，可以理解为在这里打上一个**懒标记**（实际实现不用）。

## 懒标

首先有一个显而易见的东西：

给每一个点的区间 $[L_x,R_x]$ 选一个中间值 $M_x\in[L_x,R_x]\cap\mathbb{Z}$。

设其 $L_x$ 缩短至 $L'_x$，其 $R_x$ 缩短至 $R'_x$。

若有 $L'_x\le M_x<R'_x$，则 $x$ 这个点必然仍然合法。

不妨称 $L'_x\le M_x<R'_x$ 为**懒条件**。

* * *


所以得到了一个非常巧妙的懒标记方法：

取 $M_x=\lfloor\frac{L_x+R_x}{2}\rfloor$，若某一条边更新后 $x$ 点（$x$ 点可以是入点也可以是出点）新的 $L'_x,R'_x$ 满足懒条件，则我们不继续更新 $x$ 点了，由于下次 $\text{expand}(x)$ 能再从这里取到值并更新，所以直接退出无妨。

否则，若 $x$ 不满足懒条件了，直接更改 $L_x,R_x$（别忘了 $M_x$ 也要改），然后 $\text{expand}(x)$，成功呼应上一段中的 $\text{expand}(x)$。

## 时间

由于一次更新必将 $[L_x,R_x]$ 区间长度减半，所以为 $O((n+m)\log k)$。

## 代码

```cpp
//真·粘贴到代码开头
void init(int n, int k);
int add_teleporter(int u, int v);
#include<bits/stdc++.h>
using namespace std;
#define For(i,j,k) for(int i=(j),i##_=(k);i<=i##_;i++)
#define Rof(i,j,k) for(int i=(j),i##_=(k);i>=i##_;i--)
#define ckmx(a,b) a=max(a,b)
#define ckmn(a,b) a=min(a,b)
#define debug(...) cerr<<"#"<<__LINE__<<": "<<__VA_ARGS__<<endl
const int N=300010;
int n,m,flag,L[N],R[N];
vector<int> e[N],g[N];
void init(int nn,int mm){
	n=nn,m=mm;
	For(i,1,m) L[i]=R[i]=i;
	For(i,m+1,n) L[i]=0,R[i]=m+1;
	flag=0;
	For(i,1,n) e[i].clear(),g[i].clear();
}
void work(int x,int y);
void expand(int x){
	for(int i:e[x]) work(x,i);
	for(int i:g[x]) work(i,x);
}
void work(int x,int y){//flush x->y
	if(R[y]<=L[x]) return flag=1,void();
	int Mx=(L[x]+R[x])>>1,My=(L[y]+R[y])>>1;
	if(R[y]<=Mx) R[x]=Mx,expand(x);
	if(L[x]>=My+1) L[y]=My+1,expand(y);
}//mid=(l+r)>>1, Ltag<=mid Rtag>mid => R-L+1>=2
int add_teleporter(int x,int y){
	x++,y++;
	e[x].push_back(y),g[y].push_back(x);
	work(x,y);
	return flag;
}
```