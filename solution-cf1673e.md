# CF1673E Power or XOR?（组合数奇偶性）

### Analysis

我们设 $B(n,m)$ 表示 $n$ 个不同的球取至少 $m$ 个的方案数，即

$$
B(n,m)=\sum_{i=m}^n\binom{n}{i}
$$

------------

显然这是一道**算贡献题**，即枚举每一段极长的 Pow 区间，看是否异或在答案里。

首先，发现 Pow 区间长度 $>21$ 一律没有贡献。因为最后答案要 $\bmod\ 2^{2^{20}}$，而这样区间算出来的值一定是其倍数。

所以我们就可以枚举约 $21n$ 个可能有贡献的区间，一一判断即可。

若待判断的区间为 $a[l\dots r]$。

若 $l>1$ 且 $r<n$，则被异或进答案的次数为 $B(n-(r-l+1)-2,k-2)$，若为奇数就对答案有贡献。

$B(n-(r-l+1)-2,k-2)$ 的原因是已经确定 $r-l$ 个为 Pow，两边也确定为 Xor。所以剩下 $n-(r-l+1)-2$ 个符号位置至少要放 $k-2$ 个 Xor。

若 $l=1$ 且 $r=n$，请特判。

若 $l=1$ 且 $r<n$，次数为 $B(n-(r-l+1)-1,k-1)$。

原因是已经确定 $r-l$ 个为 Pow，右侧也确定为 Xor。所以剩下 $n-(r-l+1)-1$ 个符号位置至少要放 $k-1$ 个 Xor。

若 $l>1$ 且 $r=n$，与上述情况类似，不再赘述。

那咋求 $B(n,m)$ 的奇偶呢？

$$\begin{aligned}
B(n,m)&=\sum_{i=m}^n\binom{n}{i}
\\
&=\sum_{i=m}^{n-1}\binom{n}{i}+1
\\
&=\sum_{i=m}^{n-1}\binom{n-1}{i-1}+\sum_{i=m}^{n-1}\binom{n-1}{i}+1
\\
&=\sum_{i=m-1}^{n-2}\binom{n-1}{i}+\sum_{i=m}^{n-1}\binom{n-1}{i}+1
\\
&\equiv\binom{n-1}{m-1}+\binom{n-1}{n-1}+1&\pmod{2}
\\
&\equiv\binom{n-1}{m-1}&\pmod{2}
\\
&=\frac{(n-1)!}{(m-1)!(n-m)!}
\end{aligned}$$

所以我们 $\log$ 时间求出 $x!$ 中 $2$ 因子的个数，$B(\dots)$ 就迎刃而解了。

注意上述 $B(\dots)$ 中取到非正数的情况特判。

### Code

```cpp
//Said no more counting dollars. We'll be counting stars.
#pragma GCC optimize("Ofast")
#include<bits/stdc++.h>
using namespace std;
#define For(i,j,k) for(int i=j;i<=k;i++)
#define Rof(i,j,k) for(int i=j;i>=k;i--)
#define N 1048576
#define pc putchar
int n,k,a[N+1];
bitset<N> ans;
void calc(int l,int r){//the bit to change of a[l,r]
	long long res=0;
	For(i,l+1,r) res+=a[i];
	if(res>=20 || (1ll<<res)*a[l]>=N) return ;
	ans.flip((1<<res)*a[l]);
}
inline int F(int x){//the num of factor2 in x! 
	int res=0,y=1;
	while(x>>y) res+=x>>(y++);
	return res;
}
inline bool pan(int x,int y){
	if(!x) return true;//C(0,0)
	if(!y) return false;//2^x
	x--;y--;
	return F(x)-F(y)-F(x-y)==0;//the num of factor2 in C(x-1,y-1) must be 0
}
signed main(){
	scanf("%d%d",&n,&k);
	For(i,1,n) scanf("%d",a+i); 
	For(j,1,min(21,n-k)){
		if(j==n){calc(1,n);continue;}
		if(pan(n-j-1,max(0,k-1))) calc(1,j),calc(n-j+1,n);
		if(j==n-1 || !pan(n-j-2,max(0,k-2))) continue;
		For(i,2,n-j) calc(i,i+j-1);
	}
	bool flag=0;
	Rof(i,N-1,0) if(flag || ans[i]) pc('0'+ans[i]),flag=1;
	if(!flag) pc('0');
return 0;}
```