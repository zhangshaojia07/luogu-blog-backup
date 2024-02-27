# P8097 [USACO22JAN] Farm Updates G

### Analysis

发现 A 操作只会两个活跃的农场之间添加一条道路，也就是说，把操作序列 reverse，A 操作只会断开两个活跃的农场之间的一条道路，发现对最终答案不影响。

发现操作 reverse 后只有两种操作：

* D 启用一个农场。

* R 在两个农场之间添加一条道路。

发现这不是并查集模板题吗？/xyx

因为在任意时刻，一个并查集内的点是否「有关」的状态是相同的，所以在根上标记一下即可。

当 「有关」并查集与「无关」并查集合并 或 启用一个农场 时，从根 DFS（所以每个点要用 vector 存儿子集）记录并查集内每个点的最晚时间为当前时刻 $-1$。

本蒟蒻认为本题不用路径压缩而用按秩合并（小的合到大的下面）$O(n\log n)$ 更方便（因为存儿子集合这事让改变树形态这件事变得麻烦）（虽然链表能做到 $O(n)$）。

### Code

```cpp
//Said no more counting dollars. We'll be counting stars.
#include<bits/stdc++.h>
using namespace std;
#define IOS ios::sync_with_stdio(0);cin.tie(0);cout.tie(0)
#define fir first
#define sec second
#define mkp make_pair
#define pb emplace_back
#define For(i,j,k) for(int i=j;i<=k;i++)
#define Rof(i,j,k) for(int i=j;i>=k;i--)
#define N 200010

vector<pair<int,int> > r; 

struct node{
	bool ca;
	int num,time;
};
vector<node> op;
vector<int> e[N];
vector<bool> pan;
char ch;
int f[N],sz[N],n,q;
bool l[N];
int ans[N];
inline int gf(int x){return x==f[x]?x:gf(f[x]);}
inline void dfs(int x,int time){
	ans[x]=time;
	for(int i:e[x]) dfs(i,time);
}
signed main(){IOS;
	cin>>n>>q;
	For(i,1,n) f[i]=i,sz[i]=1,l[i]=1;
	int x,y;
	r.pb(mkp(0,0));
	pan.pb(0);
	For(i,1,q){
		cin>>ch;
		if(ch=='A'){
			cin>>x>>y;
			r.pb(mkp(x,y));
			pan.pb(1);
			continue;
		}
		cin>>x;
		op.pb((node){ch=='D',x,i});
		if(ch=='D') l[x]=0;
		else pan[x]=0;
	}
	int len=r.size();
	For(i,0,len-1) if(pan[i]) op.pb((node){0,i,q+1});
	For(i,1,n) if(l[i]) ans[i]=q;
	reverse(op.begin(),op.end());
	for(auto i:op){
		if(i.ca){
			x=gf(i.num);
			if(!l[x]) l[x]=1,dfs(x,i.time-1);
		}else{
			x=gf(r[i.num].fir);
			y=gf(r[i.num].sec);
			if(x==y) continue;
			if(!l[x] && l[y]) dfs(x,i.time-1);
			if(l[x] && !l[y]) dfs(y,i.time-1);
			if(sz[x]<sz[y]) swap(x,y);
			f[y]=x;
			e[x].pb(y);
			l[x]|=l[y];
			sz[x]+=sz[y];
		}
	}
	For(i,1,n) cout<<ans[i]<<endl;
return 0;}
```