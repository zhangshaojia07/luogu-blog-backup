# P7834 [ONTAK2010] Peaks 加强版

### Preface

加强版 $=$ 强制在线

做法：$\text{Kruskal}$ 重构树 $+\text{DFS}$ 序 $+$ 主席树

### Analysis

先造出 $\text{Kruskal}$ 重构树，预处理倍增跳祖先，那问题转化成：

**每次给 $x,k$，在线求 $x$ 子树内叶子权值第 $k$ 大。**

我们求出重构树的 $\text{DFS}$ 序，这样就将树上转化成数组问题了，然后就是 [P3834 【模板】可持久化线段树 2](https://www.luogu.com.cn/problem/P3834) 了。

### Code

代码长，毕竟是算法叠加题。

```cpp
//Said no more counting dollars. We'll be counting stars.
#include<bits/stdc++.h>
using namespace std;
#define fir first
#define sec second
#define mkp make_pair
#define pb emplace_back
#define mem(x,y) memset(x,y,sizeof(x))
#define For(i,j,k) for(int i=j;i<=k;i++)
#define Rof(i,j,k) for(int i=j;i>=k;i--)
#define Fe(x,y) for(int x=head[y];x;x=e[x].nxt)
#define ckmx(a,b) a=max(a,b)
#define ckmn(a,b) a=min(a,b)
#define fin(s) freopen(s,"r",stdin)
#define fout(s) freopen(s,"w",stdout)
#define file(s) fin(s".in");fout(s".out")
#define cerr cerr<<'_'
#define debug cerr<<"Passed line #"<<__LINE__<<endl
template<typename T>T ov(T x){cerr<<"Value: "<<x<<endl;return x;}
#define ll long long
const ll mod=1000000007;
inline ll pw(ll x,ll y){ll r=1;while(y){if(y&1)r=r*x%mod;x=x*x%mod;y>>=1;}return r;}
inline void mad(ll &a,ll b){a=(a+b)%mod;while(a<0)a+=mod;}
inline void mmu(ll &a,ll b){a=a*b%mod;while(a<0)a+=mod;}
#define inv(a) pw(a,mod-2)
#define int long long
#define N 100002
#define M 500002
#define C 17
#define gc getchar
#define pc putchar
inline int read(){
	int x=0;char c=gc();bool f=0;
	while(!isdigit(c)){if(c=='-')f=1;c=gc();}
	while(isdigit(c)){x=(x<<3)+(x<<1)+c-48;c=gc();}
	if(f)x=-x;return x;
}
inline void write(int x){if(x<0){pc('-');x=-x;}if(x>9)write(x/10);pc(x%10+'0');}
const int inf=1e17;
#define mid ((l+r)>>1)
//P7834
struct Edge{
	int u,v,val;
	friend bool operator<(Edge x,Edge y){return x.val<y.val;}
}g[M];
struct node{int ls,rs,sz;}t[(2*C+4)*N];
int n,m,q,a[N],f[2*N][C+1],son[2*N][2],Rt[2*N],lim,root[2*N],b[2*N],so[N],lis,tot=0,id[N],L[2*N],R[2*N],tim=0;
inline int grt(int x){return Rt[x]==x?x:Rt[x]=grt(Rt[x]);}
void build(int rt,int l,int r){
	if(l==r) return ;
	build(t[rt].ls=++tot,l,mid);
	build(t[rt].rs=++tot,mid+1,r);
}
void add(int lt,int rt,int l,int r,int id){
	if(l==r){
		t[rt].sz=t[lt].sz+1;
		return ;
	}
	if(id<=mid){
		t[rt].rs=t[lt].rs;
		add(t[lt].ls,t[rt].ls=++tot,l,mid,id);
	}else{
		t[rt].ls=t[lt].ls;
		add(t[lt].rs,t[rt].rs=++tot,mid+1,r,id);
	}
	t[rt].sz=t[t[rt].ls].sz+t[t[rt].rs].sz;
//	cerr<<rt<<":"<<l<<" "<<r<<" "<<id<<":"<<t[rt].sz<<endl;
}
void dfs(int rt){
	L[rt]=tim;
	if(rt>n){
		dfs(son[rt][0]);
		dfs(son[rt][1]);
	}else{
		id[++tim]=rt;
	}
	R[rt]=tim;
}
int que(int lt,int rt,int l,int r,int k){
	if(l==r) return l;
	int rsz=t[t[rt].rs].sz-t[t[lt].rs].sz;
//	cerr<<lt<<" "<<rt<<":"<<l<<"~"<<r<<" "<<k<<" rsz: "<<t[t[rt].rs].sz<<"-"<<t[t[lt].rs].sz<<endl;
	if(rsz>=k) return que(t[lt].rs,t[rt].rs,mid+1,r,k);
	else return que(t[lt].ls,t[rt].ls,l,mid,k-rsz);
}
signed main(){
	n=read();m=read();q=read();
	For(i,1,n) so[i]=a[i]=read();
	sort(so+1,so+1+n);
	lis=unique(so+1,so+1+n)-so-1;
	For(i,1,n) a[i]=lower_bound(so+1,so+1+lis,a[i])-so;
//	For(i,1,n) cerr<<a[i]<<" "; cerr<<endl;
	For(i,1,2*n-1) Rt[i]=i;
	lim=n;
	For(i,1,m) g[i].u=read(),g[i].v=read(),g[i].val=read();
	sort(g+1,g+1+m);
	int x,y,z;
	For(i,1,m){
		x=grt(g[i].u);y=grt(g[i].v);
		if(x==y) continue;
		Rt[x]=Rt[y]=f[x][0]=f[y][0]=++lim;
		son[lim][0]=x;
		son[lim][1]=y;
		b[lim]=g[i].val;
	}
//	For(i,n+1,lim) cerr<<i<<" "<<son[i][0]<<endl<<i<<" "<<son[i][1]<<endl;
	For(j,1,C) For(i,1,lim) f[i][j]=f[f[i][j-1]][j-1];
	b[0]=inf;
	For(i,1,lim) if(f[i][0]==0) dfs(i);
	build(root[0]=++tot,1,lis);
//	For(i,1,n) cerr<<id[i]<<" "; cerr<<endl;
	For(i,1,n){
//		cerr<<i<<"^^^"<<endl;
		add(root[i-1],root[i]=++tot,1,lis,a[id[i]]);
	}
	int ans=0;
	For(i,1,q){
		x=(read()^ans)%n+1;
		y=read()^ans;
		z=(read()^ans)%n+1;
//		cerr<<x<<" "<<y<<" "<<z<<endl;
		Rof(j,C,0) if(b[f[x][j]]<=y) x=f[x][j];
//		cerr<<x<<"##"<<endl;
		if(R[x]-L[x]<z){
			ans=0;
			puts("-1");
		}else{
//			cerr<<L[x]<<"%%%"<<R[x]<<endl;
			ans=so[que(root[L[x]],root[R[x]],1,lis,z)];
			write(ans);pc('\n');
		}
	}
return 0;}
```