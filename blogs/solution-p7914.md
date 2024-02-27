# P7914 [CSP-S 2021] 括号序列 题解

Upd 2022-1-26：更新了 LaTeX 和标题层级。（更好的阅读体验 OvO）

# Preface

感觉比 T3 难好多 qwq。

纪念我考场上写错的 $O(n^3)$ 区间 DP（边界条件没判 qwq）。

本文半数讲思路过程，所以本题解可能稍长，但最终得出的结论和公式是简洁滴~（大佬们可以直接跳转至 Algorithm 或 Code 部分）

# Analysis

这括号序列题一般是区间 DP。

单纯的想法是设 $dp_{l,r}$ 为 $[l,r]$ 这段子串为合法超级括号序列的方案数。

但这样有问题：

![](https://cdn.luogu.com.cn/upload/image_hosting/dqn9y54t.png)

（$A,B,C,D,E$ 均为合法超级括号序列，$S$ 为题面中定义）

如图，此种情况会算两遍。

# Improve

设 $f_{l,r}$ 表示 $[l,r]$ 这段子串为合法超级括号序列**且两端（$l$ 和 $r$）的括号匹配**（$\mathtt{integral}$）的方案数。

![](https://cdn.luogu.com.cn/upload/image_hosting/adb4xxzv.png)

设 $g_{l,r}$ 表示 $[l,r]$ 这段子串为合法超级括号序列**且两端（$l$ 和 $r$）的括号不匹配**（$\mathtt{separated}$）的方案数。

![](https://cdn.luogu.com.cn/upload/image_hosting/sul4mzmf.png)

这样就能有效避免重复算的情况。

# Algorithm

区间 DP 基本：**按照子串长度从小到大枚举**。

设现在遍历到 $f_{l,r}$ 和 $g_{l,r}$，$len=r-l+1$。

设 $(bool)X_{l,r}$ 表示 $[l,r]$ 是（$1$）否（$0$）全为 `'*'` 和 `'?'`（$O(n^2)$ 预处理）。

（设原字符串为 $s$，从 $1$ 标号，与 DP 数组对齐）

## （一）特判


若 $s_l$ **不能成为** `'('` 或 $s_r$ **不能成为** `')'`，直接 `continue` 掉。

若 $len=2$，直接 $f_{l,r}=1$，`continue` 掉。

## （二） $\mathtt{integral}$

### (S)

$$f_{l,r}+=[X_{l+1,r-1}=true]$$

### (A)

$$f_{l,r}+=f_{l+1,r-1}+g_{l+1,r-1}$$

### (SA)

$$f_{l,r}+=\sum\limits^{k}_{i=1}(f_{l+i+1,r-1}+g_{l+i+1,r-1})[X_{l+1,l+i}=true]$$

### (AS)

$$f_{l,r}+=\sum\limits^{k}_{i=1}(f_{l+1,r-i-1}+g_{l+1,r-i-1})[X_{r-i,r-1}=true]$$

## （三） $\mathtt{separated}$

### ASB & AB

这部分比较麻烦，容易算重，需要谨慎。

我们规定 $B$ 为 $\mathtt{integral}$ 子串，这样就不重不漏了。

$$g_{l,r}+=\sum\limits_{l<i<j<r,j-i-1\leqslant k}((f_{l,i}+g_{l,i})\times f_{j,r})[X_{i+1,j-1}=true]$$

（规定 $X_{a,a-1}=true$）

# Optimization

发现 **ASB & AB** 部分为 $O(n^4)$，而你的愿望是 AK CSP-S 2021，怎么办呢？

思考：上面柿子中若 $i$ 增减 $1$ 则**可取的 $j$ 的范围变化量为 $O(1)$**。

优化成 $O(n^3)$。

为防止思维固化，请读者自行思考优化的具体方式（很简单的 DP 优化，若真不会则看代码）。

# Code

别忘了取模。

```cpp
#include<bits/stdc++.h>
using namespace std;
#define ll long long
#define For(i,j,k) for(register int i=j;i<=k;i++)
#define Rof(i,j,k) for(register int i=j;i>=k;i--)
#define ckmx(a,b) if(a<b){a=b;}
#define N 510
#define madd(a,b) a=(a+b%mod)%mod;
const ll mod=1000000007;
char s[N];
int n,k,R;
ll f[N][N],g[N][N],tmp,cur[N];
int id,nxt[N];
bool X[N][N];
signed main(){
	scanf("%d%d",&n,&k);
	scanf("%s",s+1);
	For(l,1,n) For(r,l,n) X[l][r]=0;
	For(l,1,n){
		if(s[l]!='?' && s[l]!='*') continue;
		X[l][l]=1;
		For(r,l+1,n){
			if(s[r]=='?' || s[r]=='*'){
				X[l][r]=1;
			}
			else break;
		}
	}
	For(len,2,n){
		int r;
		For(l,1,n-len+1){
			r=l+len-1;
			f[l][r]=g[l][r]=0;
			if((s[l]!='(' && s[l]!='?') || (s[r]!=')' && s[r]!='?'))
				continue;
			if(l+1==r){
				madd(f[l][r],1);
				continue;
			}
			//integral
			if(r-l-1<=k && X[l+1][r-1]){
				madd(f[l][r],1);
			}
			madd(f[l][r],f[l+1][r-1]+g[l+1][r-1]);
			For(l1,l+1,min(r-2,l+k))//记得取 min，我考试挂在这 
				if(X[l+1][l1])
					madd(f[l][r],f[l1+1][r-1]+g[l1+1][r-1]);
			Rof(r1,r-1,max(l+2,r-k))//记得取 max，我考试挂在这 
				if(X[r1][r-1])
					madd(f[l][r],f[l+1][r1-1]+g[l+1][r1-1]);
			//separated
			id=0;
			For(i,l+1,r-2){
				if(id<=i) id=i+1;
				while((s[id]=='*' || s[id]=='?') && id<r-1) id++;
				nxt[i]=min(i+k+1,id);
			}
			tmp=0;
			For(j,l+2,nxt[l+1]){
				madd(tmp,f[j][r]);
			}
			madd(g[l][r],(f[l][l+1]+g[l][l+1])%mod*tmp);
			For(i,l+2,r-2){
				madd(tmp,mod-f[i][r]);
				For(j,nxt[i-1]+1,nxt[i]) madd(tmp,f[j][r]);
				madd(g[l][r],(f[l][i]+g[l][i])%mod*tmp);
			}
		}
	}
	printf("%lld\n",(f[1][n]+g[1][n])%mod);
	return 0;
}
```