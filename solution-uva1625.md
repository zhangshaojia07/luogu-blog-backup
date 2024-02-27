# UVA1625 颜色的长度 Color Length

### Preface

喜提代码和思路最短！（目前）

### Analysis

显然与 LCS（Longest Common Subsequence）的做法类似。

轻松写出转移：

$$f(i,j)=\min
\begin{cases}
f(i-1,j)+g(i-1,j)
\\
f(i,j-1)+g(i,j-1)
\end{cases}$$

其中 $f(i,j)$ 代表将 $A,B$ 两个颜色序列分别取前缀 $i,j$ 位缝合成的 $C$ 颜色序列的最小代价。

其中代价可以这样理解：对于最终的缝合序列，在同种颜色的左右极端之间连一条直绳（最终输出的就是这些绳长之和），$(i,j)$ 的代价即为在这里劈开缝合序列后左边的绳长度和。

$g(i,j)$ 表示 在这个前缀 $C$ 中 且 在剩余的序列中 的颜色数，即现在的位置 $(i,j)$ 横截面有 $g(i,j)$ 条绳子。

$g(i,j)$ 可以通过二进制状压字符集求得。

时间 $O(nm)$，常数较小。

### Code

[Submission](https://www.luogu.com.cn/record/68033767)

```cpp
//Said no more counting dollars. We'll be counting stars.
#pragma GCC optimize(2,3)//For Web Contests
#include<bits/stdc++.h>
using namespace std;
#define IOS ios::sync_with_stdio(0);cin.tie(0);cout.tie(0)
#define fir first
#define sec second
#define For(i,j,k) for(int i=j;i<=k;i++)
#define Rof(i,j,k) for(int i=j;i>=k;i--)
#define ckmn(a,b) a=min(a,b)
#define N 5050
string a,b;
int n,m,f[N][N],g[N][N];
pair<int,int> aa[N],bb[N];
void work(){
	cin>>a>>b;
	n=a.size();a="#"+a;
	m=b.size();b="#"+b;
	aa[0]=aa[n+1]=bb[0]=bb[m+1]=make_pair(0,0);
	For(i,1,n) aa[i].fir=aa[i-1].fir|(1<<(a[i]-'A'));
	Rof(i,n,1) aa[i].sec=aa[i+1].sec|(1<<(a[i]-'A'));
	For(i,1,m) bb[i].fir=bb[i-1].fir|(1<<(b[i]-'A'));
	Rof(i,m,1) bb[i].sec=bb[i+1].sec|(1<<(b[i]-'A'));
	For(i,0,n) For(j,0,m) g[i][j]=__builtin_popcount((aa[i].fir|bb[j].fir)&(aa[i+1].sec|bb[j+1].sec));
	For(i,0,n) For(j,0,m){
		f[i][j]=(i+j==0?0:0x3f3f3f3f);
		if(j>0) ckmn(f[i][j],f[i][j-1]+g[i][j-1]);
		if(i>0) ckmn(f[i][j],f[i-1][j]+g[i-1][j]);
	}
	cout<<f[n][m]<<endl;
}
signed main(){IOS;int T;cin>>T;while(T--)work();return 0;}
```