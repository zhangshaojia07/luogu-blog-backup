# CF1713E Cross Swapping（带权并查集）

首先发现每种操作之间的顺序无所谓，而且一种操作操作两次相当于没操作。

我们称 $\forall k\in[1,n],\text{swap}(a_{i,k},a_{k,i})$ 的操作为 $i$-操作。

所以我们只需要找到一个 $0/1$ 串 $b_1,b_2,\dots,b_n$ 使得 $b_i=1$ 的那些 $i$-操作后的 matrix 的字典序最小。

进一步发现在最终态里 $a_{i,j},a_{j,i}$ 交换当且仅当 $b_i\ne b_j$（即 $b_i\text{ xor } b_j$），所以我们 $i$ 从小到大，$j$ 从小到大（按照题目规定的字典序）枚举元素 $a_{i,j}$：

* 若与 $a_{j,i}$ 交换后严格更优，则我们更希望 $b_i\ne b_j$

* 若与 $a_{j,i}$ 交换后严格更劣，则我们更希望 $b_i=b_j$。

* 若 $a_{i,j}=a_{j,i}$，则无所谓。

我们按照字典序优先级满足要求。

具体地，我们给 $b_1,\dots,b_n$ 共 $n$ 个点带权并查集（边权表示两端的 $\text{xor}$，所以边权 $\in\{0,1\}$），每来一个要求则看要求的两方是否在同一个连通块里，若不在则去满足这要求。

最后暴力操作后输出即可。

时间复杂度 $O(n^2\alpha(n))$。

```cpp
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
#define ll long long
const ll mod=1;
inline ll pw(ll x,ll y){ll r=1;while(y){if(y&1)r=r*x%mod;x=x*x%mod;y>>=1;}return r;}
#define int ll
#define N 1001
int n,a[N][N],f[N];
bool g[N];
inline int gf(int x){
	if(x==f[x]) return x;
	int res=gf(f[x]);
	g[x]^=g[f[x]];
	return f[x]=res;
}
void turn(int x){
	For(i,1,n) swap(a[i][x],a[x][i]);
}
void merge(int x,int y,bool val){
	int xx=gf(x),yy=gf(y);
	if(xx==yy) return ;
	val^=g[x]^g[y];
	f[xx]=yy;
	g[xx]=val;
}
void work(){
	cin>>n;
	For(i,1,n) For(j,1,n) cin>>a[i][j];
	iota(f+1,f+1+n,1);
	fill(g+1,g+1+n,false);
	For(i,1,n) For(j,i,n){
		if(a[i][j]>a[j][i]){//swap
			merge(i,j,1);
		}else if(a[i][j]<a[j][i]){//not swap
			merge(i,j,0);
		}
	}
	For(i,1,n) gf(i);
	For(i,1,n) if(g[i]) turn(i);
	For(i,1,n){
		For(j,1,n){
			cout<<a[i][j]<<" ";
		}
		cout<<"\n";
	}
}
signed main(){ios::sync_with_stdio(false),cin.tie(nullptr);
	int T;cin>>T;
	while(T--)work();
return 0;}
```