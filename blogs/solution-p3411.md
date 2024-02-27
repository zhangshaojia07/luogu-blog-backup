# P3411 序列变换（真正正解）

[给出几组 Hack 数据](https://www.luogu.com.cn/paste/q1m794w3)

这里是 $O(n)$ 正解。

首先，题目等价于要求出最长的子序列，使得：

* 子序列单调非降。

* 若子序列中最小 / 大值为 $lv,rv$，则值属于 $[lv+1,rv-1]$ 的元素均在此子序列里。

我们设 $a_1,\dots,a_n$ 为原序列，定义 $L_x,R_x$ 表示值为 $x$ 的元素下标的最小 / 大值。

所以我们要满足 $\forall v\in[lv+2,rv-1],R_{v-1}<L_v$。

我们把满足上述条件的连续值域区间称为「值域段」。我们分别考虑每一个值域段，设当前值域为 $[lx,rx]$。

由于我们想让子序列最长，所以贪心地将 $[lx,rx]$ 值域内的元素全选，然后我们再选进 下标 $<L_{lx}$ 值为 $lx-1$ 的元素 和 下标 $>R_{rx}$ 值为 $rx+1$ 的元素。

最后每一个值域段取最大值即可。

[AC 记录](https://www.luogu.com.cn/record/79130016)

```cpp
//We'll be counting stars.
#include<bits/stdc++.h>
using namespace std;
#define For(i,j,k) for(int i=(j),i##_=(k);i<=i##_;i++)
#define Rof(i,j,k) for(int i=(j),i##_=(k);i>=i##_;i--)
#define ckmx(a,b) a=max(a,b)
#define ckmn(a,b) a=min(a,b)
#define N 1000005
int L[N],R[N],a[N],lim,ans=0,st,s[N],n,b[N],nxt[N],cnt[N];
int work(int lv,int rv){
	int res=0;
	For(i,lv,rv) res+=cnt[i];
	for(int i=L[lv-1];i;i=nxt[i]) res+=(i<L[lv]);
	for(int i=L[rv+1];i;i=nxt[i]) res+=(i>R[rv]);
	return res;
}
int calc(int x){
	st=0;
	for(int i=L[x],j=L[x+1];i||j;)
		if(!j||(i&&i<j)) s[++st]=0,i=nxt[i];
		else s[++st]=1,j=nxt[j];
	For(i,1,st) s[i]+=s[i-1];
	int res=0,sz=cnt[x+1];
	For(i,0,st) ckmx(res,i+sz-2*s[i]);
	return res;
}
int main(){
	scanf("%d",&n);
	For(i,1,n) scanf("%d",a+i);
	For(i,1,n) b[a[i]]=1;
	For(i,1,N-1) b[i]+=b[i-1];
	For(i,1,n) a[i]=b[a[i]];
	fill(L+1,L+1+(lim=b[N-1]),n+1);
	fill(b+1,b+1+lim,0);
	Rof(i,n,1) ckmx(R[a[i]],i),ckmn(L[a[i]],i),nxt[i]=b[a[i]],b[a[i]]=i,cnt[a[i]]++;
	For(i,1,lim) ckmx(ans,cnt[i]);
	For(i,1,lim-1) ckmx(ans,calc(i));
	int lst=1;
	For(i,2,lim+1) if(R[i-1]>L[i] || i==lim+1) ckmx(ans,work(lst,i-1)),lst=i;
	printf("%d\n",n-ans);
return 0;}
```