# SP4168 SQFREE - Square-free integers（莫比乌斯函数 + 奇葩整除分块）

因为
$$
\sum_{i|n}\mu(i)=[n=1]
$$
其实是判断 $n$ 是否有非 $1$ 因子。

则推广
$$
\sum_{i^2|n}\mu(i)=[n\text{ square free}]
$$
判断 $n$ 是否有非 $1$ 平方因子。

我们最后的答案
$$\begin{aligned}
\sum_{i=1}^n[i\text{ square free}]&=\sum_{i=1}^n\sum_{j^2|i}\mu(j)
\\&=\sum_{j=1}^{\lfloor\sqrt{n}\rfloor}\sum_{j^2|i}\mu(j)
\\&=\sum_{i=1}^{\lfloor\sqrt{n}\rfloor}\mu(i)\left\lfloor\frac{n}{i^2}\right\rfloor
\end{aligned}$$
通过线性筛 $\mu$ 再作前缀和与整除分块可做到 $O(n^{1/2}+Tn^{1/4})$。

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
#define debug(...) cerr<<"#"<<__LINE__<<": "<<__VA_ARGS__<<endl
#define ll long long
const ll mod=1;
inline ll pw(ll x,ll y){ll r=1;while(y){if(y&1)r=r*x%mod;x=x*x%mod;y>>=1;}return r;}
#define int ll
#define N 10000000
int n,mu[N+1];
bool vis[N+1];
vector<int> p;
void init(){
	mu[1]=1;
	For(i,2,N){
		if(!vis[i]){
			mu[i]=-1;
			p.pb(i);
		}
		for(int j:p){
			if(i*j>N) break;
			vis[i*j]=1;
			if(i%j==0){
				mu[i*j]=0;
				break;
			}else{
				mu[i*j]=-mu[i];
			}
		}
	}
	For(i,2,N) mu[i]+=mu[i-1];
}
void work(){
	cin>>n;
	int ans=0;
	for(int l=1,r;l*l<=n;l=r+1){
		r=sqrt(n/(n/(l*l)));
		ans+=(n/(l*l))*(mu[r]-mu[l-1]);
	}
	cout<<ans<<endl;
}
signed main(){
	ios::sync_with_stdio(false),cin.tie(nullptr);
	init();
	int T;cin>>T;
	while(T--)work(); 
return 0;}
```