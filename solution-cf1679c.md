# CF1679C Rooks Defenders（树状数组）

### Analysis

* * * 

**结论：** 若一个子矩阵中的格子均可以被攻击到，则子矩阵中的格子均可以被横向攻击到，或者均可以被纵向攻击到。

**证明：** 反证法，假设有位置 $(x_1,y_1)$ 只能被横向攻击到，位置 $(x_2,y_2)$ 只能被纵向攻击到。

则 $y_1$ 这一列全部是没有被纵向攻击的，$x_2$ 这一行全部是没有被横向攻击的，所以 $(x_2,y_1)$（必然在子矩阵内）没有被任何攻击到。

矛盾，原命题成立。

* * *

所以，我们对于行（列同理）记录「这一行是（$1$）否（$0$）被横向攻击」。

接下来就等价于区间询问是否全为 $1$，这就是树状数组 / 线段树模板了。

### Code

本人用的树状数组。

```cpp
//Said no more counting dollars. We'll be counting stars.
#pragma GCC optimize("Ofast")
//#pragma GCC optimize("unroll-loops")//DONT use rashly,I have suffered
//#pragma GCC target("sse,sse2,sse3,ssse3,sse4,popcnt,abm,mmx,avx,avx2,tune=native")//DONT use rashly,I have suffered
#include<bits/stdc++.h>
using namespace std;
#define IOS ios::sync_with_stdio(0);cin.tie(0);cout.tie(0)
#define fir first
#define sec second
#define mkp make_pair
#define pb emplace_back
#define mem(x,y) memset(x,y,sizeof(x))
#define For(i,j,k) for(int i=j;i<=k;i++)
#define Rof(i,j,k) for(int i=j;i>=k;i--)
#define Fe(x,y) for(int x=head[y];x;x=e[x].nxt)
#define ckmx(a,b) a=max(a,b)
#define ckmn(a,b) a=min(a,b)
#define fin(s) freopen(s,"r",stdin)
#define fout(s) freopen(s,"w",stdout)
#define file(s) fin(s".in");fout(s".out")
#define cerr cerr<<'_'
#define debug cerr<<"Passed line #"<<__LINE__<<endl
template<typename T>T ov(T x){cerr<<"Value: "<<x<<endl;return x;}
#define ll long long
const ll mod=1000000007;
inline ll pw(ll x,ll y){ll r=1;while(y){if(y&1)r=r*x%mod;x=x*x%mod;y>>=1;}return r;}
inline void mad(ll &a,ll b){a=(a+b)%mod;while(a<0)a+=mod;}
inline void mmu(ll &a,ll b){a=a*b%mod;while(a<0)a+=mod;}
#define inv(a) pw(a,mod-2)
#define int long long
#define N 100010
#define low (x&(-x))
int a[N],b[N],c[N],d[N],n;
void add(int x,int y){
	while(x<=n){
		c[x]+=y;
		x+=low;
	}
}
void Add(int x,int y){
	while(x<=n){
		d[x]+=y;
		x+=low;
	}
}
int que(int x){
	int res=0;
	while(x){
		res+=c[x];
		x-=low;
	}
	return res;
}
int Que(int x){
	int res=0;
	while(x){
		res+=d[x];
		x-=low;
	}
	return res;
}
signed main(){IOS;
int q;
	cin>>n>>q;
	int opt,x,y,xx,yy;
	while(q--){
		cin>>opt>>x>>y;
		if(opt==1){
			if(!a[x]) add(x,1);
			if(!b[y]) Add(y,1);
			a[x]++;
			b[y]++;
		}else if(opt==2){
			a[x]--;
			b[y]--;
			if(!a[x]) add(x,-1);
			if(!b[y]) Add(y,-1);
		}else{
			cin>>xx>>yy;
			if(que(xx)-que(x-1)==xx-x+1 || Que(yy)-Que(y-1)==yy-y+1) cout<<"Yes"<<endl;
			else cout<<"No"<<endl;
		}
	}
return 0;}
```