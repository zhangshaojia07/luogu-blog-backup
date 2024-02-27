# CF1667B Optimal Partition

### Analysis

发现很难有贪心策略（至少我没想到），所以就暴力 DP。

$f_i$ 表示 $a[1,i]$ 划分后的最大价值和，$s_i=\sum_{j=1}^ia_j$。

$$p(x)=\begin{cases}
1&x>0\\
0&x=0\\
-1&x<0
\end{cases}$$

$$\begin{cases}
f_0=0
\\
f_i=\max\limits_{j=0}^{i-1}(i-j)\cdot p(s_i-s_j)+f_j
\end{cases}$$

这样是 $O(n^2)$ 的，优化时发现 $p$ 阻碍了时间的优化。

将 $p$ 分类讨论

$$\begin{aligned}
f_i&=\max_{j=0}^{i-1}\begin{cases}
(i-j)+f_j&s_i>s_j
\\
f_j&s_i=s_j
\\
(j-i)+f_j&s_i<s_j
\end{cases}
\\&=\max_{j=0}^{i-1}\begin{cases}
i+(f_j-j)&s_i>s_j
\\
f_j&s_i=s_j
\\
-i+(f_j+j)&s_i<s_j
\end{cases}
\end{aligned}$$

然后就可以用 **两个 BIT（树状数组）** 维护 $s_i\ne s_j$ 部分 和 **一个离散化 $+$ 桶** 维护 $s_i=s_j$ 部分。

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
#define N 500010
#define low (x&(-x))
const int inf=1e16;
int n,a[N],b[N],lim,c[2][N],qwq[N];
void add(bool T,int x,int y){
	while(x<=lim){
		ckmx(c[T][x],y);
		x+=low;
	}
}
int que(int T,int x){
	int res=-inf;
	while(x){
		ckmx(res,c[T][x]);
		x-=low;
	}
	return res;
}
void work(){
	cin>>n;
	For(i,1,n) cin>>a[i];
	For(i,1,n) a[i]+=a[i-1];
	For(i,0,n) b[i]=a[i];
	sort(b,b+1+n);
	lim=unique(b,b+1+n)-b;
	For(i,0,n) a[i]=lower_bound(b,b+lim,a[i])-b+1;
//	For(i,0,n) cerr<<a[i]<<" "; cerr<<endl;
	For(i,1,lim) c[0][i]=c[1][i]=-inf;
	For(i,1,lim) qwq[i]=-inf;
	add(0,a[0],0);
	add(1,1+lim-a[0],0);
	qwq[a[0]]=0;
	int x;
	For(i,1,n){
		x=qwq[a[i]];
		ckmx(x,que(0,a[i]-1)+i);
		ckmx(x,que(1,1+lim-(a[i]+1))-i);
		add(0,a[i],x-i);
		add(1,1+lim-a[i],x+i);
		qwq[a[i]]=x;
	}
	cout<<x<<endl;
}
signed main(){IOS;
	int T;cin>>T;
	while(T--)work();
return 0;}
```