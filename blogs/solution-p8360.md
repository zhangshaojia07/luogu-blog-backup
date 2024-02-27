# P8360 [SNOI2022] 军队（分块+块内同颜色建树）

一眼看题发现 polylog 不可做（若可以请以任何方式通知我），考虑分块。

对于 1 操作想到 dsu，但是 2 操作否认了这一个想法。

我们可以将 dsu 转成二叉树，然后 2 操作打懒标记。

对于两边的散块，我们暴力拆解重构。

拆解时遍历每一棵树，下传懒标记，转成 $\{a\},\{c\}$ 两个序列的形式。

实现要达到 $O(\sqrt n)$ 需要深思。

对于 3 询问，我们记录每一个整块的和，散块暴力拆解 $\to$ 询问 $\to$ 重构。

总复杂度 $O(n\sqrt n)$（$n,q$ 同阶）。

对于空间，发现出题人卡 $O(n\sqrt n)$，导致我这个空间卡了一下午无法通过。

既然在线空间不行则转为离线，对于每一个块分别计算答案，统计在一起即可。

空间 $O(n)$，这代码非常难调（我菜），可把我写吐血了，下次不轻易写毒瘤分块题了。

```cpp
/*
* Author: ShaoJia
* Last Modified time: 2022-09-09 22:14:25
* Motto: We'll be counting stars.
*/
#include<bits/stdc++.h>
using namespace std;
#define For(i,j,k) for(int i=(j);i<=(k);i++)
#define Rof(i,j,k) for(int i=(j);i>=(k);i--)
#define ll long long
char buf[1<<21],*p1,*p2;
#define gc() (p1==p2&&(p2=(p1=buf)+fread(buf,1,1<<21,stdin),p1==p2)?EOF:*p1++)
inline int read() {
	int x=0,f=1;
	char c=gc();
	while(c<'0'||c>'9'){if(c=='-')f=-1;c=gc();}
	while(c>='0'&&c<='9'){x=(x<<3)+(x<<1)+(c^48);c=gc();}
	return x*f;
}
const int N=250002,gap=500;
struct Que{
	int opt,l,r,x,y;
}Q[N];
struct node{
	int ls,rs,sz,pos;
	ll val;
	node(){}
	node(int k1,int k2,int k3,int k4,ll k5){ ls=k1,rs=k2,sz=k3,pos=k4,val=k5; }
}t[gap<<1];
int n,q,C,c[N],lim,L,R,tot,root[N],s[N],st;
ll a[N],ans[N],sum;
bool vis[N];
inline int merge(int x,int y){
	if(!x || !y) return x^y;
	t[++tot]=node(x,y,t[x].sz+t[y].sz,0,0);
	return tot;
}
void build(){
	sum=0;
	For(i,L,R){
		t[++tot]=node(0,0,1,i,a[i]);
		if(!vis[c[i]]){
			vis[c[i]]=true;
			s[++st]=c[i];
			root[c[i]]=tot;
		}else{
			root[c[i]]=merge(tot,root[c[i]]);
		}
		sum+=a[i];
	}
}
inline void dfs(int rt,int col){
	if(!t[rt].ls){
		a[t[rt].pos]=t[rt].val;
		c[t[rt].pos]=col;
		return ;
	}
	int ls=t[rt].ls,rs=t[rt].rs;
	t[ls].val+=t[rt].val;
	t[rs].val+=t[rt].val;
	dfs(ls,col);
	dfs(rs,col);
}
void ruin(){
	For(i,1,st){
		if(root[s[i]]){
			dfs(root[s[i]],s[i]);
			root[s[i]]=0;
		}
	}
	For(i,1,st) 
		vis[s[i]]=false;
	st=tot=0;
}
void Chan(int x,int y){
	if(!root[x] || x==y) return ;
	root[y]=merge(root[y],root[x]);
	root[x]=0;
	if(!vis[y]){//!!!
		s[++st]=y;
		vis[y]=true;
	}
}
void chan(int l,int r,int x,int y){
	ruin();
	For(i,l,r) if(c[i]==x) c[i]=y;
	build();
}
void Add(int x,ll y){
	if(root[x]){
		t[root[x]].val+=y;
		sum+=t[root[x]].sz*y;
	}
}
void add(int l,int r,int x,ll y){
	ruin();
	For(i,l,r) if(c[i]==x) a[i]+=y;
	build();
}
ll que(int l,int r){
	ruin();
	ll res=0;
	For(i,l,r) res+=a[i];
	build();
	return res;
}
void solve(){
	tot=0;
	build();
	int l,r;
	For(i,1,q){
		l=max(Q[i].l,L);
		r=min(Q[i].r,R);
		if(l>r) continue;
		if(L==l && R==r){//all
			if(Q[i].opt==1) Chan(Q[i].x,Q[i].y);
			else if(Q[i].opt==2) Add(Q[i].x,Q[i].y);
			else ans[i]+=sum;
		}else{//some
			if(Q[i].opt==1) chan(l,r,Q[i].x,Q[i].y);
			else if(Q[i].opt==2) add(l,r,Q[i].x,Q[i].y);
			else ans[i]+=que(l,r);
		}
	}
	ruin();
}
signed main(){
	n=read(),q=read(),C=read();
	For(i,1,n) a[i]=read();
	For(i,1,n) c[i]=read();
	lim=(n-1)/gap+1;
	For(i,1,q){
		Q[i].opt=read(),Q[i].l=read(),Q[i].r=read();
		if(Q[i].opt!=3) Q[i].x=read(),Q[i].y=read();
	}
	For(i,1,lim) L=1+(i-1)*gap,R=min(i*gap,n),solve();
	For(i,1,q) if(Q[i].opt==3) printf("%lld\n",ans[i]);
return 0;}
```