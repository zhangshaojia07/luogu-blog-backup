# CF1661D Progressions Covering

### Preface

前置知识：差分，树状数组（单点询问，区间加），贪心。

### Analysis

显然的贪心：从右往左满足各个元素。

但是修改是区间加等差序列，比较头疼（当然线段树也是能做的）。

所以我们将原序列进行差分，用树状数组维护这个差分数组，$n\to 1$ 枚举时元素值可以从上一个值加差分得来。

这样，我们的修改变为区间加（当然不同的差分方式可能有单点加），可行。

时间 $O(n\log n)$，常数小。

### Code

[Link](https://codeforces.com/contest/1661/submission/153598871)

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
#define N 300010
#define low (x&(-x))
int n,k,a[N],b[N],ans=0,c[N];
void add(int x,int y){
	while(x<=n+1){
		c[x]+=y;
		x+=low;
	}
}
int que(int x){
	int res=0;
	while(x){
		res+=c[x];
		x-=low;
	}
	return res;
}
signed main(){IOS;
	cin>>n>>k;
	For(i,1,n) cin>>a[i];
	For(i,1,n) a[i]=-a[i];
	reverse(a+1,a+1+n);
	Rof(i,n,1) a[i]-=a[i-1];
	For(i,1,n) add(i,a[i]-a[i-1]);
	int now=0,lim;
	For(i,1,n-k+1){
		now+=que(i);
		if(now>=0) continue;
		lim=(-now+k-1)/k;
//		cerr<<i<<" "<<now<<":"<<lim<<endl;
		now+=lim*k;
		ans+=lim;
		add(i+1,-lim);
		add(min(n,i+k)+1,lim);
	}
	int res=0;
	For(i,n-k+2,n){
		now+=que(i);
//		cerr<<i<<"!"<<now<<endl;
		ckmx(res,((-now+(n-i))/(n-i+1)));
	}
	cout<<ans+res<<endl;
return 0;}
```