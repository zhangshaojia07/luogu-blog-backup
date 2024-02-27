# CF1713D Tournament Countdown

我不知道当时为啥想了这么久 /kk。

我们发现在淘汰赛二叉树上将每个叶子向上将边涂成蓝色，直到输。则询问等价于两个叶子向上蓝色线段长度的比较。

发现很难不递归做，设我们询问次数为 $T(2^n)=2^{n-b}T(2^{n-a})$。

经尝试，发现 $a=1$ 难做，进一步考虑 $a=2$。

要做到 $\lceil\frac{1}{3}2^{n+1}\rceil$ 次询问，我们 $b$ 至少为 $1$，发现 $b=1$ 可做，具体如下。

先将目前的选手每相邻 $4$ 个分为一组，我们只要在每一组中询问 $2$ 次得出组内赢家即可。

![](https://cdn.luogu.com.cn/upload/image_hosting/91oviab0.png)

我们第一次询问 $(0,2)$。

* 若两者等长，由于这个子树内最终只能有一位赢家，所以得出 $0,2$ 两个节点均在第一轮死掉。只需要再询问一下 $(1,3)$ 谁长即为组内赢家。

* 若两者不等长，不妨设 $0$ 比 $2$ 长，推导得到 $0$ 一定打败了 $1$，且 $3$ 打败了 $2$，进一步询问 $(0,3)$ 即得出组内赢家。

完结。

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
#define debug(...) cerr<<"#"<<__LINE__<<": "<<__VA_ARGS__<<endl
#define ll long long
const ll mod=1;
inline ll pw(ll x,ll y){ll r=1;while(y){if(y&1)r=r*x%mod;x=x*x%mod;y>>=1;}return r;}
#define int ll
#define N 131100
int a[N],b[N];
int que(int x,int y){
	cout<<"? "<<x<<" "<<y<<endl;
	cout.flush();
	int z;
	cin>>z;
	return z;
}
int solve(int n){
	if(n==0) return a[0];
	if(n==1) return que(a[0],a[1])==1?a[0]:a[1];
	int x;
	for(int i=0,j=0;i<(1<<n);i+=4,j++){
		x=que(a[i],a[i+2]);
		if(x==1){
			b[j]=(que(a[i],a[i+3])==1?a[i]:a[i+3]);
		}else if(x==2){
			b[j]=(que(a[i+1],a[i+2])==2?a[i+2]:a[i+1]);
		}else{
			b[j]=(que(a[i+1],a[i+3])==1?a[i+1]:a[i+3]);
		}
	}
	For(i,0,(1<<(n-2))-1) a[i]=b[i];
	return solve(n-2);
}
int n;
void work(){
	cin>>n;
	iota(a,a+(1<<n),1);
	int res=solve(n);
	cout<<"! "<<res<<endl;
	cout.flush();
}
signed main(){ios::sync_with_stdio(false),cin.tie(nullptr);
	int T;cin>>T;
	while(T--)work();
return 0;}
```