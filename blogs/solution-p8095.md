# P8095 [USACO22JAN] Cereal 2 S

### Preface

我和许多同学赛后一直讨论这道题，有的说网络流，有的说拓扑，~~有的说不可做~~，而我用了应该是最简单的思路，所以有底气来发题解~~主要是我比较闲~~。

这篇题解写在官方题解出来之前，所以如果官方用了非常巧妙的算法，~~那么请各位抽烟~~那么算我输。

### Analysis

建图：$m$ 个点代表麦片，$n$ 条有向边（代码实现时记得存反向边），$x\to y$ 表示有一个奶牛的第一选择是 $y$，第二选择是 $x$（不要搞反了）。

**请永远记住：边是奶牛，点是麦片，$n$ 是边数，$m$ 是点数。**

当然，由于边要存较多的信息（奶牛的编号，正反方向等），请使用前向星。（我之前写的是 vector，$176$ 行的代码重写 qwq）

算法开始。

我们将每一个连通块（不是强连通分量）分别考虑。现在考察连通块 $A$。计算点数和边数 $V,E$。

#### 情况（一）$E=V-1$

必然是棵树（忽略定向）。$A$ 中绝对没有挨饿的奶牛。

构造（即证明）：

由于 $A$ 是一个 DAG（有向无环图），必然有无入度的点，DFS 选取奶牛即可。

![](https://cdn.luogu.com.cn/upload/image_hosting/95st68ie.png)

#### 情况（二）$E\ge V$

必然有一个基环树（环可能是重边）的子图（忽略定向）。$A$ 中挨饿的奶牛数 $=E-V$（即不在基环树内的边数）。

证明：

由于麦片一共就 $V$ 包，所以挨饿的奶牛数 $\ge E-V$。

接下来 基环树子图上每条边都有对应点 的构造 即证明了挨饿的奶牛数 $\le E-V$，即原命题得证。

构造：

任取一基环树子图 $B$。设基环为 $C$，长度为 $len$。选取任意基环上一条边，在基环上沿着这条边的方向依次满足奶牛。这样基环上的边都有对应点了，且都是基环上的点。

![](https://cdn.luogu.com.cn/upload/image_hosting/lh6wa9pa.png)

然后我们将每个基环上的点为根的子树分别 DFS，类似情况（一）求解。

![](https://cdn.luogu.com.cn/upload/image_hosting/db06xngx.png)

算法结束。

总时间 $O(n+m)$，而常数较大。

### Code

[不带注释 2.27KB 代码提交](https://www.luogu.com.cn/record/68578612)

注释版：

```cpp
#include<bits/stdc++.h>
using namespace std;
#define IOS ios::sync_with_stdio(0);cin.tie(0);cout.tie(0)
#define fir first
#define sec second
#define mkp make_pair
#define pb emplace_back
#define For(i,j,k) for(int i=j;i<=k;i++)
#define Fe(x,y) for(int x=h[y];x;x=e[x].nxt) //for:edge 跟铁没关系 qwq 
#define N 100100
struct edge{
	int to,nxt,id;
	bool fw;//is it forward?
}e[2*N];//正反都要 
int n,m,tot=1,h[N],sum,f[N];//tot:从 1 开始，方便找反向边 h:前向星的头 sum:该联通块的边数 f[i]:搜索树中 i 的父亲 
bool vis[N],out[N],ban[N],ins[N];//vis[i]:i 所在连通块是否被算过 out[i]:边（奶牛）是否被输出了 ban[i]:点 i DFS 是否被达咩了 ins[i]:是否在搜索树中的祖先集合 
vector<int> v,con,cy;//v:待输出的方案 con:当前连通块 cy:基环上依次的点
pair<int,int> key;//环的两端（环上相邻） 
inline void print(int x){v.pb(x);out[x]=1;}
inline void adde(int x,int y,int id,bool z){e[++tot]={y,h[x],id,z};h[x]=tot;}//加边 
void see(int rt){//查看整个连通块 
	vis[rt]=1;con.pb(rt);
	Fe(i,rt){
		sum++;
		if(!vis[e[i].to]) see(e[i].to);
	}
}
void tree(int rt,int fa){//DFS tree 
	Fe(i,rt){
		int to=e[i].to;
		if(to==fa) continue;
		print(e[i].id);
		tree(to,rt);
	}
}
void solve1(int x){//树（边有向） 
	int root=-1;bool flag;
	for(int i:con){//找无入度的点 
		flag=1;
		Fe(j,i)if(!e[j].fw){flag=0;break;}
		if(flag){root=i;break;}
	}
	tree(root,0);
}
bool dfs(int x,int fr,int fa){//找到一个环（可能是重边，但不能是正向边和对应的反向边） 
	ins[x]=1;f[x]=fa;
	Fe(i,x){
		if((i^fr)==1) continue;//fa来的反向边，达咩 
		if(ins[e[i].to]){
			key=mkp(x,e[i].to);
			return true;
		}
		if(dfs(e[i].to,i,x)) return true;
	}
	ins[x]=0;return false;
}
void subt(int rt){//基环树的子树求解 
	Fe(i,rt){
		if(ban[e[i].to]) continue;
		print(e[i].id);
		ban[e[i].to]=1;
		subt(e[i].to);
	}
}
inline bool fang(int x,int y){Fe(i,x)if(e[i].to==y)return e[i].fw;}//x=>y 的方向 
inline int cow(int x,int y){Fe(i,x)if(e[i].to==y && !out[e[i].id])return e[i].id;}//x=>y 的奶牛编号 
void solve2(int x){//最优秀为基环树（基环可能是重边） 
	cy.clear();
	dfs(x,0,0);//True 
	int tmp=key.fir;
	while(true){
		cy.pb(tmp);
		if(tmp==key.sec) break;
		tmp=f[tmp]; 
	}
	int len=cy.size();
	if(!fang(cy[0],cy[1])){//翻转环（镜像） 
		key=mkp(cy[0],cy[1]);
		For(i,2,len-1) cy[i-2]=cy[i];
		cy[len-2]=key.fir;
		cy[len-1]=key.sec;
		reverse(cy.begin(),cy.end());
	}
	For(i,0,len-1) print(cow(cy[i],cy[(i+1)%len])); //环上解决 
	for(int i:cy) ban[i]=1;
	for(int i:cy) subt(i);//子树解决 
}
signed main(){IOS;
	cin>>n>>m;
	int x,y;
	For(i,1,n){
		cin>>x>>y;
		adde(y,x,i,1);//正向边 (sec->fir)
		adde(x,y,i,0);//反向边 (fir->sec)
	}
	For(i,1,m){
		if(vis[i]) continue;
		sum=0;con.clear();
		see(i);
		sum>>=1;//双向边数量折半 
		if((int)con.size()==sum+1) solve1(i);
		else solve2(i);
	}
	int ans=0;
	For(i,1,n) if(!out[i]) ans++;//饥饿的 
	cout<<ans<<endl;
	for(int i:v) cout<<i<<endl;//输出有的吃了 
	For(i,1,n) if(!out[i]) cout<<i<<endl;//输出挨饿的 
return 0;}
```