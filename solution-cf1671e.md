# CF1671E Preorder

### Analysis

普通的先序遍历：

1. 存入本节点。

2. 先序遍历左儿子。

3. 先序遍历右儿子。

由于本题中任意次 swap 左右儿子的操作等价于先序遍历时变为：

1. 存入本节点。

2. 任意选择左右儿子中的一个遍历

3. 选择另一个儿子遍历

我们命名这样的遍历为「新遍历」。

我们设 $b_i$ 表示 $i$ 节点子树能有几种不同的「新遍历」序列。显然对于叶子 $x$，$b_x=1$。

我们考虑如何将儿子的 $b$ 推到父亲的 $b$。

------------

**引理：** 如果两棵子树所有可能的「新遍历」序列中字典序最小的不同，则这两个子树的所有可能的「新遍历」序列均不相同。

**证明：** 反证法。假设有一对子树 $x,y$ 不满足引理。

我们可以知道 $x,y$ 取到相同的「新遍历」序列时每个节点先遍历左儿子还是右儿子。我们把 $x,y$ 某些子树交换左右儿子使得每个节点均先遍历左儿子。我们发现此时 $x,y$ 两棵树是一模一样的，这样所有可能的「新遍历」序列中字典序最小的一定相同。矛盾，原命题成立。

------------

所以，我们从下至上处理这棵树。每处理完一个节点就把这个子树调成「新遍历」序列字典序最小的样子。

当现在到了节点 $x$，两个儿子分别是 $ls,rs$，那我们可以在 $size(ls)+size(rs)$ 的时间里判断两个树是否相等，或是判断「新遍历」序列字典序大小。

* 若两个子树相同，则 $b_x=b_{ls}\times b_{rs}$。

* 否则，$b_x=2\times b_{ls}\times b_{rs}$。

然后将两个子树调成「新遍历」序列字典序最小的样子。

时间复杂度 $O(n2^n)$。

### Code

[Link](https://codeforces.com/contest/1671/submission/154728988)

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
const ll mod=998244353;
inline ll pw(ll x,ll y){ll r=1;while(y){if(y&1)r=r*x%mod;x=x*x%mod;y>>=1;}return r;}
inline void mad(ll &a,ll b){a=(a+b)%mod;while(a<0)a+=mod;}
inline void mmu(ll &a,ll b){a=a*b%mod;while(a<0)a+=mod;}
#define inv(a) pw(a,mod-2)
#define int long long
#define N (1<<18)+10
#define ls (rt<<1)
#define rs (rt<<1|1) 
int m,n;
bool a[N];
char ch;
int b[N];
int cmp(int x,int y){
	if(a[x]>a[y]) return 1;
	if(a[x]<a[y]) return -1;
	if((x<<1)>n) return 0;
	int res=cmp(x<<1,y<<1);
	if(res!=0) return res;
	else return cmp(x<<1|1,y<<1|1);
}
void sw(int x,int y){
	swap(a[x],a[y]);
	if((x<<1)>n) return ;
	sw(x<<1,y<<1);
	sw(x<<1|1,y<<1|1);
}
inline int dfs(int rt){
	if(ls>n) return 1;
	int r1=dfs(ls);
	int r2=dfs(rs);
	int res=cmp(ls,rs);
	if(res==1) sw(ls,rs);
	if(res!=0) return r1*r2*2%mod;
	else return r1*r2%mod;
}
signed main(){IOS;
	cin>>m;
	n=(1<<m)-1;
	For(i,1,n){
		cin>>ch;
		a[i]=(ch=='B');
	}
	cout<<dfs(1)<<endl;
return 0;}
```