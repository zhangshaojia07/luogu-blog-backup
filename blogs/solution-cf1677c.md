# CF1677C Tokitsukaze and Two Colorful Tapes（置换转环赋值差的和最大）

### Analysis

将一个位置看成两个色带对应位置颜色之间的边。

这样我们得到了一张 $n$ 个点的图，由许多分离的环构成。

我们要给每个点赋值 $1\to n$，使得每条边权（为两端点点权差的绝对值）之和最大。

若一个点的权值小于两边，则称为「山谷」；若一个点的权值大于两边，则称为「山峰」；剩下的为「山坡」。显然山峰与山谷个数相同。

我们用另一种方式算边权和：设山峰的点权和为 $x$，山谷为 $y$，则边权和为 $2(x-y)$。

由于山坡对答案没贡献，我们尽量多山峰（$=$ 山谷）。考虑单个环，设环长为 $L$，则山峰（$=$ 山谷）个数最多

$$\left\lfloor\frac{L}{2}\right\rfloor$$

设这样算出来山峰最多 $a$ 个。

所以我们将山峰们设为最大的值 $[n-a+1,n]\cap\mathbb{Z}$，山谷们设为最小的 $[1,a]\cap\mathbb{Z}$。

答案也就呼之欲出：

$$2a(n-a)$$

### Code

```cpp
//Said no more counting dollars. We'll be counting stars.
#pragma GCC optimize("Ofast")
#include<bits/stdc++.h>
using namespace std;
#define IOS ios::sync_with_stdio(0);cin.tie(0);cout.tie(0)
#define For(i,j,k) for(int i=j;i<=k;i++)
#define Rof(i,j,k) for(int i=j;i>=k;i--)
#define int long long
#define N 100010

int n,a[N],b[N],ans;
bool vis[N];
int dfs(int x){
	vis[x]=1;
	if(vis[b[x]]) return 1;
	else return 1+dfs(b[x]);
}
void work(){
	cin>>n;
	int x;
	For(i,1,n){
		cin>>x;
		a[x]=i;
	}
	For(i,1,n){
		cin>>x;
		b[i]=a[x];
	}
	ans=0;
	For(i,1,n) vis[i]=0;
	For(i,1,n) if(!vis[i]) ans+=dfs(i)>>1;
	cout<<2*ans*(n-ans)<<endl;
}
signed main(){IOS;
	int T;cin>>T;
	while(T--)work();
return 0;}
```
