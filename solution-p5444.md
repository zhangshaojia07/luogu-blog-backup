# P5444 [APIO2019]奇怪装置（等价类循环节、环形区间覆盖）

## Analysis

显然随着 $t$ 的增大，$(x,y)$ 是有循环节的。

但是循环节有多长呢？

### 求最小循环节长度

* * *
**引理：**
$$
A\ |\ BC
\iff
\frac{A}{\gcd(A,B)}\ |\ C
$$
* * *

这里我们称 $(x,y)$ 相同的 $t$ 的集合为等价类。

$r<s\ (\in\mathbb{N})$ 为两个等价的时刻，当且仅当
$$\begin{aligned}
r+\left\lfloor\frac{r}{B}\right\rfloor
&\equiv
s+\left\lfloor\frac{s}{B}\right\rfloor
&\pmod{A}
\quad
&(1)
\\
r
&\equiv
s
&\pmod{B}
\quad
&(2)
\end{aligned}$$
由 $(2)$ 设 $s=kB+r\ (k\in\mathbb{N}^+)$，则 $(1)$ 式改为

（以下全部为 $\pmod{A}$）
$$\begin{aligned}
&
r+\left\lfloor\frac{r}{B}\right\rfloor
\equiv
s+\left\lfloor\frac{s}{B}\right\rfloor
\\
\iff\quad&
r+\left\lfloor\frac{r}{B}\right\rfloor
\equiv
kB+r+\left\lfloor\frac{kB+r}{B}\right\rfloor
\\
\iff\quad&
r+\left\lfloor\frac{r}{B}\right\rfloor
\equiv
kB+r+\left\lfloor\frac{r}{B}+k\right\rfloor
\\
\iff\quad&
r+\left\lfloor\frac{r}{B}\right\rfloor
\equiv
kB+r+\left\lfloor\frac{r}{B}\right\rfloor+k
\\
\iff\quad&
kB+k
\equiv
0
\\
\iff\quad&
k(B+1)
\equiv
0
\\
\iff\quad&
A\ |\ k(B+1)
\\
\iff\quad&
\frac{A}{\gcd(A,B+1)}\ |\ k
\end{aligned}$$

由于我们要求**最小**循环节长度 $kB$，所以要 $k$ 最小，为
$$
\frac{A}{\gcd(A,B+1)}
$$
最小循环节为
$$
\frac{AB}{\gcd(A,B+1)}
$$

### 环形区间覆盖问题

设我们求出的最小循环节为 $d$。

若有一个 $r_i-l_i+1\ge d$，则所有可能的 $(x,y)$ 均被取到，输出 $d$ 即可。

否则，就是在同余系中的区间覆盖问题了。

接下来有多种写法，我这里用排序 + 求连续段的方法，具体看代码。

## Detail

最小循环节可能会爆 int64，所以建议用 long double 判断是否 $>10^{18}$，若是则设为 $10^{18}$。（因为循环节太长相当于没循环）

## Code

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
#define N -1

const long double Lim=1.1e18;
int n,A,B;
long double mul(int x,int y){
	long double xx=x,yy=y;
	return xx*yy;
}
vector<pair<int,int> > v;
signed main(){IOS;
	cin>>n>>A>>B;
	A/=__gcd(A,B+1);
	if(mul(A,B)>=Lim) A=Lim;//循环节 > 1e18，相当于没有循环 
	else A*=B;
	int x,y;
	For(i,1,n){
		cin>>x>>y;
		if(y-x+1>=A){
			cout<<A<<endl;
			return 0;
		}
		x%=A;
		y%=A;
		if(x<=y){
			v.pb(mkp(x,y));
		}else{
			v.pb(mkp(x,A-1));
			v.pb(mkp(0,y));
		}
	}
	sort(v.begin(),v.end());
	int lst=-1,now=-2,ans=0;
	for(auto i:v)
		if(i.fir<=now){
			ckmx(now,i.sec);
		}else{
			ans+=now-lst+1;
			lst=i.fir;
			now=i.sec;
		}
	ans+=now-lst+1;
	cout<<ans<<endl;
return 0;}
```