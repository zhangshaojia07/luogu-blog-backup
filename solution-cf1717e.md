# CF1717E Madoka and The Best University（数论）

真正的 $O(n)$ 线性做法。

先来 dirty work：
$$
\begin{aligned}
&\sum_{c=1}^{n-2}\sum_{a=1}^{n-c-1}\text{lcm}(c,\gcd(a,n-c-a))
\\=& \sum_{c=1}^{n-2}\sum_{a=1}^{n-c-1}\text{lcm}(c,\gcd(a,n-c))
\\=& \sum_{c=1}^{n-2}\sum_{d=1}^{n}\sum_{a=1}^{n-c-1}[\gcd(a,n-c)=d]\text{lcm}(c,d)
\\=& \sum_{c=1}^{n-2}\sum_{d=1}^{n}[d|n-c]\text{lcm}(c,d)\sum_{a=1}^{\lfloor\frac{n-c-1}{d}\rfloor}[\gcd(a,\frac{n-c}{d})=1]
\\=& \sum_{c=1}^{n-2}\sum_{d=1}^{n}[d|n-c]\text{lcm}(c,d)\varphi(\frac{n-c}{d})[\frac{n-c}{d}>1]
\\=& \sum_{c=2}^{n}\sum_{d=1}^{n}[d|c]\text{lcm}(n-c,d)\varphi(\frac{c}{d})[\frac{c}{d}>1]
\\=& \sum_{d=1}^{n}\sum_{c=2}^{\lfloor\frac{n}{d}\rfloor}\text{lcm}(n-cd,d)\varphi(c)
\\=& \sum_{c=2}^{n}\varphi(c)\sum_{d=1}^{\lfloor\frac{n}{c}\rfloor}\text{lcm}(n-cd,d)
\\=& \sum_{i=2}^{n}\varphi(i)\sum_{j=1}^{\lfloor\frac{n}{i}\rfloor}\frac{j(n-ij)}{\gcd(n-ij,j)}
\\=& \sum_{i=2}^{n}\varphi(i)\left(\sum_{j=1}^{\lfloor\frac{n}{i}\rfloor}\text{lcm}(n,j)-i\sum_{j=1}^{\lfloor\frac{n}{i}\rfloor}\frac{j^2}{\gcd(n,j)}\right)
\end{aligned}
$$
不妨设
$$
S(x)=\sum_{i=1}^{x}\text{lcm}(n,i)\quad T(x)=\sum_{i=1}^{x}\frac{i^2}{\gcd(n,i)}
$$
则最终结果为
$$
\sum_{i=2}^{n}\varphi(i)\left(S(\lfloor\frac{n}{i}\rfloor)-i\cdot T(\lfloor\frac{n}{i}\rfloor)\right)
$$

先线性筛出 $\varphi(x),mp(x)$（$mp$ 为最小素因子），就可以线性求出 $\gcd(n,x),S(x),T(x)$，再 $O(n)$ 求得最终答案。

```cpp
/*
* Author: ShaoJia
* Last Modified time: 2022-09-03 12:26:17
* Motto: We'int be counting stars.
*/
#pragma GCC optimize("Ofast")
#include<bits/stdc++.h>
using namespace std;
#define For(i,j,k) for(int i=(j),i##_=(k);i<=i##_;i++)
#define Rof(i,j,k) for(int i=(j),i##_=(k);i>=i##_;i--)
#define int long long
const int mod=1e9+7;
inline int pw(int x,int y){int r=1;while(y){if(y&1)r=r*x%mod;x=x*x%mod;y>>=1;}return r;}
#define N 100010
int s[N],t[N],phi[N],n,g[N],mp[N],f[N],ivf[N],p[N],pt=0;
bool vis[N];
signed main(){ios::sync_with_stdio(false),cin.tie(nullptr);
	cin>>n;
	f[0]=1; For(i,1,n) f[i]=f[i-1]*i%mod;
	ivf[n]=pw(f[n],mod-2); Rof(i,n,1) ivf[i-1]=ivf[i]*i%mod;
	phi[1]=1;
	int x;
	For(i,2,n){
		if(!vis[i]){
			p[++pt]=i;
			phi[i]=i-1;
			mp[i]=i;
		}
		For(j,1,pt){
			x=p[j];
			if(i*x>n) break;
			vis[i*x]=1;
			mp[i*x]=x;
			if(i%x){
				phi[i*x]=phi[i]*(x-1);
			}else{
				phi[i*x]=phi[i]*x;
				break;
			}
		}
	}
	g[1]=1;
	For(i,2,n){
		g[i]=g[i/mp[i]];
		if(n%(g[i]*mp[i])==0) g[i]*=mp[i];
	}
	For(i,1,n) s[i]=(s[i-1]+n/g[i]*i)%mod;
	For(i,1,n) t[i]=(t[i-1]+i*i%mod*ivf[g[i]]%mod*f[g[i]-1])%mod;
	int ans=0;
	For(i,2,n) (ans+=(s[n/i]-i%mod*t[n/i]%mod+mod)%mod*phi[i])%=mod;
	cout<<ans<<endl;
return 0;}
```