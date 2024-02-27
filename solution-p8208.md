# P8208 [THUPC2022 初赛] 骰子旅行

### Analysis

有向图随机游走问题，本题**概率 DP** 没跑了。

一般地，设 $d_i$ 表示 $i$ 点的**出度**，$e_i$ 表示 $i$ **一步能到达的点集**，$|e_i|=d_i$。所有式子均在模 $998,244,353$ 意义下运算。

$f(s,i)$ 表示从 $i$ 开始用骰子走 $s$ 步，废话指数的期望值，那我们有

$$\begin{aligned}
f(0,i)&=0\\
f(s,i)&=\frac{1}{d_i}\sum_{j\in e_i}f(s-1,j)+j\cdot p(s-1,j,i)
\end{aligned}$$

其中 $p(s,x,y)$ 表示从点 $x$ 走 $s$ 步，**经过**（$1$ 次或以上均可）点 $y$ 的概率。

柿子解释：首先 $i$ 到 $e_i$ 中的点都是等概率的，所以每个分别计算取平均值即可；正常来说 $f(s-1,j)$ 就对了，除非又绕回了 $i$，这时以概率 $p(s-1,j,i)$ 多贡献 $j$。

时间 $O(Tn\sum\!m)$

### Code

```cpp
//Said no more counting dollars. We'll be counting stars.
#include<bits/stdc++.h>
using namespace std;
#define pb emplace_back
#define For(i,j,k) for(int i=j;i<=k;i++)
#define ll long long
const ll mod=998244353;
inline ll pw(ll x,ll y){ll r=1;while(y){if(y&1)r=r*x%mod;x=x*x%mod;y>>=1;}return r;}
inline void mad(ll &a,ll b){a=(a+b)%mod;while(a<0)a+=mod;}
inline void mmu(ll &a,ll b){a=a*b%mod;while(a<0)a+=mod;}
#define inv(a) pw(a,mod-2)
#define int long long
#define N 102
#define gc getchar
#define pc putchar
inline int read(){
	int x=0;char c=gc();bool f=0;
	while(!isdigit(c)){if(c=='-')f=1;c=gc();}
	while(isdigit(c)){x=(x<<3)+(x<<1)+c-48;c=gc();}
	if(f)x=-x;return x;
}
inline void write(int x){if(x<0){pc('-');x=-x;}if(x>9)write(x/10);pc(x%10+'0');}

int p[N][N][N];//i steps j -> k
int f[N][N];//i steps start from j
int n,S,m,co[N];
vector<int> e[N];
signed main(){
	n=read();S=read();m=read();
	int x,y;
	For(i,1,n){
		x=read();
		co[i]=inv(x);
		while(x--){
			y=read(); 
			e[i].pb(y);
		}
	}
	For(i,1,n) For(j,1,n) p[0][i][j]=(i==j);
	For(T,1,m){
		For(i,1,n){
			For(j,1,n){
				if(i==j){
					p[T][i][j]=1;
					continue;
				}
				p[T][i][j]=0;
				for(int k:e[i]) mad(p[T][i][j],p[T-1][k][j]);
				mmu(p[T][i][j],co[i]);
			}
		}
	}
	For(i,1,n) f[0][i]=0;
	For(T,1,m){
		For(i,1,n){
			f[T][i]=0;
			for(int j:e[i]){
				mad(f[T][i],f[T-1][j]+p[T-1][j][i]*j%mod);
			}
			mmu(f[T][i],co[i]);
		}
	}
	write(f[m][S]);
return 0;}
```