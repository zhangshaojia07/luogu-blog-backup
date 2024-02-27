# P9066 [yLOI2023] 腐草为萤（模拟/优先队列）

「妈妈，我将变成一只萤火虫。」

就是模拟 + 优化。

可以算出每个萤火虫初始移动的方向，这样她的位置可以用关于时间的一次函数表示。

同样也可以处理出若之后没有萤火虫消失，每个萤火虫将要被吞并的时间。

按时间入堆，堆顶的萤火虫被吞并的时间就是我们计算的时间。

为了优化，若一个萤火虫动的方向上下一个萤火虫和她方向相同，则可以先不入堆，因为下个萤火虫必然消失得比她早。

通过一次函数计算出被吞并的坐标，接下来就是改动别的萤火虫。

用链表维护活着的虫，设局部依次为 $LL,L,x,R,RR$。当 $x$ 消失后，$L,R$ 的一次函数和时间有可能改，$LL,RR$ 的时间可能会改。

细节较多，注意修改的时候排除时间相同的萤火虫，因为她们是同一时间消失的。

优先队列应该比 set 快。

```cpp
// Problem: Luogu P9066 [yLOI2023] 腐草为萤
// Url: https://www.luogu.com.cn/problem/P9066
// T/M Limit: 2000ms 512MB
// Time: 2023-02-14 13:16:10
#include<bits/stdc++.h>
using namespace std;
#define rep(i,j,k) for(int i=(j),i##_=(k);i<=i##_;i++)
#define per(i,j,k) for(int i=(j),i##_=(k);i>=i##_;i--)
#define int long long
const int N=500010,inf=1e18;
int n,pos[N],w[N],L[N],R[N],dir[N],ans[N],t[N],st[N];
int a[N];
inline int way(int x,int y,int z){ return (y>x && y>z)?0:(z>x?1:-1); }
inline void get(int x){
	int y;
	if(!dir[x]) t[x]=inf;
	else if(dir[x]==1){
		y=R[x];
		if(!dir[y]){
			t[x]=pos[y]-pos[x];
		}else if(dir[y]==1){
			t[x]=inf;
		}else assert(0);
	}else{
		y=L[x];
		if(!dir[y]){
			t[x]=pos[x]-pos[y];
		}else if(dir[y]==-1){
			t[x]=inf;
		}else assert(0);
	}
}
struct node{
	int id,t,st;
	friend bool operator<(const node&x,const node&y){ return x.t>y.t; }
};
priority_queue<node> q;
inline int p(int x,int tm){ return pos[x]+dir[x]*tm; }
inline void ins(int x){ q.push({x,t[x],++st[x]}); }
void work(int x,int tm){
	pos[x]=p(x,tm);
	dir[x]=way(w[L[x]],w[x],w[R[x]]);
	pos[x]-=dir[x]*tm;
	get(x);
	ins(x);
}
signed main(){ios::sync_with_stdio(false),cin.tie(nullptr);
	cin>>n;
	rep(i,1,n) cin>>pos[i];
	rep(i,1,n) cin>>w[i];
	rep(i,1,n) dir[i]=way(w[i-1],w[i],w[i+1]);
	rep(i,2,n) L[i]=i-1;
	rep(i,1,n-1) R[i]=i+1;
	iota(a+1,a+1+n,1);
	sort(a+1,a+1+n,[](int x,int y){return w[x]>w[y];});
	rep(i,1,n) get(a[i]);
	rep(i,1,n) ins(i);
	int x,y,z,tm;
	while(1){
		x=q.top().id; tm=q.top().t; z=q.top().st; q.pop();
		if(st[x]!=z) continue;
		if(!dir[x]) break;
		ans[x]=p(x,tm);
		y=L[x],z=R[x];
		R[y]=z,L[z]=y;
		//y,z 改 dir & t , L[y] 和 R[z] 改 t
		if(y && t[y]>tm) work(y,tm);
		if(z && t[z]>tm) work(z,tm);
		if(L[y] && t[L[y]]>tm) get(L[y]),ins(L[y]);
		if(R[z] && t[R[z]]>tm) get(R[z]),ins(R[z]);
	}
	rep(i,1,n) cout<<ans[i]<<" \n"[i==n];
return 0;}
```