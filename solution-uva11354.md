# UVA11354 Bond

### Preface

为啥没有 **Kruskal 重构树**的题解 qwq 我来写一个。

（没学过的快去找模板题练）

### Analysis

先建出重构树，再倍增求 LCA 即可。

讲讲为啥这样是对的：

* 首先，这样是一个可行的解。

* 其次，如果我们不经过 LCA 节点所代表的边，由于 Kruskal 是按边的权值排序的，那让这两个并查集（或称为连通块）连通的边权一定不会比 LCA 小。

讲完了，觉得简单就是 Kruskal 重构树厉害。

### Detail

注意输出格式，比较坑。

### Code

```cpp
//Said no more counting dollars. We'll be counting stars.
#pragma GCC optimize("Ofast")
#include<bits/stdc++.h>
using namespace std;
#define IOS ios::sync_with_stdio(0);cin.tie(0);cout.tie(0)
#define For(i,j,k) for(int i=j;i<=k;i++)
#define Rof(i,j,k) for(int i=j;i>=k;i--)
#define int long long
#define N 100010
#define C 16
struct edge{
	int u,v,w;
	friend bool operator<(edge x,edge y){return x.w<y.w;}
}e[N];
int n,m,q,f[N][C+1],rt[N],tot,a[N],dep[N],son[N][2];
inline int grt(int x){return rt[x]==x?x:rt[x]=grt(rt[x]);}
void dfs(int rt){
	For(i,1,C) f[rt][i]=f[f[rt][i-1]][i-1];
	if(rt<=n) return ;
	dep[son[rt][0]]=dep[son[rt][1]]=dep[rt]+1;
	dfs(son[rt][0]);
	dfs(son[rt][1]);
} 
int lca(int x,int y){
	int xx,yy;
	if(dep[x]<dep[y]) swap(x,y);
	Rof(i,C,0){
		xx=f[x][i];
		if(dep[xx]>=dep[y]) x=xx;
	}
	if(x==y) return x;
	Rof(i,C,0){
		xx=f[x][i];
		yy=f[y][i];
		if(xx!=yy){
			x=xx;
			y=yy;
		}
	}
	return f[x][0];
}
signed main(){IOS;
	int x,y;
	bool flag=0;
	while(cin>>n>>m){
		if(flag) cout<<endl;
		flag=1;
		tot=n;
		For(i,1,2*n) f[i][0]=0,rt[i]=i,a[i]=0;
		For(i,1,m) cin>>e[i].u>>e[i].v>>e[i].w;
		sort(e+1,e+1+m);
		For(i,1,m){
			x=grt(e[i].u);y=grt(e[i].v);
			if(x==y) continue;
			a[++tot]=e[i].w;
			f[x][0]=f[y][0]=rt[x]=rt[y]=tot;
			son[tot][0]=x;
			son[tot][1]=y;
		}
		dep[tot]=1;
		dfs(tot);
		cin>>q;
		while(q--){
			cin>>x>>y;
			cout<<a[lca(x,y)]<<endl;
		}
	}
return 0;}
```