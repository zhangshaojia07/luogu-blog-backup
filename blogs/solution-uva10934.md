# UVA10934 装满水的气球 Dropping water balloons

### Analysis

发现时间复杂度不能带 $n$。

关键条件：超过 $63$ 次直接输出 `More than 63 trials needed.`。

也就是提醒我们时间是 $O(km)$ 的，其中 $m$ 为抛球次数 $63$。

DP，$f(k,m)$ 表示有 $k$ 个球，抛球 $m$ 次能**确定球的硬度**的**最大楼层数**。

显然，$f(k,m)$ 时第一次抛球在第 $f(k-1,m-1)+1$ 层，因为这样不仅如果球破了可以有足够的球来解决子问题 $[1,f(k-1,m-1)]$，而且尽可能使更高的楼也能被确定。

这时如果球没破，那么在 $f(k-1,m-1)+1$ 层上还能解决 $f(k,m-1)$ 层（子问题），即：

$$f(k,m)=f(k-1,m-1)+f(k,m-1)+1$$

我们可以 $O(km)$ 求出这个表格 $f$。

每次询问 $k,n$，从小到大枚举 $x$，到达第一个 $\ge n$ 的 $f(k,x)$ 时输出 $x$ 即可。

### Code

```cpp
//Said no more counting dollars. We'll be counting stars.
#pragma GCC optimize(2,3)//For Web Contests
#include<bits/stdc++.h>
using namespace std;
#define For(i,j,k) for(int i=j;i<=k;i++)
#define Rof(i,j,k) for(int i=j;i>=k;i--)
#define int long long
#define N 101
#define M 64

int f[N][M];//n balls check m times the max floor to determine 
signed main(){
	For(i,1,N-1)
		For(j,1,M-1)
			f[i][j]=f[i-1][j-1]+1+f[i][j-1];
	int x,y;
	while(true){
		scanf("%lld%lld",&x,&y);
		if(!x) break;
		bool flag=0;
		For(i,0,M-1){
			if(f[x][i]>=y){
				printf("%lld\n",i);
				flag=1;
				break;
			}
		}
		if(!flag) puts("More than 63 trials needed.");
	} 
return 0;}
```