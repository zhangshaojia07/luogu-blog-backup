# CF1250N Wires

似乎还没有人写题解……

设初始连通块数为 $cnt$，似乎很显然最终答案一定是 $cnt-1$：

* 由于最后要连通，所以答案 $\ge cnt-1$。

* 接下来我们构造一组 $cnt-1$ 的方案。

我们对于每一个连通块随便找一个生成树，每一棵生成树随便找一个叶子，得知我们将这个叶子随意拖动都不会分裂这个本来的连通块，所以我们将 $cnt-2$ 个叶子连到剩下那个连通块的任意一个点即可。

```cpp
//We'll be counting stars.
#pragma GCC optimize("Ofast")
#include<bits/stdc++.h>
using namespace std;
#define fir first
#define sec second
#define mkp make_pair
#define pb emplace_back
#define For(i,j,k) for(int i=(j),i##_=(k);i<=i##_;i++)
#define Rof(i,j,k) for(int i=(j),i##_=(k);i>=i##_;i--)
#define ckmx(a,b) a=max(a,b)
#define ckmn(a,b) a=min(a,b)
#define Fi(s) freopen(s,"r",stdin)
#define Fo(s) freopen(s,"w",stdout)
#define Fre(s) Fi(s".in"),Fo(s".out")
#define debug(...) cerr<<"#"<<__LINE__<<": "<<__VA_ARGS__<<endl
#define ll long long
const ll mod=1;
inline ll pw(ll x,ll y){ll r=1;while(y){if(y&1)r=r*x%mod;x=x*x%mod;y>>=1;}return r;}
#define int ll
#define pi pair<int,int>
#define N 100010
pi a[N];
vector<pi> e[2*N];
int b[2*N],n,lim,lst,id;
bool v[2*N];
int f[2*N],rep[2*N],ed[2*N];
vector<pi> ans;
inline int gf(int x){
	return x==f[x]?x:f[x]=gf(f[x]);
}
void dfs(int x){
	v[x]=1;
	lst=x;
	for(auto i:e[x]) if(!v[i.fir]) id=i.sec,dfs(i.fir);
}
void work(){
	cin>>n;
	For(i,1,n) cin>>a[i].fir>>a[i].sec;
	For(i,1,n) b[2*i-1]=a[i].fir,b[2*i]=a[i].sec;
	sort(b+1,b+1+2*n);
	lim=unique(b+1,b+1+2*n)-b-1;
	For(i,1,n)
		a[i].fir=lower_bound(b+1,b+1+lim,a[i].fir)-b,
		a[i].sec=lower_bound(b+1,b+1+lim,a[i].sec)-b;
	For(i,1,lim) e[i].clear();
	For(i,1,n) e[a[i].fir].pb(mkp(a[i].sec,i)),e[a[i].sec].pb(mkp(a[i].fir,i));
	fill(v+1,v+1+lim,false);
	iota(f+1,f+1+lim,1);
	For(i,1,n) f[gf(a[i].fir)]=gf(a[i].sec);
	For(i,1,lim) gf(i);
	For(i,1,lim) if(!v[i]){
		dfs(i);
		rep[f[i]]=lst;
		ed[f[i]]=id;
	}
	ans.clear();
	fill(v+1,v+1+lim,false);
	For(i,1,lim) if(!v[f[i]]){
		v[f[i]]=1;
		ans.pb(mkp(rep[f[i]],ed[f[i]]));
	}
	int k=ans.size()-1;
	cout<<k<<endl;
	For(i,0,k-1) cout<<ans[i].sec<<" "<<b[ans[i].fir]<<" "<<b[ans.back().fir]<<endl;
}
signed main(){
	ios::sync_with_stdio(false),cin.tie(nullptr);
	int T;cin>>T;
	while(T--)work();
return 0;}
```