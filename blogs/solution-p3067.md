# P3067 [USACO12OPEN]Balanced Cow Subsets G

### Preface

本题解采用折半搜索（meet in the middle）。

本人为折半搜索的~~出血~~初学者，请多关照。

本人认为折半搜索和双向 BFS 十分类似。

### Analysis

设

$$\begin{aligned}
k&=\left\lfloor\dfrac{n}{2}\right\rfloor
\\
A&=\{M_1,M_2,\dots,M_k\}
\\
B&=\{M_{k+1},M_{k+2},\dots,M_n\}
\end{aligned}$$

我们用 $O(3^\frac{n}{2})$ 分别求出 $A,B$ 的子序列带符号可能值。

具体地，$\{3,7,5,2\}$ 的一种可能值为 $+3-7-2=-6$，此时选择入子序列的状压为 $(1101)_2$。

将 $A,B$ 得到的结果分别排序，值互为相反数的两个可以有 $1$ 的贡献，但前提是这种二进制状压（$\{M\}$ 中这些数被选中）没有出现过。

由于值互为相反数的都要计算，那当两半的值分别都相同时，时间不还是 $O(3^n)$ 的 TLE 吗？

不……

题目中 $M_i$ 的下限为 $1$ 可帮了大忙，让两半的值分别都相同变成了幻想。

计算 $A=B=\begin{matrix}\{\underbrace{1,1,\dots,1}\}\\10\ times\end{matrix}$（最坏情况），发现两者每种的值的分布：

```
值	 数量
-10     : 1
-9      : 10
-8      : 55
-7      : 210
-6      : 615
-5      : 1452
-4      : 2850
-3      : 4740
-2      : 6765
-1      : 8350
0       : 8953
1       : 8350
2       : 6765
3       : 4740
4       : 2850
5       : 1452
6       : 615
7       : 210
8       : 55
9       : 10
10      : 1
数量的平方和（计算次数）：3.77*10^8
```

可见还是能过的（只要常数小）。

### Detail

最后记得减去空集，即 $ans-=1$。

### Code

```cpp
#include<bits/stdc++.h>
using namespace std;
#define pb emplace_back
#define mkp make_pair
#define fir first
#define sec second
#define For(i,j,k) for(int i=j;i<=k;i++)
#define gc getchar
inline int read(){
	int x=0;char c=gc();bool f=0;
	while(!isdigit(c)){if(c=='-')f=1;c=gc();}
	while(isdigit(c)){x=(x<<3)+(x<<1)+c-48;c=gc();}
	if(f)x=-x;return x;
}

int n,m;
bool vis[(1<<20)+10];
vector<int> a[2];
vector<pair<int,int> > b[2];//值，状压（b[1] 的值存的是相反数）
void dfs(int T,int x,int val,int er){
	if(x==a[T].size()){
		b[T].pb(mkp(val,er));
		return ;
	}
	dfs(T,x+1,val,er);
	dfs(T,x+1,val+a[T][x],er|(1<<x));
	dfs(T,x+1,val-a[T][x],er|(1<<x));
}
signed main(){
	n=read();
	m=n>>1;
	n-=m;
	For(i,1,n) a[0].pb(read());
	For(i,1,m) a[1].pb(read());
	dfs(0,0,0,0);
	dfs(1,0,0,0);
	sort(b[0].begin(),b[0].end());
	sort(b[1].begin(),b[1].end());
	vector<pair<int,int> >::iterator pos=b[1].begin(),tmp;
	int ans=0;
	for(auto i:b[0]){
		while(pos!=b[1].end() && pos->fir<i.fir) pos++;
		tmp=pos;
		while(tmp!=b[1].end() && tmp->fir==i.fir){
			m=i.sec|(tmp->sec<<n);
			if(!vis[m]){
				vis[m]=1;
				ans++;
			}
			tmp++;
		}
	}
	printf("%d\n",ans-1);
	return 0;
}
```