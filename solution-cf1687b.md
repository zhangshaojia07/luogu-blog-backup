# CF1687B Railway System（Kruskal 变式）

首先我们可以 $m$ 次询问得到 $m$ 条边权。

然后参照 Kruskal 算法流程。

我们将边按照边权升序排序，依次判断两端点是否已经在同一并查集里。

具体地，假设当前枚举到边 $e$，边权为 $x$，前面已经确认在最小生成树（或森林）的边集为 $E$，$E$ 中的边权和为 $y$。

询问一下 $E\cup e$ 的最大生成树（森林），设为 $z$。若 $z=x+y$，则说明 $e$ 的两端不在同一个并查集内，此时我们将 $e$ 加入 $E$ 即可。

* * *

若 $e$ 的两端不在同一个并查集内，则树上的某条路径和 $e$ 组成了一个环，我们必然在环上删掉一条边，由于边权为正，$z<x+y$，矛盾。

* * *

否则，不将 $e$ 加入 $E$。

这样我们不超过 $m+m=2m$ 次求出了答案。

```cpp
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
#define N 502

struct node{
	int val,id;
	friend bool operator<(node x,node y){
		return x.val<y.val;
	}
}a[N];
int n,m;
int que(string s){
	cout<<"? "<<s<<endl;
	cout.flush();
	static int x;
	cin>>x;
	return x;
}
signed main(){IOS;
	cin>>n>>m;
	string s;
	For(i,1,m) s=s+'0';
	For(i,1,m){
		s[i-1]='1';
		a[i].val=que(s);
		s[i-1]='0';
	}
	For(i,1,m) a[i].id=i;
	sort(a+1,a+1+m);
	int ans=0,res,lst=0;
	For(i,1,m){
		s[a[i].id-1]='1';
		res=que(s);
		if(res!=lst+a[i].val){
			s[a[i].id-1]='0';
		}else{
			lst=res;
			ans+=a[i].val;
		}
	}
	cout<<"! "<<ans<<endl;
return 0;}
```