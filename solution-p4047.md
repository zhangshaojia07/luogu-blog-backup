# P4047 [JSOI2010]部落划分（Prim）

发现题解区全都是带 $\log$ 的解法……要么 Kruskal 要么二分答案。咋的，**我堂堂 $O(n^2)$ Prim 不被认可吗？**

[喜提最优解 36 ms。](https://www.luogu.com.cn/record/77615656)

如其他题解所说，我们要做的是求出这个**完全图**的最小生成树（以下简称 MST）。因为 MST 上每一条边删去后两部分之间的最短距离一定就是这条边的边权本身，所以一种最优的部落划分方案就是删去 MST 中边权最大的 $k-1$ 条边。

也就是说，答案为 MST 中边权第 $k-1$ 大。

我们跑 Prim 之后边权排序即可，不会 Prim 的请出门右转百度或看代码。

**时间 $O(n^2)$。**

```cpp
//We'll be counting stars.
#include<bits/stdc++.h>
using namespace std;
#define For(i,j,k) for(int i=(j),i##_=(k);i<=i##_;i++)
#define db double
#define N 1001
int tot=0,n,k,a[N],b[N];
bool vis[N];
db dis[N],s[N];
inline db pw(int x){return (db)x*x;}
inline db Dis(int x,int y){return sqrt(pw(a[x]-a[y])+pw(b[x]-b[y]));}
int main(){
	scanf("%d%d",&n,&k);
	For(i,1,n) scanf("%d%d",a+i,b+i);
	For(i,0,n) dis[i]=(i==1)?0:1e9;
	For(i,1,n){
		int x=0;
		For(j,1,n) if(!vis[j] && dis[j]<dis[x]) x=j;
		vis[x]=1;
		s[tot++]=dis[x];
		For(j,1,n) if(!vis[j]) dis[j]=min(dis[j],Dis(x,j));
	}
	sort(s+1,s+tot,greater<db>());
	printf("%.2lf\n",s[k-1]);
return 0;}
```