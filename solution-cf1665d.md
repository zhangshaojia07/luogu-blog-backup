# CF1665D GCD Guess

### Preface

为啥要 CRT 啊，二进制香啊。

### Analysis

由于 $a\ne b$，我们规定 $a<b$。

显然 $\gcd(x+a,x+b)=\gcd(b-a,x+a)$。

这样，假设我们知道了 $x\ \bmod 2^k=r$，我们询问 $a=2^k-r,b=2^{k+1}-r$ 就得到

$$\gcd(2^k,x-r+2^k)=(x-r)\ \text{AND}\ 2^k$$

（$\text{AND}$ 表示按位与）

即得到了 $x$ 二进制下的第 $k$ 位（从 $0$ 标号），进一步得到 $x\ \bmod 2^{k+1}$，递推做即可。

这样正好 $30$ 次询问，但是最后一次询问 $b$ 最大达到 $2^{31}>2\cdot10^9$。

若当前值 $r$ 加上 $2^{29}$ 大于 $10^9$，则这一位必然是 $0$，判掉。

若当前 $r=0$，则这一位必然是 $1$，判掉。（因为题目保证 $x>0$）

询问 $a=r+2^{29},b=2(r+2^{29})$，得到

$$\gcd(r+2^{29},x+r+2^{29})=\gcd(r+2^{29},x)$$

即可判断 $x$ 是否含有 $2^{29}$ 这一位。

而且此时 $b=2(r+2^{29})\le 2\cdot10^9$，满足。

### Code

[Link](https://codeforces.com/contest/1665/submission/153106978)

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

const int inf=1e9;
inline int que(int x,int y){
	cout<<"? "<<x<<" "<<y<<endl;
	cout.flush();
	int res;
	cin>>res;
	return res;
}
void work(){
	int ans=0,res,x;
	For(i,0,28){
		x=(1ll<<(i+1))-ans;
		res=que(x,x+(1ll<<(i+1)));
		if(res==(1ll<<i)) ans+=(1ll<<i);
	}
	if(ans+(1ll<<29)<=inf){
		x=ans+(1ll<<29);
		if(!ans) ans=(1ll<<29);
		else if(que(x,x*2)==x){
			ans+=(1ll<<29);
		}
	}
	cout<<"! "<<ans<<endl;
	cout.flush();
}
signed main(){IOS;
	int T;cin>>T;
	while(T--)work();
return 0;}
```