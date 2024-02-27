# CF1709E XOR Tree（dsu on tree + set）

若是「边权异或和为 $0$」的话，我们只要记录每个点到根节点的路径异或和为 $d_x$，两点间路径异或和即为 $d_x\oplus d_y$。

但是现在是点权，非常难搞。

我们称一条路径是非法的当且仅当路径上点权异或和为 $0$。

设 $a_x$ 为点权，$b_x$ 为 $x$ 到根的点权异或和，则 $x,y$ 路径上点权异或和为 $b_x\oplus b_y\oplus a_{lca(x,y)}$。

我们注意到题目中将点重新赋值相当于删除点，分成多个子树，其中不存在非法路径。

我们考察所有儿子均已做完的节点 $x$。

由于儿子均已做完，以 $x$ 为根的子树中只可能有经过 $x$ 的路径非法。

设 $S_x$ 表示 $x$ 的子树中到根没有被删除的点的 $b$ 值集合。

考虑启发式合并 $S_y(\forall y\in son_x)$，若有非法路径经过 $x$，则 $Ans\leftarrow Ans+1$（$x$ 节点被删去），然后清空 $S_x$。

时间 $O(n\log^2n)$。

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
#define N 200010
int n,a[N],b[N],root[N],ans;
set<int> s[N];
vector<int> e[N];
void dfs(int rt,int fa,int val){
	b[rt]=a[rt]^val;
	s[root[rt]].insert(b[rt]);
	bool flag=0;
	for(int i:e[rt]) if(i!=fa){
		dfs(i,rt,b[rt]);
		if(s[root[rt]].size()<s[root[i]].size()) swap(root[rt],root[i]);
		for(int j:s[root[i]]) if(s[root[rt]].find(a[rt]^j)!=s[root[rt]].end()) flag=1;
		for(int j:s[root[i]]) s[root[rt]].insert(j);
	}
	if(flag){
		ans++;
		s[root[rt]].clear();
	}
}
signed main(){
	ios::sync_with_stdio(false),cin.tie(nullptr);
	cin>>n;
	iota(root+1,root+1+n,1);
	For(i,1,n) cin>>a[i];
	int x,y;
	For(i,1,n-1){
		cin>>x>>y;
		e[x].pb(y);
		e[y].pb(x);
	}
	dfs(1,0,0);
	cout<<ans<<endl;
return 0;}
```