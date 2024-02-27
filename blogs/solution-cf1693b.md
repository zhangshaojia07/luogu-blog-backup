# CF1693B Fake Plastic Trees

显然是树形 DP（其实类似贪心）。

设 $f_x$ 表示满足 $x$ 为根的子树都满足条件的所需最少操作数，$g_x$ 表示满足条件后 $x$ 节点的最大可能权值（为何最大：给祖先留值域）。

对于叶子 $x$：$f_x=1,g_x=r_x$。

对于非叶子 $x$，设儿子的 $f$ 和为 $F$，$g$ 和为 $G$：

* $G\ge l_x$，可以不用动，$f_x=F,g_x=\min(r_x,G)$。

* $G< l_x$，必须要在此节点操作，$f_x=F+1,g_x=r_x$。

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
#define N 200010
#define pi pair<int,int>
int n,f[N],g[N]; 
pi a[N];
vector<int> e[N];
void work(){
	cin>>n;
	For(i,1,n) e[i].clear();
	int x,y; 
	For(i,2,n){
		cin>>x;
		e[x].pb(i);
	}
	For(i,1,n) cin>>a[i].fir>>a[i].sec; 
	Rof(i,n,1){
		if(e[i].empty()){
			f[i]=1;
			g[i]=a[i].sec;
		}else{
			x=0;y=0;
			for(int j:e[i]){
				x+=f[j];
				y+=g[j];
			}
			if(y>=a[i].fir){
				f[i]=x;
				g[i]=min(a[i].sec,y);
			}else{
				f[i]=x+1;
				g[i]=a[i].sec;
			}
		}
	}
	cout<<f[1]<<endl;
}
int32_t main(){
	ios::sync_with_stdio(false);
	cin.tie(nullptr);
	int T;cin>>T;
	while(T--)work();
return 0;}
```