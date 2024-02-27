# CF1706E Qpwoeirut and Vertices（Kruskal 重构树 + 线段树）

蠢蠢的 $O((n+q)\log^2n)$ 做法~~太简单了~~。

先建出 Kruskal 重构树，并倍增预处理，接下来问题转化为树上多点 $\text{lca}$。

对于 $\text{lca}$，我们有结合律 $\text{lca}(\text{lca}(a,b),c)=\text{lca}(a,\text{lca}(b,c))$。

所以我们可以对点（按标号排）建线段树，树上每个节点存这段区间所有点的 $\text{lca}$。

然后就是线段树最最最普通的区间查询了。

合并时 $t_{rt}=\text{lca}(t_{lson},t_{rson})$ 带 $\log$，所以总体是双 $\log$。

```cpp
//We'll be counting stars.
#pragma GCC optimize("Ofast")
#include<bits/stdc++.h>
using namespace std;
#define For(i,j,k) for(int i=(j),i##_=(k);i<=i##_;i++)
#define Rof(i,j,k) for(int i=(j),i##_=(k);i>=i##_;i--)
#define N 200010
#define C 20
int n,m,q,f[N],tot,g[N][C+1],son[N][C+1],a[N],dep[N],t[N<<2];
inline int gf(int x){ return x==f[x]?x:f[x]=gf(f[x]); }
inline int lca(int x,int y){
	int xx,yy;
	if(dep[x]<dep[y]) swap(x,y);
	Rof(i,C,0){
		xx=g[x][i];
		if(dep[xx]>=dep[y]) x=xx;
	}
	if(x==y) return x;
	Rof(i,C,0){
		xx=g[x][i];
		yy=g[y][i];
		if(xx!=yy) x=xx,y=yy;
	}
	return g[x][0];
}
inline void dfs(int x){
	For(i,1,C) g[x][i]=g[g[x][i-1]][i-1];
	dep[x]=dep[g[x][0]]+1;
	if(x>n) dfs(son[x][0]),dfs(son[x][1]);
}
#define ls (rt<<1)
#define rs (rt<<1|1)
#define mid ((l+r)>>1)
void build(int rt,int l,int r){
	if(l==r){
		t[rt]=l;
		return ;
	}
	build(ls,l,mid);
	build(rs,mid+1,r);
	t[rt]=lca(t[ls],t[rs]);
}
int que(int rt,int l,int r,int x,int y){
	if(x<=l && r<=y) return t[rt];
	if(y<=mid) return que(ls,l,mid,x,y);
	else if(x>mid) return que(rs,mid+1,r,x,y);
	else return lca(que(ls,l,mid,x,y),que(rs,mid+1,r,x,y));
}
void work(){
	scanf("%d%d%d",&n,&m,&q);
	iota(f+1,f+1+2*n,1);
	fill(a+1,a+1+n,0);
	tot=n;
	int x,y;
	For(i,1,m){
		scanf("%d%d",&x,&y);
		x=gf(x),y=gf(y);
		if(x!=y){
			g[x][0]=g[y][0]=f[x]=f[y]=++tot;
			son[tot][0]=x;
			son[tot][1]=y;
			a[tot]=i;
		}
	}
	g[tot][0]=0;
	dfs(tot);
	build(1,1,n);
	while(q--) scanf("%d%d",&x,&y),printf("%d ",a[que(1,1,n,x,y)]); puts("");
}
int main(){
	int T;scanf("%d",&T);
	while(T--)work();
return 0;}
```