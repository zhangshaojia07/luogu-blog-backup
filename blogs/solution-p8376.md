# P8376 [APIO2022] 排列

场上 $91.36$ 分选手前来补题。

以下 vi 均指 `std::vector<int>`，IS 指上升子序列。

由于这道题比较难调，这里~~凉~~良心地给出 checker（返回序列上升子序列个数）：

```cpp
long long f[100];
long long check(vi x){
	int len=x.size();
	long long res=1;
	for(int i=0;i<len;i++){
		f[i]=1;
		For(j,0,i-1) if(x[j]<x[i]) f[i]+=f[j];
		res+=f[i];
	}
	return res;
}
```

### Analysis

首先，$\{0,1,2,\dots,(x-1)\}$ 拥有 $2^x$ 个 IS（含空）。

这启示我们将 $k$ 二进制拆分。

设 $k$ 的最高位为 $2^x(x\ge 1)$，则我们先构造（横坐标为序列，纵坐标为值）：

![](https://cdn.luogu.com.cn/upload/image_hosting/gjh0bo25.png)

满足了 $k$ 的最高位，接下来我们 $k:=k-2^x$。

然后我们能插入一些数来满足剩下的二进制位。具体地，假设我们还要增加 $2^c$ 个 IS：

![](https://cdn.luogu.com.cn/upload/image_hosting/k6p0hpjf.png)

由于二进制位之间 IS 个数是单纯「加」的关系，所以下面这一排要单调递减：

![](https://cdn.luogu.com.cn/upload/image_hosting/kjakdp5v.png)

（所以总体看起来像一个小于号 $<$）

此时恭喜你可以获得 $91.36$ 的分数！在场上建议去开别的题了。

* * *

满分做法：

考虑到 $k=10^{18}<2^{60}$，代表着我们最多再用 $30(=90-60)$ 个位置表示出 $2^0,2^1,\dots,2^{58}$，这比刚才每一位都加一个点的限制缩小了一半（$59\to 30$）。

有一个~~螺旋升天的~~想法：**试着将 $k$ 二进制中连续的两个 $1$ 只用一个位置弄出来。**

这个想法是可行的，我们这样：

![](https://cdn.luogu.com.cn/upload/image_hosting/0jigc2rw.png)

也就是从左往右加下面那排的点：

* 若这个点右边紧接着下一个要加的点，并且下排的点至少有了两个。我们将这个点的纵坐标插在次小和次次小之间，横坐标为紧邻的这两个位置中靠右的位置。

* 否则像之前那样单点加。

即可 AC。

~~时间复杂度啥的就不管了吧。~~

### Code

```cpp
#include <vector>
std::vector<int> construct_permutation(long long k);
//We'll be counting stars.
#include<bits/stdc++.h>
using namespace std;
#define pb emplace_back
#define For(i,j,k) for(int i=(j),i##_=(k);i<=i##_;i++)
#define Rof(i,j,k) for(int i=(j),i##_=(k);i>=i##_;i--)
#define ckmx(a,b) a=max(a,b)
#define ckmn(a,b) a=min(a,b)
#define debug cerr<<"Line#"<<__LINE__<<endl
#define vi vector<int>
const int inf=2022;
vi s,res;
int a[100];
bool b[100];
vi construct_permutation(long long k){
	res.clear(); 
	int x=1; while((1ll<<(x+1))<=k) x++;
	k-=(1ll<<x);
	memset(a,-1,sizeof a);
	For(i,0,x-1) b[i]=1ll&(k>>i);
	int mn=-1,se=-1;
	Rof(i,x-1,0) if(b[i]){
		if(i && b[i-1] && mn!=-1 && se!=-1){
			a[mn]--;
			a[i-1]=a[se];
			a[se]--;
			i--;
		}else if(mn!=-1){
			a[i]=a[mn]-1;
			se=mn;
			mn=i;
		}else{
			a[i]=inf-1;
			mn=i;
		}
	}
	For(i,0,x-1){
		if(a[i]!=-1) res.pb(a[i]);
		res.pb(2*inf-i);
	}
	reverse(res.begin(),res.end());
	s=res;
	sort(s.begin(),s.end());
	for(int &i:res) i=lower_bound(s.begin(),s.end(),i)-s.begin();
	return res;
} 
```