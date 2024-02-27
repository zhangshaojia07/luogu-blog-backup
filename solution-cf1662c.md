# CF1662C European Trip（矩阵乘法套矩阵快速幂）

### Preface

本来想着 naive 的矩阵乘法，结果还要来一层……

### Analysis

没错，这道题是**矩阵乘法套矩阵快速幂**。

简化版：如果不是「special trip」而是「trip」，想想咋做。

这是一个经典的模型。[P6190 [NOI Online #1 入门组] 魔法](https://www.luogu.com.cn/problem/P6190) 做完后就懂了。因为本文主要讲 trip $\to$ special trip，所以这里不再赘述。

由于某道古老的题是边转点做的，而复杂度是 $O(m^3\log m)$ 的，所以我考场上就蒙了。

其实正解还是正常的节点矩阵乘法。

设邻接矩阵为 $G$，$G_{i,j}=1\iff i,j$ 之间有边。

若我们得到了从 $i$ 开始、$j$ 结束的长度为 $k$ 的 special trip 数量为 $S^{(k)}_{i,j}$，则最终答案为 $\sum_{i=1}^{n}S^{(k)}_{i,i}$。

显然 $S^{(1)}=G$。

那 $S^{(2)}$ 呢？若是 $G^2$，多算了两条边相同的情况，所以减掉即可。设矩阵 $D$，其中 $D_{i,i}$ 为 $i$ 节点的度，其他为 $0$，则

$$
S^{(2)}=G^2-D
$$

若 $k\ge 3$，我们分类计数：

* 先不管最后两条边是否相等，数量为 $G\cdot S^{(k-1)}$。

* 再减去最后两条边相等，其他连着的边不同的方案数，为 $(D-I)\cdot S^{(k-2)}$

对于第二类详细说明一下原因：。

所以有

$$
S^{(k)}=
\begin{cases}
G & k=1
\\
G^2-D & k=2
\\
G\cdot S^{(k-1)}-(D-I)\cdot S^{(k-2)} &k>2
\end{cases}
$$

然后就是[P1349 广义斐波那契数列](https://www.luogu.com.cn/problem/P1349)了，只不过元素都是矩阵。

所以是矩阵套矩阵啊……qwq

时间 $O(n^3\log n)$。

### Code

实现中点下标从 $0$ 开始。

本代码用了 struct 套 struct，显然慢，建议直接将小矩阵写进大矩阵。

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
const long long mod=998244353;
#define N 100
int n,m,k;
struct ma{
	int a[N][N];
	void init(){For(i,0,n-1)For(j,0,n-1)a[i][j]=(i==j);}
	void clear(){For(i,0,n-1)For(j,0,n-1)a[i][j]=0;};
	ma mul(ma x){
		ma res;res.clear();
		For(i,0,n-1)For(j,0,n-1)For(k,0,n-1)res.a[i][j]=(res.a[i][j]+1ll*a[i][k]*x.a[k][j])%mod;
		return res;
	}
	ma del(ma x){
		ma res;
		For(i,0,n-1)For(j,0,n-1)res.a[i][j]=(a[i][j]-x.a[i][j]+mod)%mod;
		return res;
	}
	ma add(ma x){
		ma res;
		For(i,0,n-1)For(j,0,n-1)res.a[i][j]=(a[i][j]+x.a[i][j])%mod;
		return res;
	}
}g,d,I,O,s1,s2;
struct Ma{
	ma a[2][2];
	void clear(){For(i,0,1)For(j,0,1)a[i][j]=O;}
	void init(){For(i,0,1)For(j,0,1)a[i][j]=(i==j?I:O);}
	Ma mul(Ma x){
		Ma res;res.clear();
		For(i,0,1)For(j,0,1)For(k,0,1)res.a[i][j]=res.a[i][j].add(a[i][k].mul(x.a[k][j]));
		return res;
	}
}a,b,c;
void output(ma x){
	int ans=0;
	For(i,0,n-1) ans=(ans+x.a[i][i])%mod;
	cout<<ans<<endl;
}
signed main(){IOS;
	cin>>n>>m>>k;
	g.clear();
	d.clear();
	I.init();
	O.clear();
	int x,y;
	For(i,1,m){
		cin>>x>>y;
		x--;y--;
		g.a[x][y]++;
		g.a[y][x]++;
		d.a[x][x]++;
		d.a[y][y]++;
	}
	s1=g;
	s2=(g.mul(g)).del(d);
	if(k==1) output(s1);
	else if(k==2) output(s2);
	else{
		k-=2;
		a.a[0][0]=g;a.a[0][1]=I.del(d);
		a.a[1][0]=I;a.a[1][1]=O;
		b.init();
		while(k){
			if(k&1) b=b.mul(a);
			k>>=1;
			a=a.mul(a);
		}
		output(b.a[0][0].mul(s2).add(b.a[0][1].mul(s1)));
	} 
return 0;}
```