# CF1290A题解

2021/7/17:增加了句号与空格

2021/7/9:增加了 $LaTeX$

~~良心题解 管理员求过~~

### 题意简述
一串数共 $n$ 个,有 $n$ 次操作,每次去掉数列的第一个或最后一个。现在你要操作的是第 $m$ 次，并且你可以控制任意 $k$ 次操作的选择。问你在最优选择下所能取到的最小的数的最大值是多少？
### 题目分析
首先，有用的控制一定控制"我"前面的人。

所以不妨将 $k$ 改为 $\min(k,m-1)$ 。

前 $m-1$ 个人先取,共 $m$ 种情况:

**case 1:** $0$ 人取前端, $m-1$ 人取后端，此时你取 $\max(a_1,a_{n-m+1})$ ；

**case 2:** $1$ 人取前端, $m-2$ 人取后端，此时你取 $\max(a_2,a_{n-m+2})$ ；

**case 3:** $2$人取前端, $m-3$ 人取后端，此时你取 $\max(a_3,a_{n-m+3})$ ；

$...$

**case m:** $m-1$ 人取前端, $0$ 人取后端，此时你取 $\max(a_m,a_n)$ 。

构建一个数组 `b[m]` ：

`b[i]=max(a[i],a[n-m+i])`

所以 $k=0$ 时(即不能控制),最优选择下所能取到的最小的数的最大值 $ans$ 是 $\min(b_1,b_2,b_3,...,b_m)$ 。

**若 $k>0$ 呢?**

为了使 $ans$ 最大,你会让 $k$ 个在你前面的人指定取前面/后面。

于是部分 **case** 取不到了。

哪些?

若你派 $t$ 人取前面, $(k-t)$ 人取后面，

则取前面至少有 $t$ 人,取后面至少有 $(k-t)$ 人。

于是 **case** $1$ ~ $t$ 和 **case** $(m-k+t+1)$ ~ $m$ 均不可取，

即对应的 $b$ 数组中元素不可取。

$ans$ 从$k=0$时的 $\min(b_1,b_2,b_3,...,b_m)$ 变成了 $\min(b_{t+1},b_2,b_3,...,b_{m-k+t})$ 。

**接下来只要求出使 $ans$ 最大的 $t$ 了**

~~此题数据水~~ $\mathcal{O}(Tn^2)$ 暴力即可，

也可以 $\mathcal{O}(Tn)$ 单调队列优化。

### Code:
```cpp
//https://www.luogu.com.cn/problem/CF1290A 
#include<bits/stdc++.h>
#define inf 0x7fffffff
#define fo(x,y,z) for(int x=y;x<=z;x++)
#define N 4000
using namespace std;
typedef long long ll;
void setio(string);

int T,n,m,k,a[N],b[N],minn,ans; 
int main(){
	setio("");
	cin>>T;
	while(T--){
		cin>>n>>m>>k;
		k=min(k,m-1);
		fo(i,1,n)cin>>a[i];
		fo(i,1,m)b[i]=max(a[i],a[i+n-m]);
		ans=0;
		fo(i,1,k+1)//这里可以用单调队列，但n^2能过就行了 （landuo） 
		{
			minn=inf;
			fo(j,i,i+m-k-1){
				minn=min(minn,b[j]);
			}
			ans=max(minn,ans);
		}
		cout<<ans<<endl;
	}
	return 0;
}
void setio(string name){
	ios_base::sync_with_stdio(0);
	cin.tie(0);
    if(name!=""){
        freopen((name+".in").c_str(),"r",stdin);
        freopen((name+".out").c_str(),"w",stdout);
    }
}

```
