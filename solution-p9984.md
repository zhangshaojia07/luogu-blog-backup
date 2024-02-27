# P9984 [USACO23DEC] A Graph Problem P （并查集维护tag）

按边的出现顺序建 Kruskal 重构树，一条边连接两个连通块对一个点的答案会发生：先走完其连通块内的所有边，再走这条边，再以这条边的端点为起点走另一个连通块。

也就是这个点更新后的答案为原先答案、这条边、对面端点答案三者顺次拼接。

发现把 Kruskal 树平摊后就是线段树维护区间加乘。

复杂度 $O(n\log n)$，赛时写的这个。

但是我们可以更简单一点，Kruskal 树构建的时候并查集我们带上权值，代表加乘 tag，操作顺序从底向上，此信息支持路径压缩。

这样在一条边连接两个连通块时先得到两个端点的答案，再用这两个值求出并查集两条边的边权即可。

这里能用并查集的原因是打 tag 都是对 Kruskal 树的整棵子树打 tag（所以平摊到序列强化了问题），而且顺序是由叶子向根（平摊到序列则被包含的区间 tag 时刻一定在包含的之前）。

并查集复杂度。

```cpp
using Z=mod_int<P>;
const int N=200010;
int f[N<<1],sz[N<<1],tot,n,m;
Z pw[N];
struct node{
	Z mul,ad;
	node operator+(const node&v)const{ return node{mul*v.mul,ad*v.mul+v.ad}; }
}g[N<<1];
void gf(int x){
	if(x==f[x]) return ;
	gf(f[x]);
	g[x]=g[x]+g[f[x]];
	f[x]=f[f[x]];
}
signed main(){ios::sync_with_stdio(false),cin.tie(nullptr);
    cin>>n>>m;
    tot=n;
    pw[0]=1;
    rep(i,1,n) pw[i]=pw[i-1]*10;
    iota(f+1,f+2*n,1);
    fill(g+1,g+2*n,node{1,0});
    rep(i,1,m){
    	int x,y;
    	cin>>x>>y;
    	gf(x),gf(y);
    	if(f[x]!=f[y]){
    		node xx=g[x],yy=g[y],zz={10,i};
    		g[f[x]]=zz+yy;
    		g[f[y]]=zz+xx;
    		f[f[x]]=f[f[y]]=++tot;
    	}
    }
    rep(i,1,n){
    	gf(i);
    	cout<<g[i].ad<<"\n";
    }
return 0;}
```