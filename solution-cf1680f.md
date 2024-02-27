# CF1680F Lenient Vertex Cover（线段树分治）

来自 [我发的帖](https://www.luogu.com.cn/discuss/439117) [王学逸](https://www.luogu.com.cn/user/317459) 的做法，在此鸣谢。

当然是双 $\log$ 的做法 /qd。

### Analysis

首先，如果原图就是二分图，那直接黑白间隔染色即可。

否则，我们需要找到一条边使得删掉后就是二分图，或者判断没有这样的边。

我们把 $m$ 条边（按任意顺序）排成一行，并构建线段树（实际实现中不用）。

递归判断删掉区间 $[l,r]$ 内的边后剩下的图是否是二分图，这里使用可撤销并查集判断二分图。

具体递归时在进左区间前将右区间的边加进去（记得做完后撤销），如果当前图不是二分图直接 `return`，同理递归右区间。

递归到 $[l,r](l=r)$ 时就找到了删掉一条边是二分图的方案了，下标为 $l(=r)$ 的这条边就是被删的边。

由于可撤销并查集的时间复杂度带 $\log$，线段树每一层的边都会被加入并删除一次，所以最终时间复杂度为 $O(n\log^2 n)$。

### Code

```cpp
#include<bits/stdc++.h>
using namespace std;
#define IOS ios::sync_with_stdio(false),cin.tie(0),cout.tie(0) 
#define For(i,j,k) for(int i=j;i<=k;i++)
#define pi pair<int,int>
#define N 1000010
#define mkp make_pair
#define fir first
#define sec second
#define pb emplace_back
int n,m,ans,col[N],f[N],sz[N],g[N];
pi a[N];
vector<int> E[N];
void dfs(int x,int c){col[x]=c; for(int i:E[x]) if(!col[i]) dfs(i,3-c);}
inline pi gf(int x){//return mkp(id,val)
	if(x==f[x]) return mkp(x,0);
	pi res=gf(f[x]);
	res.sec^=g[x];
	return res;
}
int del[N],tot=0;
inline bool merge(int x,int y){
	int op=1;pi res;
	res=gf(x);op^=res.sec;x=res.fir;
	res=gf(y);op^=res.sec;y=res.fir;
	if(x==y) return op==0;
	if(sz[x]<sz[y]) swap(x,y);
	sz[x]+=sz[y];
	f[y]=x;g[y]=op;
	del[++tot]=y;
	return true;
}
inline void antimerge(){
	int x=del[tot--];
	sz[f[x]]-=sz[x];f[x]=x;
}
void solve(int l,int r){
	if(ans!=-1) return ;
	if(l==r){
		ans=l;
		return ;
	}
	//left
	int re=tot,mid=(l+r)>>1;
	bool flag=1;
	For(i,mid+1,r) if(!merge(a[i].fir,a[i].sec)){flag=0; break;}
	if(flag) solve(l,mid);
	while(re!=tot) antimerge();
	if(ans!=-1) return ;
	//right
	flag=1;
	For(i,l,mid)   if(!merge(a[i].fir,a[i].sec)){flag=0; break;}
	if(flag) solve(mid+1,r);
	while(re!=tot) antimerge();
}
void work(){
	cin>>n>>m;
	For(i,1,m) cin>>a[i].fir>>a[i].sec;
	For(i,1,n) E[i].clear();
	For(i,1,m) E[a[i].fir].pb(a[i].sec),E[a[i].sec].pb(a[i].fir);
	For(i,1,n) col[i]=0;
	dfs(1,1);
	bool flag=1;
	For(i,1,m) if(col[a[i].fir]==col[a[i].sec]) flag=0;
	if(flag){
		cout<<"YES"<<endl; For(i,1,n) cout<<col[i]-1; cout<<endl;
		return ;
	}
	ans=-1;
	For(i,1,n) f[i]=i;
	For(i,1,n) sz[i]=1;
	solve(1,m);
	if(ans==-1){
		cout<<"NO"<<endl;
		return ;
	}
	cout<<"YES"<<endl;
	For(i,1,n) E[i].clear();
	For(i,1,m) if(ans!=i) E[a[i].fir].pb(a[i].sec),E[a[i].sec].pb(a[i].fir);
	For(i,1,n) col[i]=0;
	dfs(1,1);
	if(col[a[ans].fir]==1) For(i,1,n) col[i]=3-col[i];
	For(i,1,n) cout<<col[i]-1; cout<<endl;
}
signed main(){IOS;
	int T;cin>>T;
	while(T--)work();
	return 0;
}
```