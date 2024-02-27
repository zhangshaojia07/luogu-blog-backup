# P6765 [APIO2020]交换城市

### Analysis

* * *

**引理：** 若连通简单图 $G$ 不是链，则 $G$ 有一个点的度 $\ge 3$ 或有环。

**证明：** 无环连通图为树，一棵树不是链则有一个度数 $\ge 3$ 的点。

* * *

我们设两个城市为 $X,Y$，车为 $x,y$。

核心问题：怎样的图可以让 $X,Y$ 互通车？

手玩一下，发现答案是**除了链的所有图**（当然要保证两城市在同一个连通块中），下面给出证明。

* * *

首先一条链肯定是不行的，因为两辆车在链上的相对位置不会改变。

我们证明不是链（有一个点的度 $\ge 3$ 或有环）一定可行。

**有环：**

1. 将 $x,y$ 开到环上。

2. 将 $x,y$ 在环上交换位置。

3. $x$ 沿着 $y$ 来的路回，$y$ 同理。

**无环：**

不妨设度 $\ge 3$ 的点为 $O$，且度数恰好为 $3$，相邻的点为 $A,B,C$。

以 $O$ 定根。

不妨设城市 $X$ 在 $A$ 的子树内。

**（一）** $Y$ 也在 $A$ 子树内。不妨设 $Y$ **不是** $X$ 的祖先。则：

1. $x$ 移到 $B$。

2. $y$ 移到 $C$。

3. $x$ 移到 $Y$。

4. $y$ 移到 $X$。

**（二）** $Y$ 不在 $A$ 子树内，不妨设在 $B$ 的子树内。

1. $x$ 移到 $C$。

2. $y$ 移到 $X$。

3. $x$ 移到 $Y$。

* * *

我们要找到对于每组询问，让 $X,Y$ 同连通块不为链的最大边权最小值。（慢慢读 qwq）

这让我们想起了 **Kruskal 重构树**，没学过的快去学 awa。

将边按照边权排序，依次加入（设当前为 $(x,y)$）：

* 若 $x,y$ 已在一个连通块里。
1. 该连通块为链，新开结点，标记为非链。
2. 否则不做。

* 若 $x,y$ 不在一个连通块里。
1. $x,y$ 所在连通块中至少有一个不是链，或者 $x$ 或 $y$ 不为该连通块的两端之一。将两连通块合并，标记为非链。
2. 否则，将两连通块合并，标记为链，并记录两端以便后续查询。

最后询问时查询 $x,y$ 的 LCA 的最近非链祖先，输出边权即可。

### Detail

建议用 `C++ 20` 编译，否则会 CE。

注意不能 `#define int long long`，因为题目中给的函数返回必须是 int32。

由于此 Kruskal 重构树变形有 链 $\to$ 非链 新开节点的操作，空间记得开 $3$ 倍而不是 $2$ 倍。

### Code

```cpp
//Said no more counting dollars. We'll be counting stars.
#include<bits/stdc++.h>
using namespace std;
#define For(i,j,k) for(int i=j;i<=k;i++)
#define Rof(i,j,k) for(int i=j;i>=k;i--)
#define vi vector<int>
#define N 100010
#define M 200010
#define C 20 
struct edge{
	int u,v,w;
	friend bool operator<(edge x,edge y){return x.w<y.w;}
}e[M];
int rt[3*N],st[3*N],ed[3*N],f[3*N][C+1],tot,val[3*N],dep[3*N];
inline int gf(int x){return rt[x]==x?x:rt[x]=gf(rt[x]);}
void init(int n,int m,vi U,vi V,vi W){
	For(i,0,m-1) e[i+1]=(edge){U[i]+1,V[i]+1,W[i]};
	sort(e+1,e+1+m);
	For(i,1,3*n) rt[i]=i;
	For(i,1,n) st[i]=ed[i]=i;
	tot=n;
	int x,y,w,xx,yy;
	For(i,1,m){
		x=e[i].u,y=e[i].v,w=e[i].w;
		xx=gf(x),yy=gf(y);
		if(xx==yy){
			if(st[xx]!=-1){
				val[++tot]=w;
				f[xx][0]=tot;
				rt[xx]=tot;
				st[tot]=-1;
//				cerr<<xx<<"->"<<tot<<":"<<val[tot]<<" "<<st[tot]<<"*"<<ed[tot]<<endl; 
			}
		}else{
			val[++tot]=w;
			f[xx][0]=f[yy][0]=tot;
			rt[xx]=rt[yy]=tot;
			if(st[xx]!=-1 && st[yy]!=-1 && (x==st[xx] || x==ed[xx]) && (y==st[yy] || y==ed[yy])){
				st[tot]=x^st[xx]^ed[xx];
				ed[tot]=y^st[yy]^ed[yy];
			}else st[tot]=-1;
//			cerr<<xx<<"&"<<yy<<"->"<<tot<<":"<<val[tot]<<" "<<st[tot]<<"*"<<ed[tot]<<endl; 
		}
	}
	Rof(i,tot,1) dep[i]=dep[f[i][0]]+1;
//	For(i,1,tot) cout<<dep[i]<<" "; cout<<endl;
	For(j,1,C) For(i,1,tot) f[i][j]=f[f[i][j-1]][j-1];
}
int lca(int x,int y){
	if(dep[x]<dep[y]) swap(x,y);
	int xx,yy;
	Rof(i,C,0){
		xx=f[x][i];
		if(dep[xx]>=dep[y]) x=xx;
	}
	if(x==y) return x;
	Rof(i,C,0){
		xx=f[x][i];
		yy=f[y][i];
		if(xx!=yy) x=xx,y=yy;
	}
	return f[x][0];
}
int antichain(int x){
	if(st[x]==-1) return x;
	int xx;
	Rof(i,C,0){
		xx=f[x][i];
		if(xx && st[xx]!=-1) x=xx;
	}
	return f[x][0];
}
int getMinimumFuelCapacity(int x,int y){
	x++,y++;
	int z=lca(x,y);
	z=antichain(z);
	if(st[z]!=-1) return -1;
	else return val[z];
}
```