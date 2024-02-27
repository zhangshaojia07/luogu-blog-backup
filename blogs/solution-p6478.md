# P6478 [NOI Online #2 提高组] 游戏

树形背包 + 二项式反演 好题。

**注意这道题判情况不同的方法，回合之间交换顺序算同一种情况。**

发现「非平局回合」等价于两人选的点存在祖孙关系。

我们设 $f(i)$ 表示**恰好**有 $i$ 局非平局的方案数（也就是我们最终的答案），设 $g(i)$ 表示**钦定**有 $i$ 局非平局的方案数

当作您会了二项式反演，这里不再赘述**钦定**和**至少**的区别了。

由定义得到
$$
g(i)=\sum_{j=i}^m\binom{j}{i}f(j)
$$
二项式反演
$$
f(i)=\sum_{j=i}^m(-1)^{j-i}\binom{j}{i}g(j)
$$
也就是说我们只要求出 $g(0),g(1),\dots,g(m)$ 即可。

设 $dp^{(1)}_i$ 表示树上选择 $i$ 对祖孙关系异色点的方案数（点对之间顺序无关）。

我们有
$$
g(i)=(m-i)!dp^{(1)}_i
$$
接下来压力转到 $dp^{(1)}$。显然是树形背包。（因为 $^{(1)}$ 根节点角标都标好了）

背包转移时，我们先类似普通背包，再计算子树根节点能和子树内多少点配对。

记得用刷表法。

```cpp
//We'll be counting stars.
//#pragma GCC optimize("Ofast")
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
#define N 5006
char c[N];
int dp[N][N/2],s0[N],s1[N],lim[N],n,f[N/2],ivf[N/2];
vector<int> e[N];
void dfs(int rt,int fa){
	s1[rt]=c[rt]-'0';
	s0[rt]=s1[rt]^1;
	dp[rt][0]=1;
	for(int i:e[rt]) if(i!=fa){
		dfs(i,rt);
		s0[rt]+=s0[i];
		s1[rt]+=s1[i];
		Rof(j,lim[rt],0) For(k,1,lim[i]) (dp[rt][j+k]+=dp[rt][j]*dp[i][k]%mod)%=mod;
		lim[rt]+=lim[i];
	}
	int lst=(c[rt]=='0'?s1[rt]:s0[rt]);
	Rof(i,lim[rt],0) (dp[rt][i+1]+=dp[rt][i]*max(0ll,lst-i)%mod)%=mod;
	lim[rt]++;
	while(!dp[rt][lim[rt]]) lim[rt]--;
}
inline int P(int x){return (x&1)?mod-1:1;}
inline int C(int x,int y){return f[x]*ivf[y]%mod*ivf[x-y]%mod;}
int32_t main(){
	ios::sync_with_stdio(false);
	cin.tie(nullptr);
	cin>>n>>(c+1);
	int x,y,m=n/2;
	f[0]=1; For(i,1,m) f[i]=f[i-1]*i%mod;
	ivf[m]=pw(f[m],mod-2); Rof(i,m,1) ivf[i-1]=ivf[i]*i%mod;
	For(i,1,n-1){
		cin>>x>>y;
		e[x].pb(y);
		e[y].pb(x);
	}
	dfs(1,0);
//	For(i,0,lim[1]) cout<<dp[1][i]<<" "; cout<<endl;
	int ans;
	For(i,0,m){
		ans=0;
		For(j,i,m) (ans+=P(i+j)*C(j,i)%mod*dp[1][j]%mod*f[m-j]%mod)%=mod;
		cout<<ans<<endl;
	}
return 0;}
```