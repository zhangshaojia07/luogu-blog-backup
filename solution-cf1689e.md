# CF1689E ANDfinity

$O(30n)$（$30$ 为二进制位数）暴虐标算，喜提 CF 最优解 15 ms（（（

显然地，我们把 $\{a\}$ 中所有 $0$ 全都 $+1$ 变成 $1$。

接下来我们只考虑无 $0$ 串的答案。

我们设值的 $\text{lowbit}$ 最大的那些元素集合为 $S$。

我们可以保证答案 $\le 2$，证明如下：

* 若 $|S|=1$，则将这个元素 $-1$ 后必然全连通。

* 若 $|S|>1$，则将其中一个 $-1$，另一个 $+1$，必然连通。

（这里比较简略，具体请移步别的题解）

所以，我们得到了算法流程：

1. 先通过枚举每个二进制位来 $O(30n)$ 判断是否已经连通。

2. 否则，我们遍历 $S$ 中的每一个元素，尝试 $+1$ 和 $-1$ 是否能连通。

3. 否则，我们确定答案 $=2$，随便在 $S$ 中一个 $+1$，一个 $-1$ 即可。

再具体一点：

第一步时，我们枚举二进制的每一位，然后并查集合并这一位为 $1$ 的元素。

第二步前半部分，我们判断第一步结束后的并查集数量。若 $=2$，则有可能 $+1$ 后连通。我们判断是否有奇数出现在非 $|S|$ 出现的并查集里即可。

第二步后半部分，由于这个 $S$ 中 $-1$ 的元素能集结所有不在 $S$ 中的元素，我们只要判断这个元素 $-1$ 后是否能连通 $S$ 中其他元素。我们将非 $S$ 元素 $\text{bitwise or}$ 起来，以及 $S$ 中（按下标）前缀与后缀也 $\text{or}$ 起来。然后对于每一个 $S$ 中元素 $-1$ 是否可行就可以 $O(1)$ 判断了，具体看代码。

精简但不可观的代码：

```cpp
//We'll be counting stars.
#pragma GCC optimize("Ofast")
#include<bits/stdc++.h>
using namespace std;
#define For(i,j,k) for(int i=(j),i##_=(k);i<=i##_;i++)
#define Rof(i,j,k) for(int i=(j),i##_=(k);i>=i##_;i--)
#define N 2022
int n,a[N],ans,f[N],cnt,mx,qwq;
int pre[N],suf[N],pos[N],tot;
inline int low(int x){ return x&(-x); }
inline int gf(int x){ return x==f[x]?x:f[x]=gf(f[x]); }
inline void merge(int x,int y){
	x=gf(x),y=gf(y);
	if(x!=y) cnt--,f[x]=y;
}
void work(){
	int x;
	ans=0;
	For(i,1,n) if(a[i]==0) a[i]=1,ans++;
	cnt=n;
	For(i,1,n) f[i]=i;
	For(j,0,29){
		x=0;
		For(i,1,n) if(a[i]&(1<<j)){
			if(x==0) x=i;
			else merge(x,i);
		}
	}
	if(cnt==1) return ;
	ans++;
	mx=tot=qwq=0;
	For(i,1,n) mx=max(mx,low(a[i]));
	For(i,1,n) if(low(a[i])==mx) pos[++tot]=i; else qwq|=a[i];
	if(cnt==2){
		x=gf(pos[1]);
		For(i,1,n) if(gf(i)!=x && a[i]&1){
			a[pos[1]]++;
			return ;
		}
	}
	if(tot==1){
		a[pos[1]]--;
		return ;
	}
	pre[0]=suf[tot+1]=0;
	For(i,1,tot) pre[i]=pre[i-1]|a[pos[i]];
	Rof(i,tot,1) suf[i]=suf[i+1]|a[pos[i]];
	For(i,1,tot) if((pre[i-1]|suf[i+1])&((a[pos[i]]^mx)|qwq)){
		a[pos[i]]--;
		return ;
	}
	ans++;
	a[pos[2]]++;
	a[pos[1]]--;
}
int main(){
	int T;scanf("%d",&T);
	while(T--){
		scanf("%d",&n);
		For(i,1,n) scanf("%d",a+i);
		work();
		printf("%d\n",ans);
		For(i,1,n) printf("%d ",a[i]); puts("");
	}
return 0;}
```