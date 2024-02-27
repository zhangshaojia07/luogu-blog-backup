# P4428 [BJOI2018]二进制 题解

本题解有自信说此做法空间常数非常小。

### Analysis

我们先研究一个二进制数是否能重新排列后是个 $3$ 的倍数。

通过简单的数位拆分，得到该二进制数的条件，但是非常麻烦（便不展示），所以就有了**正难则反**的思想。

考虑哪些二进制数不能满足上述条件，发现满足以下三种之一即可（$cnt_{0/1}$ 表示二进制中 $0/1$ 的个数）：

1. $cnt_0\geqslant 2$ 且 $cnt_1=1$

2. $cnt_0=0$ 且 $cnt_1\equiv 1\pmod{2}$

3. $cnt_0=1$ 且 $cnt_1\equiv 1\pmod{2}$

发现这是一个**线段树**（单点修改，区间询问）题！

由于不满足条件的二进制数 $\min(cnt_0,cnt_1)\leqslant 1$，我们就可以轻松地线段树节点合并了。

每一个节点存：

* 区间 最/次 左/右 的 $0/1$ 的位置。

* 区间内的答案（不满足条件的二进制数）。

其实本题最核心的部分在于 `merge`，详情请见代码，有详细注释。

### Detail

最后用全集减去不满足的即可。

### Code

时间复杂度为 $O(km\log n)\quad k\approx 40$。

空间复杂度为 $O(kn\log n)\quad k\approx 4\times 10$(以 `int` 为单位，常数 $4$ 为线段树必须)。

```cpp
#include<bits/stdc++.h>
using namespace std;
#define ll long long
#define IOS ios::sync_with_stdio(0);cin.tie(0);cout.tie(0)
#define For(i,j,k) for(register int i=j;i<=k;i++)
#define Rof(i,j,k) for(register int i=j;i>=k;i--)
	#define fir first
	#define sec second
	#define mkp make_pair
	#define pii pair<int,int>
#define N 100100
	#define mid ((l+r)>>1)
	#define ls (rt<<1)
	#define rs (rt<<1|1)
struct node{
	pii l[2];
	pii r[2];
	ll ans;
}t[4*N];
int n,q;
node upd(node x,node y,int l,int m,int r){//merge two nodes x:[l,m] and y:[m+1,r] 
	node res;
	res.ans=x.ans+y.ans;
	res.l[0]=x.l[0],res.l[1]=x.l[1];
	res.r[0]=y.r[0],res.r[1]=y.r[1];
	For(i,0,1){
		if(res.l[i].sec==m+1){
			if(res.l[i].fir==m+1) res.l[i]=y.l[i];
			else res.l[i].sec=y.l[i].fir;
		}
		if(res.r[i].sec==m){
			if(res.r[i].fir==m) res.r[i]=x.r[i];
			else res.r[i].sec=x.r[i].fir;
		}
	}
	ll tmp;//增加值 
	ll L=m-x.r[0].fir;
	ll R=y.l[0].fir-m-1;
	ll LL=max(0,x.r[0].fir-x.r[0].sec-1);
	ll RR=max(0,y.l[0].sec-y.l[0].fir-1);
	tmp=(L>>1)*((R+1)>>1)+((L+1)>>1)*(R>>1);//第 2 类，分 奇|偶 和 偶|奇 讨论 
	res.ans+=tmp;
	For(i,0,1){//第 3 类，分 奇|偶0偶 和  偶|偶0奇 和 偶|奇0偶 和  奇|奇0奇 讨论 
		if((i==0 && y.l[0].fir!=r+1) || (i==1 && x.r[0].fir!=l-1)){
			if(R&1) tmp=(L>>1)*((RR+2)>>1)+((L+1)>>1)*((RR+1)>>1);
			else	tmp=((L+1)>>1)*((RR+2)>>1)+(L>>1)*((RR+1)>>1);
		}else tmp=0;
		res.ans+=tmp;
		swap(L,R);//左右 swap，各计算一次 
		swap(LL,RR);
	}
	L=m-x.r[1].fir;
	R=y.l[1].fir-m-1;
	LL=max(0,x.r[1].fir-x.r[1].sec-1);
	RR=max(0,y.l[1].sec-y.l[1].fir-1);
	For(i,0,1){//第 1 类
		if((i==0 && x.r[1].fir!=l-1) || (i==1 && y.l[1].fir!=r+1)){
			tmp=(LL+1)*R;
			if(L==0 && R>0) tmp--;//排除与第 3 类重叠的情况 
		}else tmp=0;
		res.ans+=tmp;
		swap(L,R);//左右 swap，各计算一次 
		swap(LL,RR);
	}
	return res;
} 
void build(int rt,int l,int r){
	if(l==r){
		int x;
		cin>>x;//read and assign
		t[rt]={{mkp(l+1,l+1),mkp(l+1,l+1)},{mkp(l-1,l-1),mkp(l-1,l-1)},x};
		if(x==0)	t[rt].l[0].fir=t[rt].r[0].fir=l;
		else		t[rt].l[1].fir=t[rt].r[1].fir=l;
		return ;
	}
	build(ls,l,mid);
	build(rs,mid+1,r);
	t[rt]=upd(t[ls],t[rs],l,mid,r);
}
void rev(int rt,int l,int r,int id){//reverse a leaf
	if(l==r){
		t[rt].ans^=1;
		swap(t[rt].l[0],t[rt].l[1]);
		swap(t[rt].r[0],t[rt].r[1]);
		return ;
	}
	if(id<=mid) rev(ls,l,mid,id);
	else rev(rs,mid+1,r,id);
	t[rt]=upd(t[ls],t[rs],l,mid,r);
}
node que(int rt,int l,int r,int x,int y){//query
	if(l==x && r==y)
		return t[rt];
	if(y<=mid)
		return que(ls,l,mid,x,y);
	else if(x>mid)
		return que(rs,mid+1,r,x,y);
	else
		return upd(que(ls,l,mid,x,mid),que(rs,mid+1,r,mid+1,y),x,mid,y);
}
ll calc(int l,int r){//all 
	return (ll)(r-l+1)*(r-l+2)/2;
}
signed main(){
	IOS;
	cin>>n;
	build(1,1,n);
	cin>>q;
	int opt,x,y;
	while(q--){
		cin>>opt;
		if(opt==1){
			cin>>x;
			rev(1,1,n,x);
		}else{
			cin>>x>>y;
			cout<<calc(x,y)-que(1,1,n,x,y).ans<<endl;
		}
	}
    return 0;
}
```