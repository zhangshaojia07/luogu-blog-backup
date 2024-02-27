# CF1699E Three Days Grace

~~这是 DP 吗？啊？~~

由于我们要分解后**极差最小**，相当于**固定最小值**后求**最小的最大值**。

设我们当前定的最小值为 $mn$，$f_x$ 表示 $x$ 分解成不小于 $mn$ 后最大的因数的最小值（若 $x<mn$ 则无意义），显然我们要的是 
$$
\max_{x\in A}f_x
$$
我们将 $mn$ 从大至小递减，每个答案取最小值即为最后输出。

考虑如何快速将 $mn+1$ 转移到 $mn$。我们惊奇地发现，只有 $mn$ 的倍数的 $f$ 才会改变，即
$$
f_x=\min(f_x,f_{x/mn})
$$
注意这里 $f_{x/mn}$ 为变换过的，即若 $x$ 中含多个 $mn$，则其实是
$$
f_x=\min(f_x,f_{x/mn},f_{x/mn^2},f_{x/mn^3},\dots)
$$
这样我们就以调和级数 $O(m\log m)$ 的复杂度解决本题。

```cpp
//We'll be counting stars.
#pragma GCC optimize("Ofast")
#include<bits/stdc++.h>
using namespace std;
#define For(i,j,k) for(int i=(j),i##_=(k);i<=i##_;i++)
#define Rof(i,j,k) for(int i=(j),i##_=(k);i>=i##_;i--)
#define N 5000010
int n,m,f[N],t[N],pos,ans;
bool a[N];
inline void upd(int id,int val){
	if(a[id]){
		t[f[id]]--;
		t[f[id]=val]++;
		while(!t[pos]) pos--;
	}else{
		f[id]=val;
	}
}
void work(){
	scanf("%d%d",&n,&m); 
	fill(a+1,a+1+m,0);
	fill(f+1,f+1+m,m+1);
	fill(t+1,t+2+m,0);
	int x;
	For(i,1,n){
		scanf("%d",&x);
		a[x]=1;
	}
	For(i,1,m) if(a[i]) t[m+1]++;
	ans=pos=m+1;
	Rof(i,m,1){
		upd(i,i);
		For(j,i,m/i) if(f[i*j]>f[j]) upd(i*j,f[j]);
		if(pos<=m) ans=min(ans,pos-i);
	}
	printf("%d\n",ans);
}
int main(){
	int T;scanf("%d",&T); 
	while(T--)work();
return 0;}
```