# CF1698E PermutationForces II

提供一种线性复杂度的方法。

### Analysis

首先，我们发现**每个数在序列中的绝对位置无用**，所以我们将 $\{a\},\{b\}$ 都进行如下变换（$\{b\}$ 中 $-1$ 的那些不用管）：

设原序列为 $\{A\}$，新序列为 $\{B\}$，$B_{A_i}=i$。

（也就是置换的逆）

对于 $\{b\}$ 的新序列，没有被赋值的设为 $-1$。

接下来 $\{a\},\{b\}$ 均指新序列。

那我们的操作就转化为：

$i:1\to n$，每次选择两个**下标** $i \leq x \leq y \leq \min(i+s,n)$ 然后交换两个位置上的元素。

* * *

**结论：** $\{a\}$ 能变成 $\{b\}$ 当且仅当对于任意元素 $x$，设在 $a,b$ 中的下标为 $i,j$，有 $j\ge i-m$。

这里就不证明了，不代表不会……

* * *

然后就转化成：

有 $k$ 个小朋友和 $k$ 颗糖果（$k$ 为 $\{b\}$ 中 $-1$ 的个数），每个小朋友都喜欢糖果序列的一个后缀，问有几种分配方案使得小朋友都拿到喜欢的糖果。

这是一个很经典且简单的 $O(k)$ 问题，不再赘述。

### Code

```cpp
//We'll be counting stars.
#pragma GCC optimize("Ofast")
#include<bits/stdc++.h>
using namespace std;
#define For(i,j,k) for(int i=(j),i##_=(k);i<=i##_;i++)
#define Rof(i,j,k) for(int i=(j),i##_=(k);i>=i##_;i--)
#define ll long long
#define N 200000
const ll mod=998244353;
ll ans;
int n,m,a[N],b[N],c[N],pos[N],tot,x;
void work(){
	cin>>n>>m;
	For(i,0,n-1) cin>>x,a[x-1]=i;
	For(i,0,n-1) b[i]=-1;
	For(i,0,n-1){
		cin>>x;
		if(x!=-1) b[x-1]=i;
	}
	For(i,0,n-1) pos[i]=-1;
	For(i,0,n-1) if(b[i]!=-1) pos[b[i]]=i;
	For(i,0,n-1) b[i]=(b[i]==-1);
	Rof(i,n-2,0) b[i]+=b[i+1];
	tot=0;
	For(i,0,n-1)
		if(pos[a[i]]==-1) c[tot++]=b[max(0,i-m)];
		else if(i-pos[a[i]]>m){
			cout<<0<<endl;
			return ;
		}
	ans=1;
	For(i,0,tot-1) (ans*=c[i]-(tot-1-i))%=mod; 
	cout<<ans<<endl;
}
int main(){
	ios::sync_with_stdio(false);
	cin.tie(nullptr);
	int T;cin>>T; 
	while(T--)work();
return 0;}
```