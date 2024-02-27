# P7962 [NOIP2021] 方差 题解

### Analysis

方差的 $n^2$ 倍就是

$$n\times sos-sum^2$$

其中 $sos$ 是平方和。

所以只要开 `long long` 即可，无精度问题。

我们先看看能改变啥，操作为将 $a_i$ 变成 $a_{i-1}+a_{i+1}-a_i$，画一画，发现只是将两个相邻的差分 swap 而已。

就是说题目问你，**如何将差分序列排序，使得原序列的方差最小**。

当你一头雾水时，就有 NB 的性质出现：

**使得方差最小化的差分序列一定是先单调不增，后单调不减的。**

理解一下：

![](https://cdn.luogu.com.cn/upload/image_hosting/9mc9bgqi.png)

方差不会随着整个序列整体加减而改变，所以先固定一个数为 $0$，再将差分序列排序（从小到大）后顺序插入当前的前面或后面。

然而暴搜放前面还是后面是会挂的，所以想到 DP。

状态 $f_{i,j}$ 表示当前加入了 $i$ 个差分使得原序列（共 $i+1$ 项）的和为 $j$，原序列平方和的最小值。（默认第一项为 $0$）

转移（$ckmn(a,b)$ 为 $a:=\min(a,b)$）：

$$\begin{aligned}
1&:\quad ckmn(f_{i,j+b_i},f_{i-1,j}+b_i^2)
\\
2&:\quad ckmn(f_{i,j+ia_i},f_{i-1,j}+ia_i^2+2ja_i)
\end{aligned}$$

$$$$

其中 $\{a\}$ 为排序后的差分序列，$b_k=\sum\limits_{i=1}^ka_i$。

![](https://cdn.luogu.com.cn/upload/image_hosting/s2sj2w8f.png)

随后就可以用开头的公式计算方差的最小值了。

### Detail

发现这样空间和时间都是 $O(n^2V)$ 的，~~双喜临门~~。

优化：

可以用滚动数组优化掉空间第一维度。

可以将 $\{a\}$ 中前几项为 $0$ 的先 $O(1)$ 计算，发现 $\{a\}$ 中这样只剩 $O(V)$ 个元素了（即原序列中只有 $O(V)$ 个不同的元素）。

这样时间就是 $O(nV^2)$，可以通过。

### Code

```cpp
#include<bits/stdc++.h>
using namespace std;
#define IOS ios::sync_with_stdio(0);cin.tie(0);cout.tie(0)
#define ckmn(a,b) a=min(a,b)
#define linf 0x3f3f3f3f3f3f3f3f
#define N 10010
#define M 6600000
#define ll long long
#define For(i,j,k) for(register ll i=j;i<=k;i++)
ll n,a[N],b[N],lim;
ll f[M],g[M]; 
signed main(){
	IOS;
	cin>>n;
	For(i,1,n) cin>>a[i]; 
	lim=n*a[n];
	For(i,1,n-1) a[i]=a[i+1]-a[i];
	sort(a+1,a+n);
	For(i,1,n-1) b[i]=a[i]+b[i-1];
	
	For(i,0,lim) f[i]=linf;
	ll pos=1;
	while(a[pos]==0) pos++;
	f[a[pos]]=a[pos]*a[pos]; 
	f[pos*a[pos]]=pos*a[pos]*a[pos];
	For(i,pos+1,n-1){
		For(j,0,lim) g[j]=linf;
		For(j,0,lim){
			if(f[j]==linf) continue;
			ckmn(g[j+b[i]],f[j]+b[i]*b[i]);
			ckmn(g[j+i*a[i]],f[j]+a[i]*a[i]*i+2*a[i]*j);
		}
		For(j,0,lim) f[j]=g[j];
	}
	ll ans=linf;
	For(i,0,lim) if(f[i]<linf) ckmn(ans,n*f[i]-i*i);
	cout<<ans<<endl;
	return 0;
}
```