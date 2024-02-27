# P8190 [USACO22FEB] Cow Camp G

### Preface

Naive 的我想了一个笨拙的算法，甚至时间可能都不是最优，但是谁能拒绝**矩阵乘法**呢。（坏笑）

同时纪念第一道赛场上做出来的 Gold。

### Analysis

发现单次提交所得分数为[二项分布](https://baike.baidu.com/item/%E4%BA%8C%E9%A1%B9%E5%88%86%E5%B8%83/1442377)，我们 $O(T^2)$ 类似杨辉三角预处理出**单次分数为 $i$ 的概率 $p_i$**。

**「好耶，是预处理！再加一个前缀和就更好了。」**

然后就是柿子了，$f_i$ 表示提交限制次数为 $i$ 时最优策略下的期望得分：

$$\begin{cases}
f_1=\frac{T+1}{2}
\\
f_n=f_{n-1}\sum\limits_{i=1}^{\left\lfloor f_{n-1}\right\rfloor}p_i\ +\sum\limits_{i=\left\lfloor f_{n-1}\right\rfloor+1}^{T}i\cdot p_i
\end{cases}$$

**「我好像不明白……」**

**解读：** 如果第 $i$ 次的得分**不比**后 $K-i$ 次的期望得分**小**，那就停下；否则继续提交。

发现这样可以 $O(K)$ 递推了，但是 $K=10^9$ 过不了。

**「我知道！用矩阵快速幂就行！」**

$$\begin{bmatrix}\sum\limits_{i=1}^{\left\lfloor f_{n-1}\right\rfloor}p_i & \sum\limits_{i=\left\lfloor f_{n-1}\right\rfloor+1}^{T}i\cdot p_i\\0&1\end{bmatrix}\begin{bmatrix}f_{n-1}\\ 1\end{bmatrix}=\begin{bmatrix}f_n\\ 1\end{bmatrix}$$

但是，只有当 $\left\lfloor f_{n-1}\right\rfloor$ 不变时，前面的方阵才能实行快速幂，而 $\left\lfloor f_{n-1}\right\rfloor$ 的取值只有 $T$ 种，所以**分段快速幂**搞定。

**「具体实现看代码吧！」**

总时间 $O(T^2+2^3T\log^2 K)$

**「其实不是很慢耶……话说你时间复杂度有没有搞错啊！」**

额……只能说，欢迎指正！

### Code

```cpp
//Said no more counting dollars. We'll be counting stars.
#include<bits/stdc++.h>
using namespace std;
#define IOS ios::sync_with_stdio(0);cin.tie(0);cout.tie(0)
#define For(i,j,k) for(int i=j;i<=k;i++)
#define Rof(i,j,k) for(int i=j;i>=k;i--)
#define int long long
const int N=1010;
const int lim=1e6;
int t,k;
double p[N],q[N],a[N];
void init(){
	p[1]=1;
	For(i,1,t-1){
		For(j,1,i+1) q[j]=(p[j]+p[j-1])/2;
		For(j,1,i+1) p[j]=q[j]; 
	}
	Rof(i,t,1) a[i]=a[i+1]+p[i]*i;
	For(i,2,t) p[i]+=p[i-1]; 
}
struct node{
	double a[2][2];
	node mul(node x){
		node res;
		For(i,0,1) For(j,0,1) res.a[i][j]=0;
		For(i,0,1) For(j,0,1) For(k,0,1) res.a[i][j]+=a[i][k]*x.a[k][j];
		return res;
	}
	void init(){a[0][0]=1;a[0][1]=0;a[1][0]=0;a[1][1]=1;}
	node pw(int x){
		node tmp;
		For(i,0,1) For(j,0,1) tmp.a[i][j]=a[i][j];
		while(x--) tmp=tmp.mul(tmp);
		return tmp;
	}
}m;
bool check(node x,double y){return (int)(x.a[0][0]*y+x.a[0][1])==(int)(y);}
double modi(double x,int &cnt){
	m.a[0][0]=p[(int)x];m.a[0][1]=a[(int)x+1];
	m.a[1][0]=0;m.a[1][1]=1;
	int tmp=0;
	while((1<<tmp)<=cnt) tmp++;
	node res,out;
	out.init();
	Rof(i,tmp,0){
		if((1<<i)>cnt) continue;
		res=m.pw(i);
		if(check(out.mul(res),x)){
			out=out.mul(res);
			cnt-=(1<<i);
		}
	}
	return out.a[0][0]*x+out.a[0][1];
}
void brute(double &x){x=p[(int)x]*x+a[(int)x+1];}
signed main(){IOS;
	cin>>t>>k;
	init();
	double x=(double)(t+1)/2;
	For(i,2,min(k,lim)) brute(x);
	k-=min(k,lim);
	int lst=k;
	while(lst){
		x=modi(x,lst);
		if(!lst) break;
		lst--;
		brute(x);
	}
	cout<<fixed<<setprecision(10)<<x;
return 0;}
```