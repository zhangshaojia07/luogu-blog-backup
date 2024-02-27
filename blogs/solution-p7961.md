# P7961 [NOIP2021] 数列 题解

### Description

$\{a\}$ 为值域 $0,1,\dots,m$ 长度为 $n$ 的序列，其权值为

$$\prod\limits_{i=1}^nv_{a_i}$$

输出所有满足 $popcnt(\sum\limits_{i=1}^n2^{a_i})\leqslant k$ 的 $\{a\}$ 的权值和。

### Analysis

这题目看起来就令人头大 qwq。

发现枚举 $\{a\}$ 每一个位置的值不可行（因为条件是关于权值的 $popcnt$），转而枚举 $0,1,\dots,m$ 各在 $\{a\}$ 中出现的次数 $b_0,b_1,\dots,b_m$，对答案的贡献要再乘上可重排列数

$$\dfrac{n!}{b_0!b_1!\dots b_m!}$$

接下来只要枚举 $\{b\}$ 即可，~~暴搜~~ DP 即可。

状态 $dp_{i,j,k,l}$ 表示当前遍历了 $b_0\to b_i$，$(S>>i)=j$，$popcnt(S\&((1<<i)-1))=k$，$\sum\limits_{p=0}^ib_p=n-l$ 的权值和。

其中 $S=\sum\limits_{p=0}^ib_p2^p$。

咸鱼篇幅，转移见代码。

时间复杂度 $O(m\times n\times n\times n\times n)$，前四项分别对应 DP 中的每一维，最后转移 $O(n)$。

### Detail

代码实现中将可重排列数嵌入到 DP 中。

我写的记忆化搜索。

### Code

```cpp
#include<bits/stdc++.h>
using namespace std;
#define ll long long
#define IOS ios::sync_with_stdio(0);cin.tie(0);cout.tie(0)
#define For(i,j,k) for(register int i=j;i<=k;i++)
#define Rof(i,j,k) for(register int i=j;i>=k;i--)
	const ll mod=998244353;
	inline ll pw(ll x,ll y){//快速幂
		ll r=1;
	    while(y){
	        if(y&1) r=r*x%mod;
	        x=x*x%mod;
	        y>>=1;
	    }
	    return r;
	}
	#define madd(a,b) {a=((a+b)%mod+mod)%mod;}
	#define inv(a) pw(a,mod-2)//逆元
#define N 33
#define M 110

int n,m,k;
ll dp[M][N][N][N];
bool vis[M][N][N][N];//记忆化
ll v[M][N];//{v_i}^j，为方便预处理
ll f[N],ivf[N];//f_i=i! ivf_i=inv(i!)
#define D dp[id][bit][had][lst]
#define V vis[id][bit][had][lst]
#define ppcnt __builtin_popcount
ll dfs(int id,int bit,int had,int lst){
	if(V) return D;
	V=1;
	if(had>k) return D=0;
	if(id==m+1){
		D=(!lst && had+ppcnt(bit)<=k);
		return D;
	}
	For(i,0,lst)
		madd(D,ivf[i]*v[id][i]%mod*dfs(id+1,(bit+i)>>1,had+((bit+i)&1),lst-i));
	return D;
}
void init(){
	f[0]=1;
	For(i,1,N-1)
		f[i]=f[i-1]*i%mod;
	ivf[N-1]=inv(f[N-1]);
	Rof(i,N-1,1)
		ivf[i-1]=ivf[i]*i%mod;
}
signed main(){
	IOS;
	init();
	cin>>n>>m>>k;
	For(i,0,m){
		v[i][0]=1; 
		cin>>v[i][1];
		For(j,2,N-1)
			v[i][j]=v[i][j-1]*v[i][1]%mod;
	}
	cout<<f[n]*dfs(0,0,0,n)%mod<<endl;
    return 0;
}
```