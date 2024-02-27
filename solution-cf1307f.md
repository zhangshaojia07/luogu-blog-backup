# CF1307F题解

~~良心题解，管理员求过。~~

有任何关于此篇题解的问题皆可私信[我](https://www.luogu.com.cn/user/101868)。

### 题意简述

简述不了 qwq。

### 题目分析

假设你是 Bessie，来脑中模拟一下度假过程：

1. 出发！

2. 向终点跑去。

3. 若跑着跑着饿了，找驿站吃饭（可以绕路）。

4. 步骤 2 和 3 重复。

5. 到达终点。

发现步骤 4 中 Bessie 不断从驿站到驿站。

我们马上想到**给所有驿站建立并查集**

两个驿站若**在树上有长度不大于 $m$（题中的 $k$）的路径**（此操作可从每个驿站半径为 $\frac{m}{2}$ 扩散用 BFS 在 $O(n)$ 内完成），则放入同一个并查集内。

此时同一个并查集内的驿站 **Bessie 要么都能到达，要么都不能到达**。

这就可以判断了：

- 若 $dis(x,y)\leq m$ 不用驿站直接到达。

- 设 $ans_1$ 和 $ans_2$ 分别表示 $x$ 和 $y$ 分别在 $path_{x,y}$ 上前进 $\frac{m}{2}$ 步后的点。若 $ans_1$ 和 $ans_2$ 两者皆在某驿站的扩散范围内，且两驿站在同一并查集内，则可通过驿站到达。

- Otherwise 不行（达咩）

### Details

若 $m$ 为奇数，则所有有关 $\frac{m}{2}$ 的代码部分将变得非常棘手。

此时只要双倍开点（原每条线段中间新增一个点），此时新图的问题中 $m$ 变成了 $2m$，必然是偶数，解决！

### ACcode
```
#include<bits/stdc++.h>
using namespace std;
#define ll long long
#define pb push_back
#define FOR(i,j,k) for(register int i=j;(j<k)?(i<=k):(i>=k);i+=(j<k)?1:(-1))
#define IO ios::sync_with_stdio(0),cin.tie(0),cout.tie(0)
#define N 233333

int n,m,k;
int f[2*N][21];//倍增father
int dep[2*N];//deep
int grf[2*N];//并查集的father
int vis[2*N];//bfs标记 （到驿站的dis）
vector<int> e[2*N];//边 
int gf(int x) {//路径压缩求祖先 
	if(grf[x]==x)return x;
	return grf[x]=gf(grf[x]);
}
int move(int x,int val) {//将x向祖先move val步后的id 
	FOR(i,20,0) {
		if((1<<i)<=val) {
			val-=(1<<i);
			x=f[x][i];
		}
	}
	return x;
}
int lca(int x,int y) {
	int xx,yy;
	if(dep[x]<dep[y])swap(x,y);
	x=move(x,dep[x]-dep[y]);
	if(x==y)return x;
	FOR(i,20,0) {
		xx=f[x][i];
		yy=f[y][i];
		if(xx!=yy) {
			x=xx;
			y=yy;
		}
	}
	return f[x][0];
}
void dfs(int rt,int fa) {
	dep[rt]=dep[fa]+1;
	f[rt][0]=fa;
	FOR(i,1,20)f[rt][i]=f[f[rt][i-1]][i-1];
	for(int i:e[rt]) {
		if(i==fa)continue;
		dfs(i,rt);
	}
}
signed main() {
	IO;
	cin>>n>>m>>k;
	int x,y,z;
	FOR(i,1,n-1) {
		cin>>x>>y;//为判断奇偶方便虚拟点n+i 
		e[x].pb(n+i);//push_back 
		e[y].pb(n+i);
		e[n+i].pb(x);
		e[n+i].pb(y);
	}
	dfs(1,0);
	FOR(i,1,n*2)grf[i]=i;//init 
	queue<int> q;
	memset(vis,-1,sizeof vis);
	FOR(i,1,k) {
		cin>>x;
		q.push(x);
		vis[x]=0; 
	}
	while(!q.empty()) {//广搜 半径m 
		int tmp=q.front();
		q.pop();
		if(vis[tmp]==m)break;
		for(int i:e[tmp]) {
			grf[gf(i)]=gf(tmp);//并查集合并（多个驿站联合 商帮） 
			if(vis[i]==-1) {
				vis[i]=vis[tmp]+1;
				q.push(i);
			}
		}
	}
	int qu,ans1,ans2;
	cin>>qu; 
	FOR(i,1,qu) {
		cin>>x>>y;
		z=lca(x,y);
		int len=dep[x]+dep[y]-2*dep[z];//dis计算 
		if(len<=2*m)cout<<"YES"<<endl;//直接走 
		else {//抽屉原理：一定有一边从出发点至LCA距离>=m 
			ans1=(m<=dep[x]-dep[z])?gf(move(x,m)):gf(move(y,len-m));//x向y走m步 
			ans2=(m<=dep[y]-dep[z])?gf(move(y,m)):gf(move(x,len-m));//y向x走m步 
			cout<<(ans1==ans2?"YES":"NO")<<endl;//康康是否在一个并查集里 
		}
	}
	return 0;
}

```

~~看完了，点个赞再走吧。~~