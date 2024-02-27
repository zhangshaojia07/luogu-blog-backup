# CF1709F Multiset of Strings（NTT）

## 奇妙の翻译

一棵高度为 $n$ 的**满二叉树**（共 $2^{n+1}-1$ 个点，$2^{n+1}-2$ 条边），边从父亲指向儿子。

每条边的最大流量为 $[0,k]$ 内的整数，求有几种赋最大流量方案，使得以根为源点，所有叶子为汇点，最大流恰好为 $f$。

## 做法

设 $k$ 固定时答案为 $a(n,f)$，我们在这棵树的根之上加一条“叶柄”（边权也为 $[0,k]$ 之间的整数）后答案设为 $b(n,f)$（在 $f>k$ 时定义为 $0$），则
$$
b(n,f)=(k-f+1)\, a(n,f)+\sum_{i=f+1}^{2k}a(n,i)
$$
↑ 前提是 $f\le k$。
$$
a(n,f)=\sum_{i=0}^f b(n-1,i)\cdot b(n-1,f-i)\quad(*)
$$
$$
Ans=[f\le 2k]a(n,f)
$$
由于 $n$ 很小，所以我们的瓶颈就在上面 $(*)$ 柿子。

设多项式
$$
A(x)=\sum_{i=0}^{2k}a(n,i)x^i 
\quad
B(x)=\sum_{i=0}^kb(n-1,i)x^i 
$$
由 $(*)$ 柿子得到
$$
A=B^2
$$
发现就是一个 simple 的多项式平方，直接 NTT 解决。

时间复杂度 $O(nk\log k)$，较为卡常。（我 TLE 了三发 qwq 罚时吃饱）

## Code

```cpp
//We'll be counting stars.
#pragma GCC optimize("Ofast")
#include<bits/stdc++.h>
using namespace std;
#define fir first
#define sec second
#define mkp make_pair
#define pb emplace_back
#define For(i,j,k) for(int i=(j);i<=(k);i++)
#define Rof(i,j,k) for(int i=(j);i>=(k);i--)
#define ckmx(a,b) a=max(a,b)
#define ckmn(a,b) a=min(a,b)
#define Fi(s) freopen(s,"r",stdin)
#define Fo(s) freopen(s,"w",stdout)
#define Fre(s) Fi(s".in"),Fo(s".out")
#define debug(...) cerr<<"#"<<__LINE__<<": "<<__VA_ARGS__<<endl
#define ll long long
const ll mod=998244353;
inline ll pw(ll x,ll y){ll r=1;while(y){if(y&1)r=r*x%mod;x=x*x%mod;y>>=1;}return r;}
const int V=1<<19;
int tax[V];
ll w[V+1];
inline void mktax(int L){For(i,0,(1<<L)-1) tax[i]=(tax[i>>1]>>1)|((i&1)<<(L-1));}
inline void DFT(ll *a,int L){
	int n=1<<L,len;
	For(i,0,n-1) if(i<tax[i]) swap(a[i],a[tax[i]]);
	ll x,y;
	For(i,1,L){
		len=(1<<i);
		for(int j=0;j<n;j+=len) For(k,0,(len>>1)-1){
			x=a[j+k];
			y=a[j+k+(len>>1)]*w[k*(n/len)]%mod;
			a[j+k]=(x+y)%mod;
			a[j+k+(len>>1)]=(x-y+mod)%mod;
		}
	}
}
inline void IDFT(ll *a,int L){
	int n=1<<L,d=pw(n,mod-2);
	reverse(a+1,a+n);
	DFT(a,L);
	For(i,0,n-1) (a[i]*=d)%=mod;
}
ll a[V],b[V];
int n,k,m,L;
signed main(){
	ios::sync_with_stdio(false),cin.tie(nullptr);
	cin>>n>>k>>m;
	if(m>2*k){
		cout<<0<<endl;
		return 0;
	}
	L=0;
	while((1<<L)<=2*k+1) L++;
	mktax(L);
	ll wn;
	w[0]=1; 
	wn=pw(3,(mod-1)/(1<<L));
	For(j,1,(1<<L)) w[j]=w[j-1]*wn%mod;
	For(i,0,k) a[i]=i+1;
	For(i,k+1,2*k) a[i]=2*k-i+1;
	For(i,1,n-1){
		copy(a,a+2*k+1,b);
		a[2*k]=0;
		Rof(j,2*k-1,0) a[j]=(a[j+1]+b[j+1])%mod;
		For(j,0,k) (a[j]+=b[j]*(k+1-j)%mod)%=mod;
		fill(a+k+1,a+(1<<L),0);
		DFT(a,L);
		For(j,0,(1<<L)-1) a[j]*=a[j]%mod;
		IDFT(a,L);
	}
	cout<<a[m]<<endl;
return 0;}
```