# P5300 [GXOI/GZOI2019]与或和

### Analysis

首先二进制每一位分开考虑贡献，若以 $\mathtt{AND}$ 值之和为例，则问题转化成：

**给定一个 $n\times n$ 的 $01$ 矩阵，求全 $1$ 子矩阵的个数。**

这个问题可以线性，用单调栈解决。

即转化成过于简单的直方图求子矩阵个数问题。

这里再说明一下具体过程。

枚举该子矩阵的下边高度，在这条上记录向上连续的 $1$ 的个数。然后从左往右维护单调栈即可。再具体可看 Code 中 `work()` 的部分。

总体时间 $O(31n^2)$

### Code

注意代码中求的是全 $0$ 子矩阵。

```cpp
//Said no more counting dollars. We'll be counting stars.
//#pragma GCC optimize("Ofast")
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
#define N 1024

int n,b[N][N],a1=0,a2=0,h[N][N];
bool a[N][N];
int st[N],tot;
int work(){//all 0 count
	For(i,1,n) h[1][i]=a[1][i]^1;
	For(i,2,n){
		For(j,1,n){
			h[i][j]=(a[i][j]?0:h[i-1][j]+1);
		}
	}
	int res=0,now;
	For(i,1,n){
		tot=now=0;
		For(j,1,n){
			while(tot && h[i][st[tot]]>=h[i][j]){
				mad(now,-h[i][st[tot]]*(st[tot]-st[tot-1]));
				tot--;
			}
			mad(now,h[i][j]*(j-st[tot]));
			st[++tot]=j;
			mad(res,now);
		}
	}
	return res;
}
signed main(){IOS;
	cin>>n;
	int all=pw(n*(n+1)/2%mod,2);
	For(i,1,n)For(j,1,n)cin>>b[i][j];
	For(i,0,30){
		For(j,1,n)For(k,1,n)a[j][k]=((b[j][k]>>i)&1)^1;
		mad(a1,pw(2,i)*work());
		For(j,1,n)For(k,1,n)a[j][k]=(b[j][k]>>i)&1;
		mad(a2,pw(2,i)*(all-work()));
	}
	cout<<a1<<" "<<a2<<endl;
return 0;}
```