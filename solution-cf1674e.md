# CF1674E Breaking the Wall

### Preface

被 Hack 了，祭。

### Analysis

设最终被摧毁的位置为 $l,r(l<r)$，不妨设 $a_l<a_r$。

若 $r-l\ge 3$，则一次攻击不可能同时削减 $l,r$ 的防御，则答案为 $\lceil\frac{a_l}{2}\rceil+\lceil\frac{a_r}{2}\rceil$。

若 $r-l=2$，则两者都攻击的方案只有打中间，然后双方各 $-1$。此类情况答案为 $a_l+\lceil\frac{a_r-a_l}{2}\rceil$，即先同时 $-1$，再将未清零的消掉。

若 $r-l=1$，则攻击方案有两种：

1. $a_l$ 减 $2$，$a_r$ 减 $1$。

2. $a_l$ 减 $1$，$a_r$ 减 $2$。

所以，若 $2a_l\le a_r$，全部用 (1.) 攻击，答案为 $\lceil\frac{a_r}{2}\rceil$。

否则，我们要先用「一次 (1.) 攻击 一次 (2.) 攻击」的形式使得 $a_r$ 接近 $a_l$ 的两倍，再用 (1.) 攻击。

分类讨论，设 $A=2a_l-a_r$，得到此部分的答案为 $a_r-a_l+\lfloor
\frac{2}{3}A\rfloor+(A\bmod3)$。

然后在每种答案中取最小值即可。

### Code

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
#define N 200010
int n,ans,a[N],b[N];
int calc(int x,int y){
	if(x>y) swap(x,y);
	if(2*x<=y) return (y+1)/2;
	int res=y-x;
	x-=res;
	return res+x/3*2+x%3;	
}
int work(int x,int y){
	if(x>y) swap(x,y);
	return (y-x+1)/2+x; 
}
signed main(){IOS;
	cin>>n;
	For(i,1,n) cin>>a[i];
	For(i,1,n) b[i]=(a[i]+1)>>1;
	sort(b+1,b+1+n);
	ans=b[1]+b[2];
	For(i,1,n-1) ckmn(ans,calc(a[i],a[i+1]));
	For(i,1,n-2) ckmn(ans,work(a[i],a[i+2]));
	cout<<ans<<endl;
return 0;}
```