# CF1647F Madoka and Laziness

### Analysis

诈骗题：发现**必然有一个峰值是全局最大值**（位置设为 $x$），所以最终答案小于 $n$。

我们约定**另一个峰值在最大值之右**（位置设为 $y$）（之左的情况 reverse 序列再做一遍）。那我们要做的就是：

1. 将 $[1,x]$ **分解成两个递增序列**，让不包含 $x$ 位置的序列右端值最小（贪心）。

2. 将 $[y,n]$ **分解成两个递减序列**，同上方贪心让序列左端值最小。

3. 将 $[x,y]$ **分解成（一个递减、一个递增）两个序列**，必须保证 $a_x$ 在递减序列、$a_y$ 在递增序列里。

![](https://cdn.luogu.com.cn/upload/image_hosting/58zy3td8.png)

步骤 $1,2$ 可以用简单的线性 DP 解决（记得将步骤 $2$ 的 DP 数组计算到 $x+1$，并保存）。

步骤 $3$ 接上步骤 $1$ 序列的末尾，从 $x$ 向右出发 DP，保存（其实就是有初始值的 CF1144G）。

然后枚举 $y\in[x+1,n]$，用步骤 $2,3$ 的 DP 结果判断是否能接上，统计答案即可。

### Code

[Link](https://codeforces.com/contest/1647/submission/152448488)

```cpp
//Said no more counting dollars. We'll be counting stars.
#pragma GCC optimize("Ofast")
#include<bits/stdc++.h>
using namespace std;
#define IOS ios::sync_with_stdio(0);cin.tie(0);cout.tie(0)
#define For(i,j,k) for(int i=j;i<=k;i++)
#define Rof(i,j,k) for(int i=j;i>=k;i--)
#define ckmx(a,b) a=max(a,b)
#define ckmn(a,b) a=min(a,b)
#define N 500010

const int inf=1e9+7;
int n,a[N],mx=1,ans=0,f[N][2],g[N],h[N];
void solve(){
	g[1]=0;
	For(i,2,mx){
		g[i]=inf;
		if(a[i-1]<a[i]) ckmn(g[i],g[i-1]);
		if(g[i-1]<a[i]) ckmn(g[i],a[i-1]);
	}
	if(g[mx]==inf) return ;
	int sta=g[mx];
	h[n]=0;
	Rof(i,n-1,mx+1){
		h[i]=inf;
		if(a[i+1]<a[i]) ckmn(h[i],h[i+1]);
		if(h[i+1]<a[i]) ckmn(h[i],a[i+1]);
	}
	f[mx][0]=0,f[mx][1]=sta;
	For(i,mx+1,n){
		f[i][0]=0,f[i][1]=inf;
		if(a[i-1]<a[i]) ckmx(f[i][0],f[i-1][0]);//uu
		if(a[i-1]>a[i]) ckmn(f[i][1],f[i-1][1]);//dd
		if(f[i-1][1]<a[i]) ckmx(f[i][0],a[i-1]);//du
		if(f[i-1][0]>a[i]) ckmn(f[i][1],a[i-1]);//ud
	}
	For(i,mx+1,n) if(f[i][0]>h[i]) ans++;	
}
signed main(){IOS;
	cin>>n;
	For(i,1,n) cin>>a[i];
	For(i,1,n) if(a[mx]<a[i]) mx=i;
	solve();
	mx=n+1-mx;
	reverse(a+1,a+1+n);
	solve();
	cout<<ans<<endl;
return 0;}
```