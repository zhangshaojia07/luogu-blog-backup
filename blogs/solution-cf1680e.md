# CF1680E Moving Chips（最小斯坦纳树弱化版）

### Analysis

题目中的 Chips 消失机制即对应着在 $2\times n$ 的网格图中找这些 Chips 初始位置的[最小斯坦纳树](https://www.luogu.com.cn/problem/P6192)。

![](https://cdn.luogu.com.cn/upload/image_hosting/wiojcnzg.png)

当然这道题没有那个这么难。

***

**结论：** 此图必然有一个最小斯坦纳树，使得两个相邻列之间的 $2$ 条边最多选择 $1$ 条。

证明略（非常显然，读者自证）。

***

所以只要 $1\to n$ 遍历每一列，记录与上一列连接的是上边还是下边的最小边数，再按当前列 Chips 的状态分类递推（共 $4$ 中状态）。

时间 $O(n)$。

### Code

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
#define N 200010

char ch;
int n,b[N];
pair<int,int> a[N];
void work(){
	cin>>n;
	For(i,1,n){
		cin>>ch;
		a[i].fir=ch=='*';
	}
	For(i,1,n){
		cin>>ch;
		a[i].sec=ch=='*';
	}
	b[n+1]=0;
	Rof(i,n,1) b[i]=b[i+1]+a[i].fir+a[i].sec;
	bool flag=0;
	int a1=1,a2=1,b1,b2;
	For(i,1,n){
		if(!flag && b[i]-b[i+1]==0) continue;
		if(!flag){
			flag=1;
			if(a[i].fir) a2=2;
			if(a[i].sec) a1=2;
			continue;
		}
		if(b[i]==0) continue;
		if(a[i].fir==1 && a[i].sec==1){
			b1=b2=2+min(a1,a2);
		}
		if(a[i].fir==1 && a[i].sec==0){
			b1=1+min(a1,1+a2);
			b2=2+min(a1,a2);
		}
		if(a[i].fir==0 && a[i].sec==1){
			b1=2+min(a1,a2);
			b2=1+min(1+a1,a2);
		}
		if(a[i].fir==0 && a[i].sec==0){
			b1=1+min(a1,1+a2);
			b2=1+min(a2,1+a1);
		}
		a1=b1;
		a2=b2;
	}
	cout<<min(a1,a2)-1<<endl;
}
signed main(){IOS;
	int T;cin>>T;
	while(T--)work();
return 0;}
```

