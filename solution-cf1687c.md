# CF1687C Sanae and Giant Robot

首先将 $a_i$ 减去 $b_i$，这样就转化为 $b$ 序列全零的问题了，也就是说：

* * *

给一些区间，当区间内和为 $0$ 的时候可以将区间所有数全部置为 $0$，求是否可以将整个序列全部变为 $0$。

* * *

但是这样还是不好搞，因为区间之间的操作顺序影响结果。

由于是区间和之类的问题，我们想到前缀和。

设 $a$ 的前缀和序列为 $c_0(=0),c_1,c_2,\dots,c_n$，则问题转化为：

* * *

给一些区间 $(l,r]$（左开右闭），当 $c_l=c_r$ 的时候可以将区间内所有 $c_i$ 全部置为 $c_l(=c_r)$，求是否可以将整个序列全部变为 $0$。

* * *

我们发现若 $c_l=c_r\ne 0$，这样操作的话是没有意义的，因为操作完后不会新增 $c$ 序列中的 $0$。

紧接着，发现这些区间设为 $0$ 的操作顺序是无关的。

所以我们存一个队列，表示当前有哪些区间的 $c_l=c_r=0$，我们依次操作，然后判断是否有新增的这样的区间，把她们加进队列。

具体地，对每一个位置开 vector 表示这个位置有哪些区间的端点。同时开序列上的并查集，可以 $O(\alpha(n))$ 跳过连续的 $0$ 区间。由于每一个位置变为 $0$ 后不会再变回非 $0$，所以时间复杂度是 $O(m+n\alpha(n))$。

最后判断一下 $c$ 是否全零即可。

```cpp
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

int n,m,a[N],cnt[N],f[N],R[N];
pair<int,int> b[N];
queue<int> q; 
vector<int> id[N];
inline int gf(int x){return f[x]==x?x:f[x]=gf(f[x]);}
void work(){
	cin>>n>>m;
	For(i,1,n) cin>>a[i];
	int x,y,lim;
	For(i,1,n){
		cin>>x;
		a[i]-=x;
	}
	For(i,1,n) a[i]+=a[i-1];
	For(i,1,m) cin>>b[i].fir>>b[i].sec;
	For(i,1,m) b[i].fir--;
	For(i,1,m) cnt[i]=(a[b[i].fir]!=0)+(a[b[i].sec]!=0);
	For(i,0,n) id[i].clear();
	For(i,1,m) id[b[i].fir].pb(i);
	For(i,1,m) id[b[i].sec].pb(i);
	For(i,1,m) if(!cnt[i]) q.push(i);
	For(i,0,n)
		if(a[i]) f[i]=i;
		else f[i]=i+1;
	f[n+1]=n+1;
	while(!q.empty()){
		x=q.front();
		q.pop();
		y=gf(b[x].fir);
		lim=b[x].sec;
		while(y<=lim){
			for(int i:id[y]){
				cnt[i]--;
				if(!cnt[i]) q.push(i);
			}
			f[y]=y+1;
			y=gf(y+1);
		}
	}
	cout<<(gf(0)==n+1?"YES":"NO")<<endl;
}
signed main(){IOS;
	int T;cin>>T;
	while(T--)work();
return 0;}
```