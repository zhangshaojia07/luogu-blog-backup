# P1891题解

**Update 2021-8-23:加上了线性时间复杂度的代码。**

**Update 2021-8-19:优化了时间复杂度（感谢 [@wkywkywky](https://www.luogu.com.cn/user/133954)）。**

**Update 2021-8-17:改正公式错误（感谢 [@wsyear](https://www.luogu.com.cn/user/169574)）。**

### 这是一篇清晰的、有思考过程的、有详细码析的数论题解。

如有任何问题评论区见。

步入正题。

## 题目简述

求 ${\rm lcm}$ 的前缀和。

## 题目分析

直接推公式：

$$\sum\limits_{i=1}^n{\rm lcm}(i,n)$$

$$=\sum\limits_{i=1}^n\frac{i\times n}{\gcd(i,n)}$$

$$=n\sum\limits_{i=1}^n\frac{i}{\gcd(i,n)}$$

**重点1：转换枚举顺序，先枚举 $\gcd$,再枚举  $\gcd$ 的倍数 $i$。**

$$=n\sum\limits_{d|n}\sum\limits_{i=1}^n[\gcd(i,n)=d]\frac{i}{d}$$

$$=n\sum\limits_{d|n}\sum\limits_{i=1}^n[d|i][\gcd(\frac{i}{d},\frac{n}{d})=1]\frac{i}{d}$$

**重点2：转换枚举的东西，枚举 $i$ 变成枚举 $\frac{i}{d}$。**

$$=n\sum\limits_{d|n}\sum\limits_{i=1}^{\frac{n}{d}}[\gcd(i,\frac{n}{d})=1]i$$

**重点3：最内层的求和实际上是在 $1$ 至 $\frac{n}{d}$ 中，与 $\frac{n}{d}$ 互素的数的总和，想到欧拉函数。**

$$=n\times (1+\sum\limits_{d|n \ d<n}\frac{n}{2d}\times \varphi(\frac{n}{d}))$$

**难懂？**

* 当 $\frac{n}{d}=1$ 时，总和为 $1$（她本身）。

* 当 $\frac{n}{d}>1$ 时，举个例子：

![](https://i.loli.net/2021/08/18/yjiCB9dFlHNRDLe.png)

任取一 $k\in\mathbb{N}$ 且 $k<\frac{n}{d}$，则：

$$[\gcd(k,\frac{n}{d})=1]=[\gcd(\frac{n}{d}-k,\frac{n}{d})=1]$$

（你猜这和辗转相除法有什么关系）

**所以在 $1$ 至 $\frac{n}{d}$ 中，与 $\frac{n}{d}$ 互素的数的平均数是 $\frac{n}{2d}$。($\frac{n}{d}>1$)**

**重点4：积性函数能线性筛！！！**

所以将 $\frac{1}{2}$ 从求和中提出来：

$$=n\times (\frac{1}{2}+\sum\limits_{d|n}\frac{n}{2d}\times \varphi(\frac{n}{d}))$$

$$=\frac{n}{2}\times (1+\sum\limits_{d|n}\frac{n}{d}\times \varphi(\frac{n}{d}))$$

$$=\frac{n}{2}\times (1+\sum\limits_{d|n}d\times \varphi(d))$$

其中设 $f(n)=\sum\limits_{d|n}d\times \varphi(d)$，她是积性函数（证明交给读者）

$$=\frac{n}{2}\times(1+f(n))$$

时间复杂度：$O(n\log n+T)$

(感谢 [wkywkywky](https://www.luogu.com.cn/user/133954) 大佬指出的错误：这个 $log(n)$ 是下面代码 `while(ii%p[j]==0)` 得到的，所以不算严格的**线性**筛)

## 注意细节

线性筛时，素数（及素数的幂）的初值自己推一下。

## 完整代码

**禁  止  抄  袭**

```cpp
//https://www.luogu.com.cn/problem/P1891
#include<bits/stdc++.h>
#define For(i,j,k) for(register int i=j;i<=k;i++)
#define IOS ios::sync_with_stdio(0),cin.tie(0),cout.tie(0)
using namespace std;
#define N 1000000
#define int long long//不开longlong见祖宗 

int n;
bool vis[N+10];//是否被筛过 
int p[N+10];//素数表 
int f[N+10];//积性函数 
void sieve(){//线性筛 
	f[1]=1;//积性函数f(1)=1 
	int k;
	int ii,jj;//替身 
	For(i,2,N){
		if(!vis[i]){//素数 
			f[i]=i*(i-1)+1;//素数初值
			p[++p[0]]=i; 
		}
		For(j,1,p[0]){//枚举最小素因子 
			k=i*p[j];
			if(k>N)break;//>max
			vis[k]=1;//你永远失去了当素数的机会 
			ii=i;
			jj=p[j];
			while(ii%p[j]==0){
				ii/=p[j];
				jj*=p[j];
			}
			//至此ii与jj互素 
			if(ii>1){
				f[k]=f[ii]*f[jj];
			}
			else{//k是p[j]的幂次 
				ii*=p[j];
				jj/=p[j];
				f[k]=f[jj]+k*(k-k/p[j]);//素数幂次递推 
			}
			if(i%p[j]==0) break;//最小素因子为p[j] 
		}
	} 
}
signed main(){
	IOS;
	sieve();
	int T;
	cin>>T;
	while(T--){
		cin>>n;
		//f[n]必为奇数 
		cout<<(1+f[n])/2*n<<endl;
	}
    return 0;
}
```

## Update 2021-8-19

![](https://i.loli.net/2021/08/18/CKNI5J9ODvbQupf.png)

所以存一下每个 $x\in[1,n]$ 中最小素因子的个数，将时间复杂度降到 $O(n+T)$（虽然 $O(n\log n+T)$ 也能过）。

代码先鸽着（记得催我）。

## Update 2021-8-23

不鸽了，开干~

**同  样  禁  止  抄  袭**

```cpp
#include<bits/stdc++.h>
#define For(i,j,k) for(register int i=j;i<=k;i++)
#define IOS ios::sync_with_stdio(0),cin.tie(0),cout.tie(0)
using namespace std;
#define N 1000000
#define int long long//不开longlong见祖宗 

int n;
int part[N+10];//除最小素因子外剩余部分的值 
bool vis[N+10];//是否被筛过 
int p[N+10];//素数表 
int f[N+10];//积性函数 
void sieve(){//线性筛 
	f[1]=1;//积性函数f(1)=1 
	part[1]=1;
	int k;
	For(i,2,N){
		if(!vis[i]){//素数 
			f[i]=i*(i-1)+1;//素数初值
			p[++p[0]]=i; 
			part[i]=1;//本身就是最小素因子
		}
		For(j,1,p[0]){//枚举最小素因子 
			k=i*p[j];
			if(k>N)break;//>max
			vis[k]=1;//你永远失去了当素数的机会 
			if(i%p[j]){//不整除
				part[k]=i;
				f[k]=f[i]*f[p[j]];
			}else{
				part[k]=part[i];
				if(part[i]>1){//你心里还有别人
					f[k]=f[part[i]]*f[i/part[i]*p[j]]; 
				}else{//你心里没有别人
					f[k]=f[i]+k*(k-k/p[j]);//素数幂次递推 
				}
				break;
			} 
		}
	} 
}
signed main(){
	IOS;
	sieve();
	int T;
	cin>>T;
	while(T--){
		cin>>n;
		cout<<(1+f[n])/2*n<<endl;
	}
    return 0;
}
```

还是非常快的 OvO [评测记录](https://www.luogu.com.cn/record/56747682)