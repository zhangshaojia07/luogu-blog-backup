# P5998 [PA2014]Plemiona（Hard 线段树+栈）

很妙的题啊！

将每个矩形的 $x_1,y_1$ 分别 $+1$，这样两个矩形可合并等价于两维的区间 $[x_1,x_2],[y_1,y_2]$ 分别有交（线段交的长度 $>0$）。

我们在 $O(\log n)$ 的时间内**找到**两个可合并的矩形再**合并**，由于最多合并 $n-1$ 次，这样复杂度是 $O(n\log n)$ 的。

我们按 $x$ 轴建线段树，依次 $^{*}$ 插入矩形，线段树每个节点 $[l,r]$ 存两个矩形编号**栈** $A,B$，分别表示 $[l',r']$ 与 $[l,r]$ 有交但不包含 $[l,r]$ 的矩形（我们用 $[l',r']$ 表示矩形的 $x$ 轴），$[l,r]$ 被 $[l',r']$ 包含，但是 $[l_0,r_0]$ 不被 $[l',r']$ 包含的矩形（$[l_0,r_0]$ 表示 $[l,r]$ 线段树节点的父节点的范围）。

但是我们忽略了 $y$ 轴的情况，发现我们上述的 $*$ “依次”顺序改为按 $y_1$ 非递减插入即可。

如果一个矩形被合并到了别的矩形内了我们就打一个死亡标记，下次在任何栈里发现他都直接扔掉即可。

这里栈用 vector 实现~~懒得写链表~~，跑得很慢。

其实这个 $O(n\log n)$ 不太好证，是一大堆均摊。

```cpp
/*
* Author: ShaoJia
* Last Modified time: 2022-10-14 15:19:26
* Motto: We'll be counting stars.
*/
// #pragma GCC optimize("Ofast")
#include<bits/stdc++.h>
using namespace std;
#define debug(...) cerr<<"#"<<__LINE__<<": "<<__VA_ARGS__<<endl
#define For(i,j,k) for(int i=(j),i##_=(k);i<=i##_;i++)
#define Rof(i,j,k) for(int i=(j),i##_=(k);i>=i##_;i--)
#define lob lower_bound
#define upb upper_bound
#define fir first
#define sec second
#define mkp make_pair
#define mkt make_tuple
#define siz(x) ((int)(x).size())
#define pb emplace_back
#define ckmx(a,b) a=max(a,b)
#define ckmn(a,b) a=min(a,b)
#define ll long long
#define pi pair<int,int>
#define N 100005
struct node{
	vector<int> bc,in;
}t[N<<3];//(2*N)*4
struct rec{
	int x,xx,y,yy;
	rec(){}
	rec(int k1,int k2,int k3,int k4){ x=k1,xx=k2,y=k3,yy=k4; }
	friend rec operator+(const rec& x,const rec& y){
		return rec(min(x.x,y.x),max(x.xx,y.xx),min(x.y,y.y),max(x.yy,y.yy));
	}
	friend void operator+=(rec& x,const rec& y){
		x=x+y;
	}
	friend bool operator<(const rec& x,const rec& y){
		if(x.x!=y.x) return x.x<y.x;
		if(x.xx!=y.xx) return x.xx<y.xx;
		if(x.y!=y.y) return x.y<y.y;
		return x.yy<y.yy;
	}
}a[N];
vector<rec> out;
int b[N],n,so[N<<1],lim=0;
bool kil[N];
bool cmp(int x,int y){ return a[x].yy<a[y].yy; }
void flush(vector<int>& v){ if(!v.empty() && kil[v.back()]) v.pop_back(); }
int top(vector<int>& v){ flush(v); return v.back(); }
bool empty(vector<int>& v){ flush(v); return v.empty(); }
#define mid ((l+r)>>1)
#define ls (rt<<1)
#define rs (rt<<1|1)
void add(int rt,int l,int r,int x,int y,int id){
	if(x<=l && r<=y){
		// cout<<l<<"~"<<r<<" bc "<<id<<endl;
		return t[rt].bc.pb(id),void();
	}
	if(x<=mid) add(ls,l,mid,x,y,id);
	if(y>mid) add(rs,mid+1,r,x,y,id);
}
void Add(int rt,int l,int r,int x,int id){
	t[rt].in.pb(id);
	// cout<<l<<"~"<<r<<" in "<<id<<endl;
	if(l==r) return ;
	if(x<=mid) Add(ls,l,mid,x,id);
	else Add(rs,mid+1,r,x,id);
}
bool calc(int rt,int l,int r,int x,int id){
	if(!empty(t[rt].bc)){
		int s=top(t[rt].bc);
		if(a[s].yy>=a[id].y){
			kil[s]=true;
			// cout<<s<<"->"<<id<<endl;
			a[id]+=a[s];
			return true;
		}
	}
	if(l==r) return false;
	if(x<=mid) return calc(ls,l,mid,x,id);
	else return calc(rs,mid+1,r,x,id);
}
bool que(int rt,int l,int r,int x,int y,int id){
	if(x<=l && r<=y){
		if(!empty(t[rt].in)){
			int s=top(t[rt].in);
			if(a[s].yy>=a[id].y){
				kil[s]=true;
				// cout<<s<<"->"<<id<<endl;
				a[id]+=a[s];
				return true;
			}
		}
		return false;
	}
	if(x<=mid){
		if(que(ls,l,mid,x,y,id)) return true;
	}
	if(y>mid){
		if(que(rs,mid+1,r,x,y,id)) return true;
	}
	return false;
}
bool work(int id){
	if(calc(1,1,lim,a[id].x,id)) return true;
	if(calc(1,1,lim,a[id].xx,id)) return true;
	if(a[id].x<a[id].xx-1 && que(1,1,lim,a[id].x+1,a[id].xx-1,id)) return true;
	return false;
}
signed main(){ios::sync_with_stdio(false),cin.tie(nullptr);
	cin>>n;
	For(i,1,n) cin>>a[i].x>>a[i].xx>>a[i].y>>a[i].yy,a[i].x++,a[i].y++;
	For(i,1,n) so[++lim]=a[i].x,so[++lim]=a[i].xx;
	sort(so+1,so+1+lim);
	lim=unique(so+1,so+1+lim)-so-1;
	For(i,1,n)
		a[i].x=lob(so+1,so+1+lim,a[i].x)-so,
		a[i].xx=lob(so+1,so+1+lim,a[i].xx)-so;
	// For(i,1,n) cout<<a[i].x<<"~~~"<<a[i].xx<<endl;
	// For(i,1,lim) cout<<so[i]<<"*"; cout<<endl;
	iota(b+1,b+1+n,1);
	sort(b+1,b+1+n,cmp);
	int x;
	For(i,1,n){
		x=b[i];
		// debug(x<<">>>");
		while(work(x));
		add(1,1,lim,a[x].x,a[x].xx,x);
		Add(1,1,lim,a[x].x,x);
		Add(1,1,lim,a[x].xx,x);
	}
	For(i,1,n) if(!kil[i]) out.pb(a[i]);
	sort(out.begin(),out.end());
	cout<<siz(out)<<"\n";
	for(auto i:out) cout<<so[i.x]-1<<" "<<so[i.xx]<<" "<<i.y-1<<" "<<i.yy<<"\n";
return 0;}
```