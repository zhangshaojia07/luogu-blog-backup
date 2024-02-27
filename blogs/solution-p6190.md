# P6190 [NOI Online #1 入门组] 魔法

### Preface

「我**只用矩阵快速幂**过了哎！」

「赶快写题解去吧。」

### Analysis

提供一种更简短的**单源最短路**思路。

先说结论：

$$Ans=G_{n,1}\quad G=E^n(FE^n)^k$$

设 $d_i$ 表示当前节点 $1$ 到 $i$ 的最短路，初始

$$
\begin{bmatrix}d_1\\d_2\\\vdots\\d_n\end{bmatrix}=
\begin{bmatrix}0\\+\infty\\\vdots\\+\infty\end{bmatrix}
$$

于是我们类似 Bellman-Ford 算法做**松弛操作**，设松弛后的最短路为 $d_i'$，则

$$
E\cdot
\begin{bmatrix}d_1\\d_2\\\vdots\\d_n\end{bmatrix}=
\begin{bmatrix}d_1'\\d_2'\\\vdots\\d_n'\end{bmatrix}
$$

其中 $E=\begin{bmatrix}
e_{1,1}&e_{1,2}&\cdots&e_{1,n}
\\e_{2,1}&e_{2,2}&\cdots&e_{2,n}
\\\vdots&\vdots&\ddots&\vdots
\\e_{n,1}&e_{n,2}&\cdots&e_{n,n}
\end{bmatrix}$，若 $i\to j$ 有边则 $e_{i,j}$ 为该边边权，否则为 $e_{i,j}=(+\infty)[i\ne j]$。

$[A]$ 表示若条件 $A$ 为真则值为 $1$，否则为 $0$。

**注意这里的矩阵乘法 $AB=C$ 定义为 $C_{i,j}=\min\limits_{k=1}^nA_{i,k}+B_{k,j}$（与其他题解相同），易证其结合律。**

所以

$$
E^n\cdot\begin{bmatrix}d_1\\d_2\\\vdots\\d_n\end{bmatrix}
$$

相当于做了一次 Bellman-Ford 单源最短路。由于没有负权边，此时得到的 $d_i'$ 即为 $1\to i$ 的最短路。

然后有**魔法操作**，同样可以写成矩阵的形式

$$F=\begin{bmatrix}
f_{1,1}&f_{1,2}&\cdots&f_{1,n}
\\f_{2,1}&f_{2,2}&\cdots&f_{2,n}
\\\vdots&\vdots&\ddots&\vdots
\\f_{n,1}&f_{n,2}&\cdots&f_{n,n}
\end{bmatrix}$$

$$f_{i,j}=\begin{cases}+\infty&e_{i,j}=+\infty\\-e_{i,j}&e_{i,j}\ne+\infty\end{cases}$$

在 $k$ 次**魔法操作后都要松弛**，所以最终节点 $1$ 到每个节点的最短路

$$\begin{bmatrix}d_1'\\d_2'\\\vdots\\d_n'\end{bmatrix}=G\cdot
\begin{bmatrix}0\\+\infty\\\vdots\\+\infty\end{bmatrix}
$$

$$Ans=d_n'=\min_{i=1}^n\left(G_{n,i}+(+\infty)[i>1]\right)=G_{n,1}$$

使用矩阵快速幂，时间 $O(n^3\log k)$。

### Code

```cpp
//Said no more counting dollars. We'll be counting stars.
#include<bits/stdc++.h>
using namespace std;
#define IOS ios::sync_with_stdio(0);cin.tie(0);cout.tie(0)
#define For(i,j,k) for(int i=j;i<=k;i++)
#define ckmn(a,b) a=min(a,b)
#define int long long
#define N 100

const int inf=1e17;
int n,m,k;
struct matrix{
	int m[N][N];
	void clear(){For(i,0,n-1) For(j,0,n-1) m[i][j]=inf;}//零矩阵 
	void init(){For(i,0,n-1) For(j,0,n-1) m[i][j]=(i==j?0:inf);}//单位阵 
	matrix mul(matrix x){//矩阵乘法 
		matrix res;res.clear();
		For(i,0,n-1) For(j,0,n-1) For(k,0,n-1) ckmn(res.m[i][j],m[i][k]+x.m[k][j]);
		return res;
	}
}rel,mag,ans;//rel:松弛矩阵 mag:魔法+松弛矩阵 
matrix kasumi(matrix x,int y){//快速幂 
	matrix res;res.init();
	while(y){
		if(y&1) res=res.mul(x);
		y>>=1;
		x=x.mul(x);
	}
	return res;
}
signed main(){IOS;
	cin>>n>>m>>k;
	rel.init();
	mag.init();
	int x,y,z;
	For(i,1,m){
		cin>>x>>y>>z;
		x--;y--;//矩阵中从 0 开始存 
		ckmn(rel.m[y][x],z);
		ckmn(mag.m[y][x],-z);
	}
	rel=kasumi(rel,n);
	mag=mag.mul(rel);
	mag=kasumi(mag,k);
	ans=rel.mul(mag);
	cout<<ans.m[n-1][0]<<endl;
return 0;}
```