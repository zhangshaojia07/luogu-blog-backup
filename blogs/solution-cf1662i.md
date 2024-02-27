# CF1662I Ice Cream Shop（转一维区间问题）

### Analysis

考虑当我们店开在哪一段区间里时，某 Hut 里的人会去买我们的冰淇凌。

我们可以 simple 地线性求出离每个 Hut 最近的竞争商贩有多近。设当前考虑的 Hut 的位置为 $x$，最近的竞争商贩距离它 $d$ 距离，那我们开在 $(x-d,x+d)$（左右端均开区间）区间内这 Hut 里的人会买我们的冰淇凌。

对每一个 Hut 求出上述的区间，问题就转化为**数轴上有 $n$ 段区间，求被覆盖次数最多的点**。

这样就简单了：每个区间改成左加右减的形式，排序，扫描即可。

时间 $O(n\log n+m)$

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
const int inf=1e17;
const double eps=0.1;
int n,m,p[N],b[N],L[N],R[N];
pair<double,int> q[2*N]; 
signed main(){IOS;
	cin>>n>>m;
	For(i,1,n) cin>>p[i];
	For(i,1,m) cin>>b[i];
	sort(b+1,b+1+m);
	int tmp=0,x;
	For(i,1,n){
		x=(i-1)*100;
		while(tmp<m && b[tmp+1]<=x) tmp++;
		if(tmp==0) L[i]=inf;
		else L[i]=x-b[tmp];
	}
	tmp=m+1;
	Rof(i,n,1){
		x=(i-1)*100;
		while(tmp>1 && b[tmp-1]>x) tmp--;
		if(tmp==m+1) R[i]=inf;
		else R[i]=b[tmp]-x;
	}
	double y;
	For(i,1,n){
		x=(i-1)*100;
		y=(double)min(L[i],R[i])-eps;
		q[2*i-1]=mkp(x-y,p[i]);
		q[2*i]=mkp(x+y,-p[i]);
	}
	sort(q+1,q+1+2*n);
	int ans=0;
	x=0;
	For(i,1,2*n){
		x+=q[i].sec;
		ckmx(ans,x);
	}
	cout<<ans<<endl;
return 0;}
```

