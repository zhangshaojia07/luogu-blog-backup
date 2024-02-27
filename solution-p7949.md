# P7949 [✗✓OI R1] 左方之地（格雷码变形）

### Preface

比较形式化的题解。

### Analysis

同题 [arc138_d](https://atcoder.jp/contests/arc138/tasks/arc138_d)，建议先看 [P5657 [CSP-S2019] 格雷码](https://www.luogu.com.cn/problem/P5657)。

以下 $\text{popcnt}(x)$ 表示 $x$ 在二进制下 $1$ 位的个数，$A+B$ 表示字符串拼接。

先判无解，$n\le k$ 无解。而且 $k \bmod 2=0$ 也无解，因为若每次将偶数个位置 $01$ 互换，则每个数 $\text{popcnt}$ 必然奇偶性相同，必然有些数取不到。

~~打表可知~~接下来构造证明这是充要条件。

由于正常的格雷码相邻两数正好相差 $1$ 个二进制位，所以我们引出「$k$ 格雷」表示相邻两数正好相差 $k$ 位的格雷码序列。

我们记 $s(n,k)$ 表示一种 $n$ 位「$k$ 格雷」序列。

$f(\{a\},x)$ 表示将 $\{a\}$ 中每个元素都 $\text{xor}$ 上 $x$ 后得到的序列。

$rev(\{a\})$ 表示将 $\{a\}$ 序列翻转（reverse）后的序列。

$sort(\{a\})$ 表示将 $\{a\}$ 序列升序排序后的序列。

------------

**结论 A：**

$$\text{popcnt}(a\ \text{xor}\ b)=\text{popcnt}((a\ \text{xor}\ x)\ \text{xor}\ (b\ \text{xor}\ x))$$

非常显然。

**结论 B：**

$$A=\{0,1,\dots,2^n-1\}$$

$$\forall 0\le x<2^n,sort(f(A,x))=A$$

非常显然。


------------


所以对于 $n\ge k+2$，我们有（有点长 认真看 qwq）：

$$s(n,k)=s(n-1,k)+f(rev(s(n-1,k)),2^{n-k}(2^k-1))$$

接下来我们只需要考虑如何构造 $s(n,n-1)(n \bmod 2=0)$。

$$a_i=\begin{cases}
i\ \text{xor}\ \frac{i}{2} &i\bmod 2=0
\\
i\ \text{xor}\ \frac{i-1}{2}\ \text{xor}\ (2^n-1) &i\bmod 2=1
\end{cases}$$

即为构造（下标从 $0$ 开始）。

其实主要用到了 $\text{low}(x)$ 的性质（就是树状数组里的那个）。

**证明：**

对于 $i\ \bmod\ 2=0$：

$$a_i\ \text{xor}\ a_{i+1}=i\ \text{xor}\ (i+1)\ \text{xor}\ (2^n-1)=1\ \text{xor}\ (2^n-1)$$

对于 $i\ \bmod\ 2=1$：

$$\begin{aligned}
a_i\ \text{xor}\ a_{i+1}&=
i\ \text{xor}\ (i+1)\ \text{xor}\ \frac{i-1}{2}\ \text{xor}\ \frac{i+1}{2}\ \text{xor}\ (2^n-1)
\\
&=
(2\text{low}(i+1)-1)\ \text{xor}\ (\text{low}(i+1)-1)\ \text{xor}\ (2^n-1)
\\
&=
(2\text{low}(i+1)-1)\ \text{xor}\ (\text{low}(i+1)-1)\ \text{xor}\ (2^n-1)
\\
&=
\text{low}(i+1)\ \text{xor}\ (2^n-1)
\end{aligned}$$

$\blacksquare$

### Code

```cpp
//Said no more counting dollars. We'll be counting stars.
#include<bits/stdc++.h>
using namespace std;
#define For(i,j,k) for(int i=j;i<=k;i++)
#define N (1<<20)
int a[N];
void solve(int n,int k){
	int len=1<<n;
	if(n==k+1){
		For(i,0,len-1)
			if(i&1) a[i]=i^(i>>1)^(len-1);
			else a[i]=i^(i>>1);
		return ;
	}
	solve(n-1,k);
	int pos=1<<(n-1),val=(1<<n)-(1<<(n-k));
	For(i,pos,len-1) a[i]=a[--pos]^val;
}
signed main(){
	int n,k,len;
	scanf("%d%d",&n,&k);
	len=1<<n;
	if(n==1 && k==1){puts("1\n0 1");return 0;}
	if(n<=k || !(k&1)){puts("0");return 0;}
	puts("1");
	solve(n,k);
	For(i,0,len-1) printf("%d ",a[i]);
return 0;}
```
