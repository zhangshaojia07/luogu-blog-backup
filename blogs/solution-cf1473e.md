# CF1473E Minimum Path（拆点 dij）

### Analysis

为了让最短路最短，边权不计算的一定是 $\max w_{e_i}$，双倍的一定是 $\min w_{e_i}$，所以我们将题目中的路径 weight 更改为

$$\sum\limits_{i=1}^{k}{w_{e_i}}-w_{e_x}+w_{e_y},\forall 1\le x,y\le k$$

不影响结果。

也就是说，**在路径上任意不选一条边权，任意一条双倍，求最短路。**

这可以用**拆点 Dijkstra** 解决。

具体地，将每个点拆成 $4$ 个，分别表示到这里 有/没有 不选某条边权，有/没有 双倍某条边权。

然后对于原图中的某条边 $(x,y)$，这样建图：

![](https://cdn.luogu.com.cn/upload/image_hosting/q1t6alsf.png)

↑建图方式（当然 $y\to x$ 要再建一遍对称的）

然后跑 Dij 即可 $O(n\log n+m)$ 解决。

### Code

```cpp
//Said no more counting dollars. We'll be counting stars.
#pragma GCC optimize("Ofast")
//#pragma GCC optimize("unroll-loops")//DONT use rashly,I have suffered
//#pragma GCC target("sse,sse2,sse3,ssse3,sse4,popcnt,abm,mmx,avx,avx2,tune=native")//DONT use rashly,I have suffered
#include<bits/stdc++.h>
using namespace std;
#define IOS ios::sync_with_stdio(0);cin.tie(0);cout.tie(0)
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
#define N 800010

const int inf=1e15;
int n,m,dis[N];
vector<pair<int,int> > e[N];
inline int num(int x,int y){return (x-1)*4+y;}
inline void adde(int x,int y,int z){
	For(i,1,4) e[num(x,i)].pb(mkp(num(y,i),z));
	e[num(x,1)].pb(mkp(num(y,2),2*z));
	e[num(x,1)].pb(mkp(num(y,3),0));
	e[num(x,1)].pb(mkp(num(y,4),z));
	e[num(x,2)].pb(mkp(num(y,4),0));
	e[num(x,3)].pb(mkp(num(y,4),2*z));
}
struct node{
	int id,dis;
	friend bool operator<(node x,node y){return x.dis>y.dis;}
};
priority_queue<node> q;
signed main(){IOS;
	cin>>n>>m;
	int x,y,z;
	For(i,1,m){
		cin>>x>>y>>z;
		adde(x,y,z);
		adde(y,x,z);
	}	
	For(i,1,4*n) dis[i]=inf;
	dis[num(1,1)]=0;
	q.push((node){num(1,1),0});
	int to,val;
	while(!q.empty()){
		x=q.top().id;
		y=q.top().dis;
		q.pop();
		if(dis[x]!=y) continue;
		for(auto i:e[x]){
			to=i.fir;val=i.sec;
			if(dis[to]<=dis[x]+val) continue;
			dis[to]=dis[x]+val;
			q.push((node){to,dis[to]});	
		}
	}
	For(i,2,n) cout<<dis[num(i,4)]<<" "; cout<<endl;
return 0;}
```