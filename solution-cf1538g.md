# CF1538G题解

2021/7/17:规范了格式

2021/7/15:增加了句号与空格

2021/7/9:修改了表达更严谨

2021/6/21:增加了 $LaTeX$

~~良心题解 管理员求过~~

### 题意简述
X 和 Y 两个背包，容量分别为 $x$ 和 $y$，每次操作在背包不溢出的条件下二选一：

* op1： 将体积为 $a$ 的物体放入 X，将体积为 $b$ 的物体放入 Y

* op2： 将体积为 $b$ 的物体放入 X，将体积为 $a$ 的物体放入 Y

求操作数的最大值（设为 $ans$ ）。

### 题目分析

**开始的开始先膜拜题解中 $O(1)$ 的大佬 SharpnessV。**

~~但其实没有必要跑这么快,这篇题解写给像我这样不太会或没时间数学推理的蒟蒻。~~

设 $f(x)$ 为 op1 恰有 $x$ 次时最大操作数。

有一个非常有用的性质：

**$f(x)$ 一定是上凸函数。**

感性理解一下，（不妨设 $a\leq b$ ）若全是 op1 ，则 X 还有大量空间没有利用，若全是 op2 ，则 Y 还有大量空间没有利用，在 op1 数量在增加的过程中，$ans$ 先增加，之后达到峰值（X 和 Y 利用率均衡），最后 $ans$ 下降。

样例 testcase1（ $f(x)=-1$ 表示无解）:

![](https://cdn.luogu.com.cn/upload/image_hosting/y319iihj.png)

求单峰函数的最值怎么办？

爬山（套用模拟退火代码）！

没学过模拟退火的[戳这里](https://www.cnblogs.com/rvalue/p/8678318.html)看奆佬 rvalue 博客。

$\mathcal{O}(K\log T)$ 时间复杂度($K$ 为常数,$T$ 为初始温度)。

### Code
```cpp
#include<bits/stdc++.h>
#define down 0.96//下降速率
using namespace std;
typedef long long ll;
void setio(string);

int n,m,a,b;
double ans;
double check(double x){
	if(x<0)return -1;
	if(n-a*x<0 || m-b*x<0)return -1;
	double y=min((n-a*x)/b,(m-b*x)/a);
	return x+y;
}
ll check2(ll x){
	if(x<0)return -1;
	if(n-a*x<0 || m-b*x<0)return -1;
	ll y=min((n-a*x)/b,(m-b*x)/a);
	return x+y;
}
void SA() { //模拟退火
	double t=1000000;//初始温度
	while (t>1e-15) { //略大于0
		double aa=ans+(rand()*2-RAND_MAX)*t;
		double pan=check(aa);
		double de=pan-check(ans);
		if(de>0) { //如果此答案更优,就接受
			ans=aa;
		}
		t*=down;
	}
}
int main(){
	int T;
	cin>>T;
	while(T--){
		cin>>n>>m>>a>>b;
		ans=0;
		SA();
		SA();
		SA();
		SA();
		cout<<max(check2(ans),check2(ans+1))<<endl;
	}
	return 0;
}

```
