# CF1693C Keshi in Search of AmShZ（变态 Dij）

~~蓝题过分了嗷~~

### Preface

首先我们发现这是一种最短路变式，而且题面中的「随机走一条」可以等效为「走最差的一条」。

我们首先温习一下 **Dijkstra** 算法的过程：找到**还没有确定的距离终点最短的点**确定下来，然后扩展。

我们发现这样点是按**距离终点的距离升序**确定下来的。

由于**边权非负**，可得 Dij 的正确性。

由于本题的边权（封路 + 走路）也非负，所以可以用 Dij 的思路。

### Analysis

我们先考察一个点 $x$。假设我们知道了 $x$ 每条出边到达的点的最小距离（封路 + 走路），设为 $c_1\le c_2\le \dots\le c_k$，我们可以求出 $x$ 的最小距离
$$
\min_{i=1}^k(c_i+k-i)
$$
原理是我们贪心封住最小距离最大的那些路。

由于刚才说到点是按距离终点的距离升序确定下来的，所以每次从每一个点扩展到 $x$，这些点按顺序一定是 $c_1,c_2,\dots,c_k$ 对应的点。

所以，我们在 Dij 的同时，记录这个点是被第几次扩展的（即还有多少出度），然后正常 Dij 即可。

### Code

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
#define debug cerr<<"Line#"<<__LINE__<<endl
#define ll long long
const ll mod=1;
inline ll pw(ll x,ll y){ll r=1;while(y){if(y&1)r=r*x%mod;x=x*x%mod;y>>=1;}return r;}
#define int long long
#define N 200010
const int inf=1e9;
struct node{
	int id,dis;
	friend bool operator<(node x,node y){
		return x.dis>y.dis;
	}
};
int dis[N],deg[N],n,m;
priority_queue<node> q;
vector<int> e[N];
int32_t main(){
	ios::sync_with_stdio(false);
	cin.tie(nullptr);
	cin>>n>>m;
	For(i,1,n) dis[i]=inf;
	dis[n]=0;
	int x,y;
	For(i,1,m){
		cin>>x>>y;
		e[y].pb(x);
		deg[x]++;
	}
	q.push({n,dis[n]});
	while(!q.empty()){
		x=q.top().id;
		y=q.top().dis;
		q.pop();
		if(dis[x]!=y) continue;
		for(int i:e[x]){
			if(dis[x]+deg[i]<dis[i]){
				dis[i]=dis[x]+deg[i];
				q.push({i,dis[i]});
			}
			deg[i]--;
		}
	}
	cout<<dis[1]<<endl;
return 0;}
```