# CF1695D2 Tree Queries (Hard Version)

直接 Easy Hard 一起讲了。

特判掉 $n=1$ 和链。

先无根树形态思考。

$deg$ 表示度数。

设 $Y$ 为叶子（$deg=1$）集合。

* * *

**结论：** 查询 $Y$ 必然可行。

**证明：**

将每个叶子向外染色，$deg\ge 3$ 的节点停下（该 $deg\ge3$ 不染色）。

若最终要找的节点（设为 $X$）被染色了，她一定能被找到。

否则我们将染色的点删除，此时相当于还是查询了所有叶子，重复上述过程即可得证。

* * *

所以我们目前的答案为 $|Y|$。

但是我们可以优化这个答案。

记每个叶子 $x$ 染色碰到的 $deg\ge3$ 的点为 $up_x$，对偶地，记 $S_u=\{x\ |\ deg_x=1\ \land\ up_x=u\}$。

我们考察一个 $S$ 非空的节点 $u$，我们证明在 $S$ 中可以少查询一个叶子。

* * *

**证明：**

任取 $S_u$ 中的一个元素 $d$。我们查询 $Y-d$。

将整棵树以 $u$ 定根，设 $d$ 的祖先 $y$ 使得 $y$ 的父亲为 $u$。

若 $X$ 不在 $y$ 所在子树里，那没关系。

否则，任取两个查询点 $o,p$ 不在 $u$ 的同一个子树中，得到 $dis(X,o)-dis(X,p)=dis(u,o)-dis(u,p)$，说明 $X$ 不在 $o$ 或 $p$ 所在的子树中。

由于 $X,y$ 之间必然是链，最后再通过任意查询点 $o$ 得到 $dis(o,X)$ 确定 $X$ 的深度即可。

* * *

所以最终答案就是：

**叶子数 $-\ S$ 集合非空节点数**

形式化：
$$\large{
Ans=|\{x\ |\ deg_x=1\}|-|\{x\ |\ S_x\ne\varnothing\}|
}$$
时间复杂度可做到 $O(n)$。

```cpp
//We'll be counting stars.
#pragma GCC optimize("Ofast")
#include<bits/stdc++.h>
using namespace std;
#define For(i,j,k) for(int i=(j);i<=(k);i++)
#define N 200010
int n,up[N];
bool bc[N];//branch
vector<int> e[N];
void dfs(int rt,int fa){
	if(e[rt].size()>=3) up[rt]=rt;
	else up[rt]=up[fa];
	for(int i:e[rt]) if(i!=fa) dfs(i,rt);
}
void work(){
	scanf("%d",&n);
	For(i,1,n) e[i].clear();
	int x,y;
	For(i,1,n-1){
		scanf("%d%d",&x,&y);
		e[x].push_back(y);
		e[y].push_back(x);
	}
	if(n==1){ puts("0"); return ; }
	int root=0; For(i,1,n) if(e[i].size()>=3) root=i;
	if(!root){ puts("1"); return ; }
	dfs(root,0);
	fill(bc+1,bc+1+n,false);
	int ans=0;
	For(i,1,n) if(e[i].size()==1) ans++,bc[up[i]]=true;
	For(i,1,n) if(bc[i]) ans--;
	printf("%d\n",ans);
}
int main(){
	int T;scanf("%d",&T);
	while(T--)work(); 
return 0;}
```