# CF811E Vladik and Entertaining Flags

### Analysis

$n\le 10$，懂了，是一维问题（doge）。

接下来的讲解中所有 $n,m$ 交换一下。

区间询问想到线段树，维护啥呢？

首先是左右端点下标，要知道两边「端口」地形。

再是区间内的连通块数量。

然后就是重点：

```
2 2        2 2
2 1 -link- 1 2
2 2        2 2
```

和

```
2 2        2 2
2 1 -link- 1 1
2 2        2 2
```

接口处相同但连通块个数分别减少 $2,3$。

所以对于线段树每个节点，记录左右两端每个元素所在并查集，这样才能正确 merge。

大概长这样↓

```cpp
friend node operator+(node x,node y){
	node res=(node){x.cnt+y.cnt,x.l,y.r};
	For(i,0,m-1) f[x.L[i]]=x.L[i];
	For(i,0,m-1) f[x.R[i]]=x.R[i];
	For(i,0,m-1) f[y.L[i]]=y.L[i];
	For(i,0,m-1) f[y.R[i]]=y.R[i];
	For(i,0,m-1){
		if(a[x.r][i]!=a[y.l][i] || gf(x.R[i])==gf(y.L[i])) continue;
		res.cnt--;
		f[gf(x.R[i])]=gf(y.L[i]);
	}
	For(i,0,m-1) res.L[i]=gf(x.L[i]);
	For(i,0,m-1) res.R[i]=gf(y.R[i]);
	return res;
}
```

注意这里 `f` 数组是公用的，避免 MLE。

时间 $O(nm-qm\log n)$

### Code

[Link](https://codeforces.com/contest/811/submission/153419979)

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
#define N 100010
#define M 10
#define mid ((l+r)>>1)
#define ls (rt<<1)
#define rs (rt<<1|1)
//CF811E
int n,m,q,a[N][M];
int f[N*M];
inline int gf(int x){return x==f[x]?x:f[x]=gf(f[x]);}
inline int num(int x,int y){return (x-1)*m+y+1;}
struct node{
	int cnt,l,r,L[M],R[M];
	void asg(int id){
		l=r=id;
		cnt=1;
		L[0]=R[0]=num(id,0);
		For(i,1,m-1){
			if(a[id][i]==a[id][i-1]) L[i]=R[i]=L[i-1];
			else cnt++,L[i]=R[i]=num(id,i);
		}
	}
	friend node operator+(node x,node y){
		node res=(node){x.cnt+y.cnt,x.l,y.r};
		For(i,0,m-1) f[x.L[i]]=x.L[i];
		For(i,0,m-1) f[x.R[i]]=x.R[i];
		For(i,0,m-1) f[y.L[i]]=y.L[i];
		For(i,0,m-1) f[y.R[i]]=y.R[i];
		For(i,0,m-1){
			if(a[x.r][i]!=a[y.l][i] || gf(x.R[i])==gf(y.L[i])) continue;
			res.cnt--;
			f[gf(x.R[i])]=gf(y.L[i]);
		}
		For(i,0,m-1) res.L[i]=gf(x.L[i]);
		For(i,0,m-1) res.R[i]=gf(y.R[i]);
		return res;
	}
}t[4*N];
void build(int rt,int l,int r){
	if(l==r){
		t[rt].asg(l);
		return ;
	}
	build(ls,l,mid);
	build(rs,mid+1,r);
	t[rt]=t[ls]+t[rs];
}
node que(int rt,int l,int r,int x,int y){
	if(l==x && r==y) return t[rt];
	if(y<=mid) return que(ls,l,mid,x,y);
	if(x>mid) return que(rs,mid+1,r,x,y);
	return que(ls,l,mid,x,mid)+que(rs,mid+1,r,mid+1,y);
}
signed main(){IOS;
	cin>>m>>n>>q;
	For(j,0,m-1)
		For(i,1,n)
			cin>>a[i][j];
	build(1,1,n);
	int l,r;
	while(q--){
		cin>>l>>r;
		cout<<que(1,1,n,l,r).cnt<<endl;
	}
return 0;}
```