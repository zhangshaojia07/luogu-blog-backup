# P4980 【模板】Pólya 定理（DFS 优化因数 Phi）

[**本优化喜提最优解 50 ms。**](https://www.luogu.com.cn/record/77674348)

**很抱歉，本题解不讲任何数学部分内容**，只讲求得
$$
\text{Ans}=\frac{1}{n}\sum_{d|n}\varphi(d)n^{n/d}
$$
后如何优化程序。

首先，$\frac{1}{n}$ 可以通过快速幂快速求得，所以只考虑后面的求和即可。

## 朴素做法

首先，我们得枚举 $n$ 的因数 $d$，然后对于每一个 $d$，我们要 $O(\log n/d)$ 求快速幂和 $O(\sqrt{d})$ 求单点的 $\varphi$。

这样总体时间大概为 $O(\text{d}(n)\sqrt{n})$（好像被证明可以更快？不管了），$\text{d}(n)$ 表示 $n$ 的正因数个数。

## DFS 做法

发现「单点求 $\varphi$」太慢啦。尝试将 $n$ 因数的 $\varphi$ 统一做。

$\pi(x)$ 表示 $\le x$ 的数中素数的个数，即
$$
\pi(x)=\sum_{i=1}^{x}[i\in prime]
$$
我们还是 $O(\pi(\sqrt{n}))$ 得出
$$
n=a_1^{b_1}a_2^{b_2}\dots a_t^{b_t}\quad(a_i\in prime)
$$
（唯一分解定理）

可以得到 $t\le 9$（前 $10$ 个素数乘积 $>10^9$）。

$n$ 的因数可以表示为
$$
a_1^{c_1}a_2^{c_2}\dots a_t^{c_t}\quad(0\le c_i\le b_i)
$$
所以我们按 $a_1\to a_n$ DFS 这些指数 $\{c\}$。

当一个素数 $a_i$ 第一次加入时，乘 $(a_i-1)$，否则乘 $a_i$，这样我们就 $O(\text{d}(n))$ 得到了所有因数的 $\varphi$。

再用快速幂搞定 $n^{n/d}$ 即可。

总时间 $O(\sqrt{n}+2\pi(\sqrt{n})+\displaystyle\frac{1}{2}\text{d}(n)\log n)$。

（其实常系数没用，但是还是象征性地写上了，第一项为线性筛素数的时间）

Upd：感谢 @[pzq_loves_qwq](https://www.luogu.com.cn/user/384214) 提供的化简思路，我写写看。
$$\begin{aligned}
\sum_{d|n}\log d&=\frac{1}{2}\sum_{d|n}\log d+\log\frac{n}{d}
\\&=\frac{1}{2}\sum_{d|n}2\log \sqrt{n}
\\&=\frac{1}{2}\sum_{d|n}\log n
\\&=\frac{1}{2}\text{d}(n)\log n
\end{aligned}$$
## Code

~~如果有人比我跑得快请踹我。~~

```cpp
#include<stdio.h>
using namespace std;
#define ll long long
const ll mod=1e9+7;
inline ll pw(ll x,ll y){ll r=1;while(y){if(y&1)r=r*x%mod;x=x*x%mod;y>>=1;}return r;}
#define N 31628
int n,m,p[N],len=0,a[10],b[10],tot;
bool vis[N];
void init(){
	for(int i=2;i<N;i++){
		if(!vis[i]) p[++len]=i;
		for(int x=1,j;x<=len && (j=p[x])*i<N;x++){
			vis[i*j]=1;
			if(i%j==0) break;
		}
	}
}
ll ans;
void dfs(int id,int val,int lst){
	if(id==tot+1){
		(ans+=pw(n,n/lst)*val)%=mod;
		return ;
	}
	dfs(id+1,val,lst);
	int x=a[id];
	val*=(x-1);
	for(int i=1,j=b[id];i<=j;i++){
		lst*=x;
		dfs(id+1,val,lst);
		val*=x;
	}
}
void work(){
	scanf("%d",&n);
	m=n;
	ans=tot=0;
	for(int x=1,i;(i=p[x])*i<=m;x++) if(m%i==0){
		a[++tot]=i;
		b[tot]=1;
		m/=i;
		while(m%i==0) b[tot]++,m/=i;
	}
	if(m!=1) a[++tot]=m,b[tot]=1;
	dfs(1,1,1);
	(ans*=pw(n,mod-2))%=mod;
	printf("%lld\n",ans);
}
int main(){
	init();
	int T;scanf("%d",&T);
	while(T--)work();
return 0;}
```