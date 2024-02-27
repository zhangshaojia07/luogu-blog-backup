# P2841 A*B Problem

（提供时间复杂度的题解）时间复杂度：$O(36A)$。

### Analysis

我们将问题转化成：

**求一个最小正整数的 $C$，使得 $C$ 为 $A$ 的倍数且十进制表示全部为 $0/1$。**

我们把每一个十进制位 $10^x$ 看作一个物体，体积为 $10^x\bmod A$，价值为：**$x$ 越小越优先**。

就是一个 **01 背包（$\bmod A$ 同余系中）**。

但是物品数量无限啊，咋办？

打个表看看，发现最高位最高不超过 $10^{35}$ 这一位。

[代码](https://www.luogu.com.cn/paste/2hiwthga)

而 $A\le 10^5$ 跑出来也不到 $10^{50}$。（我怀疑是近似对数函数，求大佬证明上界）

也就说，答案最大也只是 $36$ 位数。

接下来就是 DP。$f_{i,j}$ 表示只用 $0$ 到 $i$ 的十进制位，是否可以表示出 $\bmod A=j$ 的数。

转移
$$
f_{i,j}=f_{i-1,j}\ \text{OR}\ f_{i-1,(j-10^{i})\% A}
$$
$$
f_{i,10^{i}\% A}=\text{true}
$$

然后我们从高位到低位判断（类似 trie 树）。若这一位不得不取，我们就取，然后更改接下来的目标同余系；否则不取。

最后得到 $C$ 后再求 $B$（这里偷懒用了 `__int128`）。

### Code

跑了 33 ms，没拿到最优解。

```cpp
#include<bits/stdc++.h>
#define For(i,j,k) for(register int i=j;i<=k;i++)
#define Rof(i,j,k) for(register int i=j;i>=k;i--)
#define N 10000
#define ll __int128
int mod,p[36],T=0,n=1;
bool ans[36],f[36][N],flag=0;
ll A=1,B=0;
inline void print(ll x){
	if(x>=100) print(x/100),printf("%02d",(char)(x%100));
	else printf("%d",(char)(x));
}
int main(){
	scanf("%d",&mod);
	p[0]=1;
	f[0][1]=1;
	for(;;n++){
		p[n]=p[n-1]*10%mod;
		std::copy(f[n-1],f[n-1]+mod,f[n]);
		f[n][p[n]]=1; For(i,0,mod-1) f[n][(i+p[n])%mod]|=f[n-1][i];
		if(f[n][0]) break;
	}
	Rof(i,n,0) if(!i || !f[i-1][T]){
		ans[i]=1;
		(T+=mod-p[i])%=mod;
		if(!T) break;
	}
	For(i,0,n){
		if(ans[i]) B+=A;
		A*=10;
	}
	print(B/mod);printf(" ");
	print(B);puts("");
return 0;}
```