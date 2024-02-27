# CF1667C Half Queen Cover

### Preface

这种题对于我来说真的难，猜结论 + 证明 + 构造。

### Analysis

以下 H 表示半皇后的行攻击，L 表示半皇后的列攻击。

设我们在 $n\times n$ 的棋盘上放置了 $k$ 个半皇后，则若只考虑半皇后的 H/L，最多 $k$ 行、$k$ 列被攻击。

找到 从上到下第一个没有被 H 的行 和 从左往右第一个没有被 L 的列。

这一行、一列上总共至少有 $2n-2k-1$ 个没有被 h 且没有被 L 的点，而且这些点所在的对角线各不相同。

所以，我们得到 $2n-2k-1\le k$，即 $k\ge \frac{2n-1}{3}$，我们证明 $k=\lceil\frac{2n-1}{3}\rceil$ 可以取到。

对于 $n \bmod 3=2$，设 $t=\frac{n-2}{3}$，我们在左上角 $(t+1)\times (t+1)$ 的子矩形的反对角线上放半皇后，同理在右下角 $t\times t$ 的子矩形的反对角线上放半皇后。

![](https://cdn.luogu.com.cn/upload/image_hosting/1wjp4a7p.png)

大概就长这样↑，红色为半皇后，绿色为攻击范围。

对于 $n \bmod 3\ne 2$，我们在 $(n,n)$ 放一个半皇后，这样问题变成了 $(n-1)\times (n-1)$ 的棋盘问题了。

### Code

[Link](https://codeforces.com/contest/1667/submission/154284785)

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
#define N -1

int n;
vector<pair<int,int> > ans;
signed main(){IOS;
	cin>>n;
	if(n==1){
		cout<<"1\n1 1"<<endl;
		return 0;
	} 
	while(n%3!=2){
		ans.pb(mkp(n,n));
		n--;
	}
	int tar=(n+1)/3;
	For(i,1,tar) ans.pb(mkp(i,tar+1-i));
	For(i,1,tar-1) ans.pb(mkp(n+1-i,n+1-(tar-i)));
	cout<<ans.size()<<endl;
	for(auto i:ans) cout<<i.fir<<" "<<i.sec<<endl;
return 0;}
```