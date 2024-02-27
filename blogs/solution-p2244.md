# P2244 选举预测

~~为啥机房里 dalao 们都不屑一顾。~~

这里有最严谨的证明（自己推的，有疏漏请指出）。

我们把人看作点，$x$ 必然胜 $y$ 看作 $x\to y$ 的有向边，我们称点是「好的」当且仅当这个点可能取得最终胜利，设 $N(x)$ 表示 $x$ 点走一步能到达的点集，$V$ 为点的全集，称一个点 $x$ 的「反集」为 $V-x-N(x)$。

* * *

**首先我们证明出度最大的点（可能不止一个）是好的。**

反证法。假设出度最大的点之一 $x$ 不好。我们这样构造比赛顺序：

1. 让 $x$ 的反集之间对战（随便打），决出最后的赢家设为 $y$。

2. 将 $y$ 依次与 $N(x)$ 中的点对战（若 $y$ 与 $N(x)$ 中某点间没边，让 $N(x)$ 那头赢），设赢家为 $z$。

若 $z\ne y$，则 $z\in N(x)$，最后让 $x$ 打败 $z$ 即可，最后赢家 $x$ 是好的，矛盾。

若 $z=y$，则 $N(x)\subseteq N(y)$，由于 $x$ 出度的最大性，得到 $|N(x)|\ge |N(y)|$，进一步 $N(x)=N(y)$，得到 $x,y$ 之间没有边，最后让 $x$ 打败 $y(=z)$ 即可，矛盾。

综上，原命题得证。

* * *

**其次我们证明若一个点是好的，则其反集也都是好的。**

设 $x$ 的反集中有 $y$，我们先让 $x$ 成为 $y$ 除外的最后赢家，然后 $y$ 再打败 $x$ 即可。

* * *

**实现：**

我们找到出度最大的点，然后向反集 BFS 即可，过程中用并查集优化可至 $O(n+m)$（$m$ 为边数）。

* * *

**最后我们证明我们找到好的点以外都是不好的。**

设我们通过刚刚方法找到的好的集合为 $S$。当一个点 $x\notin S$ 第一次与 $S$ 中的人对战的时候，由 $S$ 的定义，$x$ 必输，推出 $x$ 不是好的。

* * *

完结撒花上代码。

```cpp
//We'll be counting stars.
#include<bits/stdc++.h>
using namespace std;
#define pb emplace_back
#define For(i,j,k) for(int i=(j),i##_=(k);i<=i##_;i++)
#define N 1000010
vector<int> e[N],id;
int f[N],n;
bool b[N];
queue<int> q;
inline int gf(int x){ return x==f[x]?x:f[x]=gf(f[x]); }
inline void del(int x){ f[x]=gf(x+1),q.push(x); }
int main(){
	scanf("%d",&n);
	int k,x,mx=-1,cnt=n;
	For(i,1,n){
		scanf("%d",&k);
		if(k>mx) id.clear(),mx=k;
		if(k==mx) id.pb(i);
		while(k--) scanf("%d",&x),e[i].pb(x);
	} 
	For(i,1,n+1) f[i]=i;
	for(int i:id) del(i);
	while(!q.empty()){
		x=q.front();
		q.pop();
		for(int i:e[x]) b[i]=true;
		for(int i=gf(1);i<=n;i=gf(i+1)) if(!b[i]) del(i);
		for(int i:e[x]) b[i]=false;
	}
	for(int i=gf(1);i<=n;i=gf(i+1)) b[i]=1,cnt--;
	printf("%d",cnt); For(i,1,n) if(!b[i]) printf(" %d",i);
return 0;}
```