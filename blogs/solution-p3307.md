# P3307 [SDOI2013]项链

数论缝合怪 qwq。

### 写在前面

就怕你们直接按我的思路往下写了。

这里有模数的坑（我也初次见这种，学一下）。

剧透一下，最后的答案可以表示为 $\frac{1}{n}a$ 的形式，且 $a$ 中有用到 $6$ 的乘法逆元（称 Inv6）。

当 $mod\ |\ n$ 时 $\frac{1}{n}$ 逆元无意义，出锅了。

可是由于 $n<mod^2$，所以 $\frac{1}{n/mod}$ 有意义啊。

所以我们求 $a$ 时用 $mod^2$ 当模数，Inv6 也相应改。

由于最终方案数必为整数，所以 $n\ |\ a$，进一步 $mod\ |\ (a\%(mod^2))$。

所以答案这样求：
$$\begin{aligned}
ans&=a\cdot\frac{1}{n}
\\&=(a\%(mod^2)/mod)\cdot\frac{1}{n/mod}
\\&=(a\%(mod^2)/mod)Inv(n/mod)
\end{aligned}$$

最后 $a$ 用 $mod^2$ 当模数时中间会爆 int64，所以奉上龟速乘

```cpp
inline ll mul(ll x,ll y){return (x*y-(ll)((long double)x/Mod*y)*Mod+Mod)%Mod;}
```

### 求不同珍珠数量

由于珍珠可以旋转、翻转，所以我们要求的就是 $1\le x\le y\le z\le a$ 且 $\gcd(x,y,z)=1$ 的三元组 $(x,y,z)$ 的个数。

看到 $\gcd$ 想到莫反
$$\begin{aligned}
\sum_
{x,y,z}[\gcd(x,y,z)=1]&=\sum_
{x,y,z}\sum_{d|\!\gcd(x,y,z)}\mu(d)
\\&=\sum_
{x,y,z}\sum_{d|x,d|y,d|z}\mu(d)
\\&=\sum_
{d=1}^{a}\mu(d)\sum_{d|x,d|y,d|z}[x\le y\le z]
\\&=\sum_
{d=1}^{a}\mu(d)\sum_{x=1}^{\lfloor a/d\rfloor}\sum_{y=1}^{\lfloor a/d\rfloor}\sum_{z=1}^{\lfloor a/d\rfloor}[x\le y\le z]
\\&=\sum_
{d=1}^{a}\mu(d)S(\lfloor\frac{a}{d}\rfloor)
\end{aligned}$$
其中
$$
S(x)=\binom{x}{3}+2\binom{x}{2}+\binom{x}{1}
$$
表示 $[1,x]$ 中不同升序三元组的个数。

这部分就解决了。

### 求不同项链的数量

设我们求出来的不同珍珠数为 $m$。

这里要用到 Pólya 定理。

[P4980 【模板】Pólya 定理 我的题解](https://www.luogu.com.cn/blog/101868/solution-p4980)

通过模板题的学习（当然这道题多了翻转操作），得到答案为
$$
\frac{1}{n}\sum_{d|n}\varphi(\frac{n}{d})((m-1)^d+(-1)^d(m-1))
$$
DFS $n$ 的因数即可。

### Code

太冗长了。

```cpp
//We'll be counting stars.
#include<bits/stdc++.h>
using namespace std;
#define pb emplace_back
#define For(i,j,k) for(int i=(j),i##_=(k);i<=i##_;i++)
#define ll unsigned long long
const ll mod=1000000007ll,Mod=1000000014000000049ll,Inv6=833333345000000041ll;
inline ll mul(ll x,ll y){return (x*y-(ll)((long double)x/Mod*y)*Mod+Mod)%Mod;}
inline ll pw(ll x,ll y){
	ll r=1;
	while(y){
		if(y&1) r=mul(r,x);
		x=mul(x,x); y>>=1;
	}
	return r;
}
inline ll inv(ll x){
	ll res=1;
	For(i,0,29){
		if(((mod-2)>>i)&1) (res*=x)%=mod;
		x=x*x%mod;
	}
	return res;
}
const int N=1e7;
int a,mu[N+1];
ll n,m,ans,out;
vector<ll> p;
bool vis[N+1];
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
	For(i,1,N) mu[i]+=mu[i-1];
}
void getM(){
	auto S=[&](ll x)->ll{return mul(Inv6,mul(x,(2+mul(x,(3+x)%Mod))%Mod));};
	m=0;
	for(int l=1,r;l<=a;l=r+1){
		r=a/(a/l);
		(m+=mul((mu[r]+Mod-mu[l-1])%Mod,S(a/l)))%=Mod;
	}
}
ll b[20];
int tot,c[20];
void fen(ll x){
	tot=0;
	for(ll i:p){
		if(i*i>x) break;
		if(x%i) continue;
		b[++tot]=i;
		c[tot]=1;
		x/=i;
		while(x%i==0){
			x/=i;
			c[tot]++;
		}
	}
	if(x>1){
		b[++tot]=x;
		c[tot]=1;
	}
}
void dfs(int id,ll val,ll lst){
	if(id==tot+1){
		lst=n/lst;
		ll tmp=mul(val,(pw(m-1,lst)+mul((lst&1)?Mod-1:1,m-1))%Mod);
		(ans+=tmp)%=Mod;
		return ;
	}
	dfs(id+1,val,lst);
	val*=b[id]-1;
	For(i,1,c[id]){
		lst*=b[id];
		dfs(id+1,val,lst);
		val*=b[id];
	}
}
void solve(){
	fen(n);
	ans=0;
	dfs(1,1,1);
}
void work(){
	scanf("%lld%d",&n,&a);
	getM();
	solve();
	if(n%mod) out=ans%mod*inv(n%mod)%mod;
	else out=(ans/mod)*inv(n/mod)%mod;
	printf("%lld\n",out);
}
int main(){
	init();
	int T;scanf("%d",&T);
	while(T--)work();
return 0;}
```