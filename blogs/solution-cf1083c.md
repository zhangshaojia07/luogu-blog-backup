# CF1083C Max Mex（线段树）

我们从值的方面考虑，即类似于二分答案 $\text{mex}(V(l))$。

即：我们要维护对于每一个值 $x\in[0,n)$，$[0,x]$ 这些**点权**对应的点是否可能构成一条链。

由于单点修改，我们考虑**线段树**。

线段树每个节点 $[l,r]$ 维护 $(u,v)$，表示：

{ 包含所有 { **点权在** $[l,r]$ 区间内的点 } 的最短链 } 的两端节点编号。

（拗口，强行断句）

当然若不可能构成链，则 $u=v=-1$（标记为无解）。

由于我们可以 $O(1)$ **“合并”两条链**（见后文解释），所以建树 $O(n)$，单次修改的复杂度为 $O(\log n)$。

查询时在线段树上二分，复杂度也是 $O(\log n)$。

总时间 $O(n+q\log n)$。

* * *

**如何 $O(1)$ 查询两点的 LCA：**

求出树的欧拉序，建 ST 表，两点的 LCA 为两点在欧拉序中之间深度最小的点。

成功转化成 RMQ 问题，$O(1)$。

**如何 $O(1)$ 判断一点 $z$ 是否在 $(x,y)$ 路径上：**

$\text{lca}(z,\text{lca}(x,y))=\text{lca}(x,y)\ \land\ (\text{lca}(z,x)=z\ \lor\ \text{lca}(z,y)=z)$

（可能不需要解释）

**“合并”两条链如何做到 $O(1)$：**

拿来两条链的 $4$ 个端点，$\binom{4}{2}$ 枚举哪两个为合并后的端点，判断剩余两个点是否在两端点间的路径上即可。

* * *

由于合并链的常数较大，请注意常数优化。

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
#define N 200010
#define C 18
#define pi pair<int,int>
char buf[1<<21],*p1,*p2;
#define gc() (p1==p2&&(p2=(p1=buf)+fread(buf,1,1<<21,stdin),p1==p2)?EOF:*p1++)
inline int read() {
	int x=0,f=1;
	char c=gc();
	while(c<'0'||c>'9'){if(c=='-')f=-1;c=gc();}
	while(c>='0'&&c<='9'){x=(x<<3)+(x<<1)+(c^48);c=gc();}
	return x*f;
}
int n,q,a[N],b[N],dep[N],L[N],R[N],tim=0,f[C+1][2*N],w[4],lg[2*N];
vector<int> e[N];
pi t[N<<2],v;
void dfs(int rt,int fa){
	dep[rt]=dep[fa]+1;
	L[rt]=++tim;
	f[0][tim]=rt;
	for(int i:e[rt]) dfs(i,rt),f[0][++tim]=rt;
	R[rt]=tim;
}
inline int cmp(int x,int y){ return dep[x]<dep[y]?x:y; }
inline int LCA(int x,int y){
	x=L[x],y=L[y];
	if(x>y) swap(x,y);
	int z=lg[y-x];
	return cmp(f[z][x],f[z][y-(1<<z)+1]);
}
inline bool onl(int x,int y,int z){
	int lca=LCA(x,y);
	return LCA(z,lca)==lca && (LCA(z,x)==z || LCA(z,y)==z);
}
inline pi operator+(pi x,pi y){
	if(x.fir==-1 || y.fir==-1) return mkp(-1,-1);
	w[0]=x.fir,w[1]=x.sec,w[2]=y.fir,w[3]=y.sec;
	For(i,0,3)
		if(onl(w[i],w[(i+1)%4],w[(i+2)%4]) && onl(w[i],w[(i+1)%4],w[(i+3)%4]))
			return mkp(w[i],w[(i+1)%4]);
	For(i,0,1)
		if(onl(w[i],w[(i+2)%4],w[(i+1)%4]) && onl(w[i],w[(i+2)%4],w[(i+3)%4]))
			return mkp(w[i],w[(i+2)%4]);
	return mkp(-1,-1);
}
#define ls (rt<<1)
#define rs (rt<<1|1)
#define mid ((l+r)>>1)
void build(int rt,int l,int r){
	if(l==r){
		t[rt]=mkp(b[l],b[l]);
		return ;
	}
	build(ls,l,mid);
	build(rs,mid+1,r);
	t[rt]=t[ls]+t[rs];
}
void modify(int rt,int l,int r,int id){
	if(l==r){
		t[rt]=mkp(b[l],b[l]);
		return ;
	}
	if(id<=mid) modify(ls,l,mid,id);
	else modify(rs,mid+1,r,id);
	t[rt]=t[ls]+t[rs];
}
int que(int rt,int l,int r){
	pi vv=v+t[rt];
	if(vv.fir!=-1){
		v=vv;
		return r+1;
	}
	if(l==r) return l;
	int res=que(ls,l,mid);
	if(res<=mid) return res;
	else return que(rs,mid+1,r);
}
signed main(){
	n=read();
	For(i,1,n) b[a[i]=read()]=i;
	For(i,2,n) e[read()].pb(i);
	dfs(1,0);
	lg[1]=0; For(i,2,tim) lg[i]=lg[i>>1]+1;
	For(j,0,C-1) For(i,1,tim-(1<<(j+1))+1) f[j+1][i]=cmp(f[j][i],f[j][i+(1<<j)]);
	build(1,0,n-1);
	q=read();
	int x,y;
	while(q--){
		if(read()==1){
			x=read(),y=read();
			swap(a[x],a[y]);
			swap(b[a[x]],b[a[y]]);
			modify(1,0,n-1,a[x]);
			modify(1,0,n-1,a[y]);
		}else{
			v=mkp(b[0],b[0]);
			printf("%d\n",que(1,0,n-1));
		}
	}
return 0;}
```