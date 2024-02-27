# CF1475G Strange Beauty 题解

这肯定是思路最简短的题解。

### 题意简述

求一个数集的最大子集使得子集中两两呈倍数关系。

### 题目分析

先对原序列 $\{a\}$ 排序。

容易发现：

子集满足子集中两两呈倍数关系 $\iff$ 子集中数里值的大小相邻的两数呈倍数关系

比较绕，举个栗子：

![](https://i.loli.net/2021/10/02/Xw7JnCdc1v4xtGM.png)

#### Proof_start

**充分性：**

因为任意两数呈倍数关系，所以值的大小相邻的两数呈倍数关系。

**必要性：**

设子集里的数分别为：

$$b_1\leqslant b_2\leqslant b_3\leqslant \dots \leqslant b_k$$

因为值的大小相邻的两数呈倍数关系，所以：

$$b_i \ | \ b_{i+1} \quad (1\leqslant i\leqslant k-1)$$

由于**整除的传递性**，对于任意的 $1\leqslant i<j\leqslant k$，均有：

$$b_i \ | \ b_{i+1} \ | \ b_{i+2} \ | \ b_{i+3} \ | \ \dots  \ | \ b_j$$

即 $b_i \ | \ b_j$，原命题得证。

#### Proof_end

所以将 $\{a\}$ 顺序遍历，记录下每一种值 $x$ 出现的最后位置 $pos_x$（若没出现记为 $0$）同时更新 $ans_i$ 表示以 $a_i$ 为最大值的满足题意子集的最大大小。

$ans$ 的更新：

$$ans_i=\max(ans_{pos_j}+1) \quad (j \ | \ i)$$

最后输出 $\max(ans_i) \quad (1\leqslant i\leqslant n)$ 即可。

### Talk is cheap,show me the code.

[Codeforces Status](http://codeforces.com/contest/1475/submission/130529487)

```cpp
int n;
int a[N];
int pos[N];
int ans[N];
int out;
void work(){
	cin>>n;
	For(i,1,n) cin>>a[i];
	sort(a+1,a+1+n);
	For(i,0,N-1) pos[i]=0;
	For(i,1,n){
		ans[i]=0;
		for(int x=1,y;x*x<=a[i];x++){
			if(a[i]%x) continue;
			y=a[i]/x;
			ckmx(ans[i],ans[pos[x]]+1);
			ckmx(ans[i],ans[pos[y]]+1);
		}
		pos[a[i]]=i;
	}
	out=0;
	For(i,1,n) ckmx(out,ans[i]);
	cout<<n-out<<endl;
}
```