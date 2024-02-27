# CF1679D Toss a Coin to Your Graph...（有向图最长路）

### Analysis

最大值最小？二分答案。

设当前二分答案到 $A$。

我们只保留有向图中点权 $\le A$ 构成的诱导子图。

接下来就是求此图的最长路（或者有环的话报告无穷），若最长路点数 $\ge k$ 则可行，否则不行。

这里我们用线性复杂度的记忆化搜索解决。

设 $f_i$ 表示从 $i$ 出发最多能经过几个点，$g_i$ 表示当前是（$1$）否（$0$）在 DFS 的栈中。

当前遍历到 $x$ 节点，遍历到 $x\to y$ 这条边。

若 $g_y$，则 $f_x=\infty$。

否则 DFS $y$ 节点，然后 $f_x=1+\max\limits_{x\to y}f_y$。

总时间 $O((n+m)\log n)$

（不过我 code 写的复杂度 $\log$ 值域的，无伤大雅）

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
#define N 200010
const int inf=2e18;
int n,a[N],f[N],m,k,tar;
vector<int> e[N];
void dfs(int x){
	f[x]=-1;
	int res=0;
	for(int i:e[x]){
		if(a[i]>tar) continue;
		if(f[i]==-1){
			f[x]=inf;
			return ;
		}
		if(!f[i]) dfs(i);
		ckmx(res,f[i]);
	}	
	f[x]=res+1;
}
bool check(){
	For(i,1,n) f[i]=0;
	int res=0;
	For(i,1,n) if(a[i]<=tar && !f[i]) dfs(i);
	For(i,1,n) ckmx(res,f[i]);
	return res>=k;
}
signed main(){IOS;
	cin>>n>>m>>k;
	For(i,1,n) cin>>a[i];
	int x,y;
	For(i,1,m){
		cin>>x>>y;
		e[x].pb(y);
	}
	int l=1,r=1e9,res=-1;
	while(l<=r){
		tar=(l+r)>>1;
		if(check()){
			res=tar;
			r=tar-1;
		}else{
			l=tar+1;
		}
	}
	cout<<res<<endl;
return 0;}
```