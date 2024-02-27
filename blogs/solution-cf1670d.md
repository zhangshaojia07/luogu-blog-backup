# CF1670D Very Suspicious

### Analysis

发现每两条相交的线就会新增两个满足题意的正三角形。

我们转化成对偶命题：**给定 $a$ 条线，求最大的交点数量。**

注意这里的交点数量为两两交点的数量，即如果三线交于一点，则算有 $3$ 个交点。

我们只需要快速算出这个问题的答案，就可以对原问题二分答案求解。

发现六边形网格中有三种斜率，我们设斜率为这三种的直线条数分别为 $x,y,z(x+y+z=a)$。

这样两两交点数量为 $xy+yz+zx$，我们要最大化这个数。

------------

**结论：** $x,y,z$ 尽可能相近可使得 $xy+yz+zx$ 最大。

**证明：**

$$\begin{aligned}
xy+yz+zx&=\frac{1}{2}(2xy+2yz+2zx)
\\
&=\frac{1}{2}\left((x+y+z)^2-x^2-y^2-z^2\right)
\\
&=\frac{1}{2}\left(a^2-x^2-y^2-z^2\right)
\end{aligned}$$

我们让 $x^2+y^2+z^2$ 最小即可。

反证法，若最优的方案存在 $x<y-1$，则重新分配成 $\{x+1,y-1,z\}$ 将是更优解，因为：

$$\begin{aligned}
(x+1)^2+(y-1)^2+z^2&=x^2+2x+1+y^2-2y+1+z^2
\\
&=x^2+y^2+z^2+2(x-y+1)
\\
&<x^2+y^2+z^2
\end{aligned}$$

同理后得到 $\max\{x,y,z\}-\min\{x,y,z\}\le 1$，得证。

------------

由于 $a$ 条线的交点数是 $a^2$ 级别的，所以原问题的答案是 $\sqrt{n}$ 级别的，所以时间复杂度 $O(\log \sqrt{n})$。

### Code

```cpp
//Said no more counting dollars. We'll be counting stars.
#pragma GCC optimize("Ofast")
#include<bits/stdc++.h>
using namespace std;
#define IOS ios::sync_with_stdio(0);cin.tie(0);cout.tie(0)
inline int check(int x){
	int a=x/3+(x%3>0),b=x/3+(x%3>1),c=x/3;
	return 2*(a*b+b*c+c*a);
}
signed main(){IOS;
	int T,n;cin>>T;
	while(T--){
		cin>>n;
		int l=1,r=39000,mid,res;
		while(l<=r){
			mid=(l+r)>>1;
			if(check(mid)>=n) res=mid,r=mid-1;
			else l=mid+1;
		} 
		cout<<res<<endl;
	}
return 0;}
```