# P8238 [AGM 2022 资格赛] 避难所（线段树）

### Preface

不好意思，把最优解抢了（doge）。

### Analysis

看人家扫描线 / 线段树合并 / 分块的，普通线段树不香吗……

时间复杂度 $O(n+q\log n)$。

将树作 DFS 序。维护每个节点到根节点被封闭的路段数量。

每个点有两个属性：权重（人口）、值（到根节点被封闭的路段数）。

这样问题就转化为线段树基本的

* 区间加值（DFS 序子树连续），保证任意时刻值 $\ge 0$（封的道路数非负）。

* 单点改权重。

* 询问全体值为 $0$ 的权重和。

具体地，线段树节点维护三个值 $lz,mn,cnt$，表示 区间加的懒标记、区间最小值、取到区间最小值元素的权重和。

若线段树根节点 $mn>0$ 则答案为 $0$，否则为 $cnt$。

### Code

```cpp
//Said no more counting dollars. We'll be counting stars.
#include<bits/stdc++.h>
using namespace std;
#define For(i,j,k) for(int i=j;i<=k;i++)
#define N 100002
#define ll long long
#define gc getchar
inline int read(){
	int x=0;char c=gc();bool f=0;
	while(!isdigit(c)){if(c=='-')f=1;c=gc();}
	while(isdigit(c)){x=(x<<3)+(x<<1)+c-48;c=gc();}
	if(f)x=-x;return x;
}
struct edge{int to,nxt;}e[2*N];
int head[N],tot=0;
struct node{
	int lz,mn;ll cnt;
	friend node operator+(node x,node y){
		if(x.mn==y.mn) return (node){0,x.mn,x.cnt+y.cnt};
		else if(x.mn<y.mn) return (node){0,x.mn,x.cnt};
		else return (node){0,y.mn,y.cnt};
	}
}t[4*N];
int n,q,a[N],id[N],L[N],R[N];
int tim=0;
bool f[N];
void dfs(int rt,int fa){
	id[++tim]=rt;
	L[rt]=tim;
	for(int i=head[rt];i;i=e[i].nxt) if(e[i].to!=fa) dfs(e[i].to,rt);
	R[rt]=tim;
}
#define mid ((l+r)>>1)
#define ls (rt<<1)
#define rs (rt<<1|1)
void build(int rt,int l,int r){
	if(l==r){
		t[rt]=(node){0,0,a[l]};
		return ;
	}
	build(ls,l,mid);
	build(rs,mid+1,r);
	t[rt]=t[ls]+t[rs];
}
void pd(int rt){
	t[ls].lz+=t[rt].lz;t[ls].mn+=t[rt].lz;
	t[rs].lz+=t[rt].lz;t[rs].mn+=t[rt].lz;
	t[rt].lz=0;
}
void modi(int rt,int l,int r,int x,int y){
	if(l==r){
		t[rt].cnt=y;
		return ;
	}
	pd(rt);
	if(x<=mid) modi(ls,l,mid,x,y);
	else modi(rs,mid+1,r,x,y);
	t[rt]=t[ls]+t[rs];
}
void add(int rt,int l,int r,int x,int y,int val){
	if(l==x && r==y){
		t[rt].lz+=val;
		t[rt].mn+=val;
		return ; 
	}
	pd(rt);
	if(y<=mid) add(ls,l,mid,x,y,val);
	else if(x>mid) add(rs,mid+1,r,x,y,val);
	else add(ls,l,mid,x,mid,val),add(rs,mid+1,r,mid+1,y,val); 
	t[rt]=t[ls]+t[rs];
}
int main(){
	n=read();q=read();
	int opt,x,y;
	For(i,1,n-1){
		x=read();y=read();
		e[++tot]=(edge){y,head[x]};head[x]=tot;
		e[++tot]=(edge){x,head[y]};head[y]=tot;
	}
	dfs(1,0);
	For(i,1,n) a[L[i]]=read();
	build(1,1,n); 
	while(q--){
		opt=read();
		if(opt==1){
			x=read();y=read();
			modi(1,1,n,L[x],y);
		}else{
			x=read();
			if(f[x]) add(1,1,n,L[x],R[x],-1);
			else add(1,1,n,L[x],R[x],1);
			f[x]^=1;
		}
		if(t[1].mn!=0) puts("0");
		else printf("%lld\n",t[1].cnt);
	}
return 0;}
```