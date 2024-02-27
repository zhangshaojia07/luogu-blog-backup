# CF1682E Unordered Swaps（置换环还原）

### Analysis

**注意：** 这篇文章可能略长，但是大多是推论及证明，最终实现不难。

首先，设置换环的个数为 $c$，则 Alice 的最小交换次数为 $n-c$，证明略。

进一步地，Alice 不会 swap 两个不同置换环上的元素。

所以，我们可以将每一个置换环分开求解。设当前枚举到的环长为 $L$，则 Alice 在这里 swap 了 $L-1$ 次，若将 swap(x,y) 变为 $x,y$ 节点之间的无向边，则构成一棵树。

所以问题被我们转化为：

**给定一棵树，点编号 $1\to L$，点上有点权（记为 $a_i$），值域是 $1\to n$ 且互不相同。每次操作可以删掉一条边，并将两端的点权交换。求一个操作的顺序，使得最后每个点的点权等于点编号。**

这个问题可以 $O(L\log L)$ 实现。

#### （一）

将这颗树**随意定根**，求出每个点的**深度**，$O(L\log L)$ **预处理树上倍增**。

这样我们就可以 $O(\log L)$ 实现两种操作：

1. 判断一个点 $x$ 是否在另一个点 $y$ 的子树里。

2. 若 1. 满足，求出 $x$ 在 $y$ 的哪个儿子的子树中。

#### （二）

我们称一条边 $(x,y)$（不妨设 $x$ 是 $y$ 的父亲）「濒死」的当且仅当节点 $a_x$ 在 $y$ 的子树内且 $a_y$ 不在 $y$ 的子树内。

* * *

**引理 A：** 一条「濒死」应当尽可能操作掉。

**证明：**

因为 $a_x$ 要到 $y$ 那头，并且 $a_y$ 要到 $x$ 那头，既然两边都准备好了，那还等啥。

**引理 B：** 在任一时刻，两条「濒死」边不可能用公共顶点。

**证明：**

反证法，设存在 $(x,y),(y,z)$ 均为「濒死」边。

则 $a_y$ 既要在 $x$ 那头，又要在 $z$ 那头~~很蓝的啦~~，所以矛盾，原命题成立。

* * *

由引理 A，**遍历每一条边，若这条边「濒死」则加入队列。**

**然后依次将队列中的边操作掉，然后判断与这条边有公共顶点的边是否「濒死」，若是则加入队列。**

由引理 B，在队列中的「濒死」边不可能变回「不濒死」的。并且 Alice 给的一定合法，所以证明了正确性。

算法总时间 $O(n\log n)$。

### Code

没有压行，略显冗长。

```cpp
//Said no more counting dollars. We'll be counting stars.
#pragma GCC optimize("Ofast")
#include<bits/stdc++.h>
using namespace std;
#define IOS ios::sync_with_stdio(0);cin.tie(0);cout.tie(0)
#define fir first
#define sec second
#define mkp make_pair
#define pb emplace_back
#define For(i,j,k) for(int i=j;i<=k;i++)
#define N 200010
#define C 17
int n,m,a[N],f[N][C+1],id[N],dep[N];
vector<pair<int,int> > e[N];
pair<int,int> g[N];
queue<int> q;
bool vis[N];
void dfs(int rt,int fa){
	dep[rt]=dep[fa]+1;
	f[rt][0]=fa; For(i,1,C) f[rt][i]=f[f[rt][i-1]][i-1];
	vis[rt]=1;
	for(auto i:e[rt]) if(i.fir!=fa) id[i.fir]=i.sec,dfs(i.fir,rt);
}
inline int up(int x,int y){//return the root of subtree of y that contains x
	int z=dep[x]-dep[y]-1;
	For(i,0,C) if(z&(1<<i)) x=f[x][i];
	return x;
}
inline bool pan(int x,int y){//Is x in the subtree rooted y?
	int z=dep[x]-dep[y];
	if(z<0) return false;
	For(i,0,C) if(z&(1<<i)) x=f[x][i];
	return x==y;
}
void dfs2(int rt,int fa){
	for(auto i:e[rt]) if(i.fir!=fa){
		if(pan(a[rt],i.fir) && !pan(a[i.fir],i.fir)) q.push(i.sec);
		dfs2(i.fir,rt);
	}
}
void check(int x){//return a[x] home
	if(a[x]==x) return ;
	if(pan(a[x],x)){
		int y=up(a[x],x);
		if(!pan(a[y],y)) q.push(id[y]);
	}else if(f[x][0]!=0){
		if(pan(a[f[x][0]],x)) q.push(id[x]);
	}
}
void work(int x){
	dfs(x,0);dfs2(x,0);
	while(!q.empty()){
		x=q.front();q.pop();
		cout<<x<<" ";
		swap(a[g[x].fir],a[g[x].sec]);
		check(g[x].fir);check(g[x].sec); 
	} 
}
signed main(){IOS;
	cin>>n>>m;
	For(i,1,n) cin>>a[i]; 
	int x,y;
	For(i,1,m){
		cin>>x>>y;
		e[x].pb(mkp(y,i));
		e[y].pb(mkp(x,i));
		g[i]=mkp(x,y);
	}
	For(i,1,n) if(!vis[i]) work(i);
return 0;}
```