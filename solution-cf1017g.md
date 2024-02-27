# CF1017G The Tree（树剖 DFS 序同时处理子树修）

我们称 1 操作为「晕开」，2 操作为「清空」。

发现朴素晕开的时间复杂度我们受不了 qwq，尝试用树剖方式与询问摊一下。

若在一个位置晕开一次黑色，我们就单点加 $1$，若所有的初始点权均为 $-1$，则单点询问 $x$ 时相当于问：

从根到 $x$ 路径上的点权的最大后缀和（不可空）是否 $\ge 0$。

原因是每个点要消耗一个单位，所以初始值为 $-1$，而每次晕开相当于向下多扩展 $1$。

清空 $x$ 如何？首先我们要让 $x$ 子树内的点权均清空为 $-1$，然后要阻断此时从上层来的墨量，所以用 $x$ 「吸收」，即 $x$ 点权再减 $x$ 处的询问值再 $-1$。

询问则树剖跳链，线段树维护区间和，区间最大后缀和即可。

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
#define debug(...) cerr<<"#"<<__LINE__<<": "<<__VA_ARGS__<<endl
#define ll long long
const ll mod=1;
inline ll pw(ll x,ll y){ll r=1;while(y){if(y&1)r=r*x%mod;x=x*x%mod;y>>=1;}return r;}
#define int ll
#define N 100005
struct node{
	int sum,ans;
	node(int s=0,int a=0){ sum=s,ans=a; }//ans(suf) can't be empty
	friend node operator+(node x,node y){ return node(x.sum+y.sum,max(y.ans,x.ans+y.sum)); }
}t[N<<2];
bool lz[N<<2];
vector<int> e[N];
int n,q,sz[N],wson[N],dfn[N],top[N],f[N],tim=0;
void dfs(int rt,int fa){
	sz[rt]=1;
	f[rt]=fa;
	for(int i:e[rt]) if(i!=fa){
		dfs(i,rt);
		sz[rt]+=sz[i];
		if(sz[i]>sz[wson[rt]]) wson[rt]=i;
	}
}
void dfs2(int rt,int tp){
	top[rt]=tp;
	dfn[rt]=++tim;
	if(wson[rt]) dfs2(wson[rt],tp);
	for(int i:e[rt]) if(i!=f[rt] && i!=wson[rt]){
		dfs2(i,i);
	}
}
#define ls (rt<<1)
#define rs (rt<<1|1)
#define mid ((l+r)>>1)
void build(int rt,int l,int r){
	lz[rt]=false;
	if(l==r){
		t[rt]=node(-1,-1);
		return ;
	}
	build(ls,l,mid);
	build(rs,mid+1,r);
	t[rt]=t[ls]+t[rs];
}
void tag(int rt,int l,int r){
	lz[rt]=true;
	t[rt]=node(-(r-l+1),-1);
}
void pd(int rt,int l,int r){
	if(lz[rt]){
		lz[rt]=false;
		tag(ls,l,mid);
		tag(rs,mid+1,r);
	}
}
void add(int rt,int l,int r,int id,int val){
	if(l==r){
		t[rt].sum+=val;
		t[rt].ans+=val;
		return ;
	}
	pd(rt,l,r);
	if(id<=mid) add(ls,l,mid,id,val);
	else add(rs,mid+1,r,id,val);
	t[rt]=t[ls]+t[rs];
}
void cle(int rt,int l,int r,int x,int y){//clean to val -1
	if(x<=l && r<=y){
		tag(rt,l,r);
		return ;
	}
	pd(rt,l,r);
	if(x<=mid) cle(ls,l,mid,x,y);
	if(y>mid) cle(rs,mid+1,r,x,y);
	t[rt]=t[ls]+t[rs];
}
node query(int rt,int l,int r,int x,int y){
	if(l==x && r==y) return t[rt];
	pd(rt,l,r);
	if(y<=mid) return query(ls,l,mid,x,y);
	else if(x>mid) return query(rs,mid+1,r,x,y);
	else return query(ls,l,mid,x,mid)+query(rs,mid+1,r,mid+1,y);
}
int Que(int x){
	node res=query(1,1,n,dfn[top[x]],dfn[x]);
	x=f[top[x]];
	while(x){
		res=query(1,1,n,dfn[top[x]],dfn[x])+res;
		x=f[top[x]];
	}
	return res.ans;
}
signed main(){ios::sync_with_stdio(false),cin.tie(nullptr);
	cin>>n>>q;
	int opt,x;
	For(i,2,n){
		cin>>x;
		e[x].pb(i);
		e[i].pb(x);
	}
	dfs(1,0);
	dfs2(1,1);
	build(1,1,n);
	For(i,1,q){
		cin>>opt>>x;
		if(opt==1){
			add(1,1,n,dfn[x],1);
		}else if(opt==2){
			cle(1,1,n,dfn[x],dfn[x]+sz[x]-1);
			add(1,1,n,dfn[x],-1-Que(x));//make this node's max suf=-1
		}else{
			cout<<(Que(x)>=0?"black":"white")<<endl;
		}
	}
return 0;}
```