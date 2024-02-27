# CF1680D Dog Walking（极差最大）

### Analysis

先分别将不确定的位移设为 $-k$ 和 $+k$，计算狗最终的坐标，判断 $0$ 坐标是否在区间内——若不在则无解。

显然的是，最终狗走到的地方是一段包含坐标 $0$ 的区间，我们只要最大化极差即可。

设不确定的位移数量为 $cnt$，先将所有不确定的位移固定为 $-k$，预处理每个时间狗的坐标。

设这样狗最终的坐标为 $-d$ ——即我们还要在不确定的位移中总共向正方向增加 $d$ 格。

相当于在 $cnt$ 容量为 $2k$ 的盒子里放恰好 $d$ 个球。

我们 $O(n^2)$ 枚举两个时间——钦定的最大和最小坐标取到的时间，然后每一对 $O(1)$ 算答案，最后取最大值即可。

设枚举到 $x$ 时间坐标最小，$y$ 时间坐标最大。不失一般性，设 $x<y$。

我们的目标是让 $x,y$ 时间坐标的差值最大，即在 $[x,y]$ 时间段内的不确定的位移因尽可能大（接近 $+k$）。

也就是说，让这段区间内的盒子尽可能多放球，设这段共 $z$ 个盒子，则最多放 $\min\{2kz,d\}$ 个球，这样极差就能快速求出。

值得一提，$x>y$ 的情况希望 $[y,x]$ 间盒子少放球，可知最少放 $\max\{0,d-2k(cnt-z)\}$。

总时间复杂度 $O(n^2)$。

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
#define N 3010

int n,k,a[N],b[N],c[N],cnt=0,ned,L=0,R=0;
signed main(){IOS;
	cin>>n>>k;
	For(i,1,n) cin>>a[i];
	For(i,1,n) if(a[i]) L+=a[i]; else L-=k;
	For(i,1,n) if(a[i]) R+=a[i]; else R+=k;
	if(!(L<=0 && R>=0)){
		cout<<-1<<endl;
		return 0;
	}
	For(i,1,n) if(!a[i]) cnt++;
	For(i,1,n) if(a[i]) b[i]=b[i-1]+a[i]; else b[i]=b[i-1]-k;
	For(i,1,n) if(a[i]) c[i]=c[i-1]; else c[i]=c[i-1]+1;
	k<<=1;
	ned=-b[n];
	int ans=0,tmp;
	For(i,0,n){//min
		For(j,0,n){//max
			if(i<j){
				tmp=b[j]-b[i]+min((c[j]-c[i])*k,ned);
			}else if(i>j){
				tmp=b[j]-b[i]-max(ned-(cnt-(c[i]-c[j]))*k,0ll);
			}else continue;
			ckmx(ans,tmp);
		}
	}
	cout<<ans+1<<endl;
return 0;}
```

