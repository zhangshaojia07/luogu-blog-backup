# CF1699D Almost Triple Deletions（众数）

结论题。

* * *

**结论：** 序列可以被删完当且仅当长度为偶数且众数出现次数不多于序列一半。

**证明：**（严谨的证明 CF 上有，这里就不放了。）

首先众数出现次数多于一半显然不行。

设众数为 $x$，值 $y$ 没出现在序列中过。 

反证法。假设 $x$ 出现次数不多于一半删完，**我们将除了 $x$ 外的数中选一些全部赋为 $y$，使得 $x,y$ 出现次数相同。**

照这样说，那 $x,y$ 岂不是都删不完吗？但是 $x,y$ 之间可以删啊！

矛盾，原命题成立。

* * *

于是就有了 DP 的想法：$f(i)$ 表示将区间 $[1,i]$ 删到只剩 $a_i$ 这种值，最后长度的最大值。

设 $a_0,a_{n+1}$ 为通配符，则答案为 $f(n+1)-1$。

考虑转移
$$
f(i)=1+\max_{j=0}^{i-1}[\text{Del}(j+1,i)\ \land\ a_i=a_j]f(j)
$$
其中 $\text{Del}(l,r)$ 表示 $[l,r]$ 区间是否可以被删完。

至此 $O(n^2)$ 解决。

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
const ll mod=1;
inline ll pw(ll x,ll y){ll r=1;while(y){if(y&1)r=r*x%mod;x=x*x%mod;y>>=1;}return r;}
#define int long long
#define N 5003
int cnt[N],a[N],f[N],n;
inline bool equ(int x,int y){return x==0 || y==n+1 || a[x]==a[y];}
void work(){
	cin>>n;
	For(i,1,n) cin>>a[i];
	fill(f,f+2+n,0);
	int mx;
	For(i,0,n){
		if(i && !f[i]) continue;
		fill(cnt+1,cnt+1+n,0);
		mx=0;
		For(j,i+1,n+1){
			if(((j^i)&1) && mx*2<=j-i && equ(i,j)) ckmx(f[j],f[i]+1);
			ckmx(mx,++cnt[a[j]]);
		}
	}
	cout<<f[n+1]-1<<endl;
}
int32_t main(){
	ios::sync_with_stdio(false);
	cin.tie(nullptr);
	int T;cin>>T; 
	while(T--)work();
return 0;}
```