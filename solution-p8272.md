# P8272 [USACO22OPEN] Apple Catching G

### Analysis

设一奶牛的位置、时间为 $x',t'$，则苹果 $(x,t)$ 能被她接到当且仅当

$$\begin{cases}
t+x\ge t'+x'
\\
t-x\ge t'-x'
\end{cases}$$

所以对于每一个苹果或奶牛，其坐标为 $(t+x,t-x)$，这样就转化成二维数点问题了。

↓以下绿点表示苹果，红半平面交为奶牛。

![](https://cdn.luogu.com.cn/upload/image_hosting/b4zf2ysw.png)

我们有以下贪心策略（建议做一下 [P5894 [IOI2013]robots 机器人](https://www.luogu.com.cn/problem/P5894)，一样的做法）：

先按照 $x$ 降序排序奶牛，$x$ 相同的 $y$ 大的靠前。图中排序为 ABC。

再用 `std::set` 维护苹果，每次优先取 $x$ 坐标小的苹果。

这样 A 奶牛就会优先选区域 2 中的苹果，不会和 B 抢。

B 在 C 之前拿苹果，所以 C 不会先把 B 能拿的先抢走。

时间带 $\log$。

### Code

```cpp
//Said no more counting dollars. We'll be counting stars.
//#pragma GCC optimize("Ofast")
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

struct node{
	bool q;
	int x,y;
	mutable int cnt;
	friend bool operator<(node x,node y){return x.x<y.x;}
}a[N];
int n;
multiset<node> s;
multiset<node>::iterator it;
bool cmp(node x,node y){
	if(x.y!=y.y) return x.y>y.y;
	else return x.x>y.x;
}
signed main(){IOS;
	cin>>n;
	int opt,x,t;
	For(i,1,n){
		cin>>opt>>t>>x>>a[i].cnt;
		a[i].q=opt-1;
		a[i].x=t+x;
		a[i].y=t-x;
	}
	sort(a+1,a+1+n,cmp);
	int ans=0;
	For(i,1,n){
		if(!a[i].q){//cow
			while(a[i].cnt){
				it=s.lower_bound((node){0,a[i].x,0,0});
				if(it==s.end()) break;
				if(it->cnt>a[i].cnt){
					ans+=a[i].cnt;
					it->cnt-=a[i].cnt;
					break;
				}
				ans+=it->cnt;
				a[i].cnt-=it->cnt;
				s.erase(it);
			}
		}else{//apple
			s.insert(a[i]);
		}
	}
	cout<<ans<<endl;
return 0;}
```

