# CF1665E MinimizOR

### Preface

考场口胡可持久化 01Trie。考完发现这神奇线段树做法。

### Analysis

先来个结论。

------------

**结论：** 若区间中每个数均 $<2^k$，则每次询问只取区间前 $k+1$ 小值（若有多个数值相同，重复计入），两两作 $\text{OR}$ 后一定是正确答案。

**证明：** 归纳法。

基础步骤：若序列中所有数 $<2^1=2$，则取前 $2$ 小的数两两作即为答案。

归纳步骤：若序列中所有数 $<2^{k-1}$ 时成立。我们证明序列中所有数 $<2^k$ 时也成立。

1. 若序列中所有数 $\ge 2^{k-1}$，则全部将最高位 $2^{k-1}$ 减掉，这样就是所有数 $<2^{k-1}$ 的问题了。

2. 若序列中有两个及以上的数 $<2^{k-1}$，则答案必然在这些数之间，转化为所有数 $<2^{k-1}$ 的问题。

3. 若序列中只有一个数 $<2^{k-1}$，设为 $x$。则答案必然包含 $2^{k-1}$ 这一二进制位，所以将 $x$ 加上 $2^{k-1}$ 对答案无影响。但是这时最小的 $k-1$ 个值会变，我们把 $x$ 加进去（变为前 $k$ 小）则必然包含答案。转化到情况 $(1.)$。

至此，归纳成立，原命题得证。

------------

所以我们维护区间前 $31$ 小值即可。

注意 ST 表和 simple 的线段树会炸空间，我们维护区间最小值，然后每次改最小值为 $\inf$，这样 $31$ 次即可，最后记得改回来。

时间 $O(n+31q(31+\log n))$

### Code

[Link](https://codeforces.com/contest/1665/submission/153105645)

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
#define mem(x,y) memset(x,y,sizeof(x))
#define For(i,j,k) for(int i=j;i<=k;i++)
#define Rof(i,j,k) for(int i=j;i>=k;i--)
#define Fe(x,y) for(int x=head[y];x;x=e[x].nxt)
#define ckmx(a,b) a=max(a,b)
#define ckmn(a,b) a=min(a,b)
#define N 100010
#define mid ((l+r)>>1)
#define ls (rt<<1)
#define rs (rt<<1|1)
#define endl '\n'
#define pi pair<int,int>
const int inf=2e9;
int n,a[N],q,s[32];
pi t[4*N];
void build(int rt,int l,int r){
	if(l==r){
		t[rt]=mkp(a[l],l);
		return ;
	}
	build(ls,l,mid);
	build(rs,mid+1,r);
	t[rt]=min(t[ls],t[rs]);
}
pi que(int rt,int l,int r,int x,int y){
	if(l==x && r==y) return t[rt];
	if(y<=mid) return que(ls,l,mid,x,y);
	if(x>mid) return que(rs,mid+1,r,x,y);
	return min(que(ls,l,mid,x,mid),que(rs,mid+1,r,mid+1,y));
}
void add(int rt,int l,int r,int x,int val){
	if(l==r){
		t[rt]=mkp(val,l);
		return ;
	}
	if(x<=mid) add(ls,l,mid,x,val);
	else add(rs,mid+1,r,x,val);
	t[rt]=min(t[ls],t[rs]);
} 
void work(){
	cin>>n;
	For(i,1,n) cin>>a[i];
	build(1,1,n);
	cin>>q;
	int l,r,cnt,ans;
	while(q--){
		cin>>l>>r;
		cnt=min(r-l+1,31);
		For(i,1,cnt){
			s[i]=que(1,1,n,l,r).sec;
			add(1,1,n,s[i],inf);
		}
		For(i,1,cnt) add(1,1,n,s[i],a[s[i]]);
		ans=inf;
		For(i,1,cnt-1) For(j,i+1,cnt) ckmn(ans,a[s[i]]|a[s[j]]);
		cout<<ans<<endl;
	}
}
signed main(){IOS;
	int T;cin>>T;
	while(T--)work();
return 0;}
```