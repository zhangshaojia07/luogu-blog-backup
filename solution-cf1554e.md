# CF1554E You

问题转化成将树的边定向，一个点的 $a$ 相当于她的出度。

注意到当 $k$ 很大的时候，我们是很难构造出解的。

具体地，当 $k\ge 2$ 时，答案至多为 $1$。原因是我们从叶往根考虑每一条边的方向。叶子向上的那些边一定是向下的（因为叶子的出度一定要为 $0$），然后一个点的所有儿子边都确定了，由于出度要整除 $k$，父亲边的方向就呼之欲出了（或者无解）。

以下称对 $k$ 进行上述操作为 $k$ 操作。

我们发现 $x$ 操作后可能 $\gcd$ 不是 $x$，而是 $x$ 的某个倍数。我们得到：所有为 $x$ 正整数倍的 $k$ 中只有这个求出 $\gcd$ 的是 $1$，否则是 $0$。

所以就类似埃氏筛。由于 $\sum a_i=n-1$，所以有解的 $k$ 一定是 $n-1$ 的因数。

我们对于所有 $n-1$ 的素因数进行操作，这样除了 $\gcd=1$ 的答案就都知道了。

最后由于 $\sum ans_i=2^{n-1}$（每条边两种方向），得到 $ans_1$。

时间 $O(n\cdot \omega(n))$，其中 $\omega$ 表示素因数个数，在 $n=10^5$ 下很小。

```cpp
//We'll be counting stars.
#pragma GCC optimize("Ofast")
#include<bits/stdc++.h>
using namespace std;
#define fir first
#define sec second
#define mkp make_pair
#define pb emplace_back
#define For(i,j,k) for(int i=(j),i##_=(k);i<=i##_;i++)
#define Rof(i,j,k) for(int i=(j),i##_=(k);i>=i##_;i--)
#define ckmx(a,b) a=max(a,b)
#define ckmn(a,b) a=min(a,b)
#define Fi(s) freopen(s,"r",stdin)
#define Fo(s) freopen(s,"w",stdout)
#define Fre(s) Fi(s".in"),Fo(s".out")
#define debug cerr<<"Line#"<<__LINE__<<endl
#define ll long long
const ll mod=998244353;
inline ll pw(ll x,ll y){ll r=1;while(y){if(y&1)r=r*x%mod;x=x*x%mod;y>>=1;}return r;}
#define int long long
#define N 100005
int n,qwq;
bool vis[N],ans[N],flag;
vector<int> e[N],p;
inline int gcd(int x,int y){
	if(!y) return x;
	return gcd(y,x%y);
}
bool dfs(int rt,int fa,int val){
	if(!flag) return 0;
	int cnt=0;
	for(int i:e[rt]) if(i!=fa) cnt+=dfs(i,rt,val);
	if(cnt%val!=0 && (!fa || (cnt+1)%val!=0)) flag=0;
	if(cnt%val==0){
		qwq=gcd(qwq,cnt);
		return 1; 
	}else{
		qwq=gcd(qwq,cnt+1);
		return 0;
	}
}
void work(){
	cin>>n;
	For(i,1,n) ans[i]=false;
	For(i,1,n) e[i].clear();
	int x,y;
	For(i,1,n-1){
		cin>>x>>y;
		e[x].pb(y);
		e[y].pb(x);
	}
	x=n-1;
	for(int i:p){
		if(x==1) break;
		if(x%i) continue;
		while(x%i==0) x/=i;
		qwq=0;
		flag=1;
		dfs(1,0,i);
		if(flag) ans[qwq]=1;	
	}
	int tmp=pw(2,n-1);
	For(i,2,n) if(ans[i]) (tmp+=mod-1)%=mod;
	cout<<tmp; For(i,2,n) cout<<" "<<ans[i]; cout<<endl;
}
int32_t main(){
	ios::sync_with_stdio(false);
	cin.tie(nullptr);
	For(i,2,N-1) if(!vis[i]){
		p.pb(i);
		for(int j=i*i;j<N;j+=i) vis[j]=1;
	}
	int T;cin>>T;
	while(T--)work(); 
return 0;}
```