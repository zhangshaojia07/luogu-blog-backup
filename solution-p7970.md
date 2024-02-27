# P7970 [KSN2021] Binary Sea（二进制与谢尔宾斯基三角）

首先发现 $n,m$ 是没有用的，可以假想平面是无限大的。

然后有一个结论：

对于任意黑格 $(x,y)[x,y>0]$，都有 $(x-1,y)$ 与 $(x,y-1)$ **两者其一**是黑格。

**证明 1：**

![](https://cdn.luogu.com.cn/upload/image_hosting/rsevgvwz.png)

（黑格组成了谢尔宾斯基三角状物）

**证明 2：**

由对称性，且 $x\ \text{and}\ y=0$，不妨设 $\text{lowbit}(x)<\text{lowbit}(y)$。

由于 $x-1=x-\text{lowbit}(x)+(\text{lowbit}(x)-1)$，就是将二进制中末尾的 $100\dots0$ 变为 $011\dots1$。

所以 $(x-1)\ \text{and}\ y=0$，对偶地 $x\ \text{and}\ (y-1)\ne 0$，得证。

* * *

所以黑格形成了一棵向右下的树啊！

所以对于一个矩形，其内的连通块数（森林的树个数）等于矩形左侧和上侧的边数。

![](https://cdn.luogu.com.cn/upload/image_hosting/sztv6p7f.png)

（就是上图的蓝圈数量）

形式化地，我们不妨只考虑上侧的边，个数为（矩形为 $(x,y,xx,yy)$）：
$$\begin{aligned}
&\sum_{i=y}^{yy}[i\ \text{and}\ x=0][i\ \text{and}\ (x-1)=0]
\\
=&\sum_{i=y}^{yy}[i\ \text{and}\ (x\ \text{or}\ (x-1))=0]
\\
=&\sum_{i=0}^{yy}[i\ \text{and}\ (x\ \text{or}\ (x-1))=0]
-\sum_{i=0}^{y-1}[i\ \text{and}\ (x\ \text{or}\ (x-1))=0]
\end{aligned}
$$

然后 $O(\log)$ DP 就做完了。

```cpp
/*
* Author: ShaoJia
* Last Modified time: 2022-09-19 11:14:26
* Motto: We'll be counting stars.
*/
#include<bits/stdc++.h>
using namespace std;
#define For(i,j,k) for(int i=(j);i<=(k);i++)
int f[31][2];//[0,i-1] did, 0 didnt exceed y, 1 exceed y
int work(int x,int y){
	if(y<0 || x==0) return 0;
	x|=x-1;
	bool X,Y;
	int z;
	For(i,1,30){
		f[i][0]=f[i][1]=0;
		X=x>>(i-1)&1;
		Y=y>>(i-1)&1;
		z=f[i-1][0]+f[i-1][1];
		if(!X){
			if(Y){
				f[i][0]+=f[i-1][0];
				f[i][1]+=f[i-1][1];
			}else{
				f[i][1]+=z;
			}
		}
		if(Y){
			f[i][0]+=z;
		}else{
			f[i][0]+=f[i-1][0];
			f[i][1]+=f[i-1][1];
		}
	}
	return f[30][0];
}
signed main(){ios::sync_with_stdio(false),cin.tie(nullptr);
	int T,x,y,xx,yy;
	cin>>T>>T>>T;
	f[0][0]=1;
	while(T--){
		cin>>x>>y>>xx>>yy;
		if(!x && !y) cout<<"1\n";
		else cout<<(work(x,yy)-work(x,y-1))+(work(y,xx)-work(y,x-1))<<"\n";
	}
return 0;}
```