# CF1609D Social Network 题解

### Analysis

以下连通块的大小均指连通块中点的个数。

每次求一个点的最大可能度数等价于**求最大连通块的最大可能点数（再减 $1$）**。

由于 $n$ 个点的连通无环图有且仅有 $n-1$ 条边，所以对于每一个给出条件的前缀，不用考虑加边的顺序。

所以思路如下：

顺序遍历给出的条件（$x$ 和 $y$ 必须连通），如果 $x$ 和 $y$ 已经连通，则将 $cnt$ 加 $1$，代表现在多出一条空闲的边，否则只能将 $x$ 和 $y$ 连边。

对于每次答案的计算，遍历每一个连通块，取前 $cnt+1$ 大的，用那 $cnt$ 条空闲的边将这些连通块连通，答案为这整个连通块的大小 $-1$。

### Detail

取前 $cnt+1$ 大的连通块时用堆维护。

计算答案时不必将连通块真的连起来。

### Code

复杂度 $O(nm\log n)$，实际上跑不满。

```cpp
#define N 1010
int n,m,f[N],sz[N],cnt=0;
bool vis[N];
priority_queue<int,vector<int>,greater<int> > q;
inline int gf(int x){
	return f[x]==x?x:f[x]=gf(f[x]);
}
signed main(){
	IOS;
	cin>>n>>m;
	For(i,1,n) f[i]=i;
	For(i,1,n) sz[i]=1;
	int x,y;
	For(i,1,m){
		cin>>x>>y;
		if(gf(x)!=gf(y)){
			sz[gf(y)]+=sz[gf(x)];
			f[gf(x)]=gf(y);
		}else{
			cnt++;
		}
		For(j,1,n) vis[j]=0;
		For(j,1,n){
			if(vis[gf(j)]) continue;
			vis[gf(j)]=1;
			q.push(sz[gf(j)]);
			if(q.size()>cnt+1) q.pop();
		}
		int ans=0;
		while(!q.empty()){
			ans+=q.top();
			q.pop();
		}
		cout<<ans-1<<endl;
	}
    return 0;
}
```