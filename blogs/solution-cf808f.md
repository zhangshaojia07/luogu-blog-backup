# CF808F Card Game（Dinic 二分图最大权独立集）

~~5ab 写错 Dinic 当前弧。~~

二分答案，我们求特定等级能达到的最大能量。

发现「两两和不为素数」条件不太好搞，尝试建图。

若两个卡上魔法值之和为素数，则将两个卡之间连无向边。

我们要求这个图的最大权（权为能量值）独立集。

发现除了 $1+1=2$ 外和为素数的两数一定一奇一偶。

我们把魔法值为 $1$ 的暂且剔除，剩下一定组成**二分图**。

众所周知，二分图最大权独立集是可以用 Dinic $O(n^2m)$ 做的。

最后考虑魔法值 $1$，发现只有可能选一张，我们选能量最大的放进去跑网络流。

```cpp
//We'll be counting stars.
//#pragma GCC optimize("Ofast")
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
#define V 200000
#define N 110
#define M 2610
const int inf=1e15;
int n,p[N],c[N],L[N];
bool vis[V+1];
vector<int> pr;
void init(){
	For(i,2,V){
		if(!vis[i]){
			pr.pb(i);
		}
		for(int j:pr){
			if(i*j>V) break;
			vis[i*j]=1;
			if(i%j==0) break;
		}
	}
}
struct edge{
	int nxt,to,flow;
}e[2*M];
int head[N],cur[N],tot,dis[N];
pair<int,int> q[N];//p c
int lim,S,T;
void adde(int x,int y,int z){
	e[++tot]=(edge){head[x],y,z};head[x]=tot;
	e[++tot]=(edge){head[y],x,0};head[y]=tot;
}
queue<int> Q;
bool bfs(){
	fill(dis+1,dis+T+1,0);
	dis[S]=1;
	Q.push(S);
	int x;
	while(!Q.empty()){
		x=Q.front();
		Q.pop();
		cur[x]=head[x];
		for(int i=head[x],to;i;i=e[i].nxt){
			to=e[i].to;
			if(!e[i].flow || dis[to]) continue;
			dis[to]=dis[x]+1;
			Q.push(to);
		}
	}
//	For(i,1,T) cout<<dis[i]<<" "; cout<<endl;
	return dis[T];
}
int dfs(int x,int flow){
	if(x==T) return flow;
	int res=0;
	for(int &i=cur[x],to,tmp;i;i=e[i].nxt){
		to=e[i].to;
		if(dis[to]!=dis[x]+1 || !e[i].flow) continue;
		tmp=dfs(to,min(flow,e[i].flow));
		e[i].flow-=tmp;
		e[i^1].flow+=tmp;
		res+=tmp;
		flow-=tmp;
		if(!flow) break;
	}
	return res;
}
int work(int val,bool one){
	lim=0;
	For(i,1,n) if(L[i]<=val && c[i]!=1 && (!one || vis[c[i]+1])) q[++lim]=mkp(p[i],c[i]);
	S=lim+1;T=lim+2;
	fill(head+1,head+T+1,0);
	tot=1;
//	For(i,1,lim) cout<<q[i].fir<<"%"<<q[i].sec<<endl;
	For(i,1,lim) For(j,i+1,lim) if(!vis[q[i].sec+q[j].sec]){
//		cout<<i<<"^"<<j<<endl;
		if(q[i].sec&1) adde(i,j,inf);
		else adde(j,i,inf);
	}
	For(i,1,lim)
		if(q[i].sec&1) adde(S,i,q[i].fir);
		else adde(i,T,q[i].fir);
	int res=0;
	For(i,1,lim) res+=q[i].fir;
	while(bfs()) res-=dfs(S,inf);
//	cout<<val<<"~"<<one<<":"<<res<<endl;
	return res;
} 
int32_t main(){
	ios::sync_with_stdio(false),cin.tie(nullptr);
	init();
	int k,l=1,r=0,mid,res=-1;
	cin>>n>>k;
	For(i,1,n) cin>>p[i]>>c[i]>>L[i];
	For(i,1,n) ckmx(r,L[i]);
	int p1;
	while(l<=r){
		mid=(l+r)>>1;
		p1=0;
		For(i,1,n) if(c[i]==1 && L[i]<=mid) ckmx(p1,p[i]);
		if(work(mid,0)>=k || (p1 && work(mid,1)>=k-p1)){
			res=mid;
			r=mid-1;
		}else{
			l=mid+1;
		}
	}
	cout<<res<<endl;
return 0;}
```