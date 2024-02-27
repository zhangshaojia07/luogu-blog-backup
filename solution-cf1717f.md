# CF1717F Madoka and The First Session

深感 wxw 爆叉 $33$ 人的强大，以及数据人和题解人的行为过于摆烂。

耗在这道题上一天了捏。

## 分析

先将每一条边的两端均 $-1$，则接下来我们要对于每一条边选一个端点 $+2$。

为方便，不妨设 $b_i=\dfrac{a_i+deg_i}{2}$。

相当于每个点现在是 $-b_i$，对于每一条边选一个端点 $+1$，问是否存在全部清零的方案。

先排掉三类非法情况：

* 存在 $b_i$ 为负数。
* 存在 $b_i$ 不为整数。
* $b_i$ 之和 $>m$。

## 建图

网络流，将每条边也建新点（算进源汇共 $n+m+2$ 个点）。

以下方括号内的两个数字代表流量上下界。

对于每一条边 $x:(u,v)$（$x$ 为边所对应的点标号），建边
$$
S\to x[1,1]\quad x\to u[0,1]\quad x\to v[0,1]
$$
对于每一个 $s=1$ 的点 $x$

$$
x\to T[b_x,b_x]
$$

对于每一个 $s=0$ 的点 $x$

$$
x\to T[0,\infty]
$$

跑上下界可行流即可。

~~先上代码，时间复杂度先别急。~~

## 如何建图及时间复杂度

我们建这些边（$E$ 为 $m$ 条边的集合，$S',T'$ 为原图的源汇，$S,T$ 为上下界建图的源汇）：
$$
\begin{array}{rll|l}
S&\to e&:1&e\in E
\\
S'&\to T&:1&
\\
S&\to T'&:\sum_i b_i&
\\
(u,v) &\to u&:1& (u,v)\in E
\\
(u,v) &\to v&:1&
\\
x &\to T&:b_x&s_x=1
\\
x &\to T'&:\color{red}\infty&s_x=0
\\
T' &\to S'&:\color{blue}\infty
\end{array}
$$
发现一个点 $x$ 的入流量不超过 $deg_x$，所以 ${\color{red}\infty}\Rightarrow deg_x$。

发现原图的总流量最大不超过 $m$，所以 ${\color{blue}\infty}\Rightarrow m$。

跑 Dinic，由于最多增广 $n-1$ 次，而且每次增广时间不会超过总容量 $O(n+m)$，所以复杂度为 $O(n(n+m))$。

## 代码

```cpp
/*
* Author: ShaoJia
* Last Modified time: 2022-09-03 20:47:59
* Motto: We'll be counting stars.
*/
#pragma GCC optimize("Ofast")
#include<bits/stdc++.h>
using namespace std;
#define fir first
#define sec second
#define mkp make_pair
#define pb emplace_back
#define For(i,j,k) for(int i=(j),i##_=(k);i<=i##_;i++)
#define Rof(i,j,k) for(int i=(j),i##_=(k);i>=i##_;i--)
#define ckmx(a,b) a=max(a,b)
#define ckmn(a,b) a=min(a,b)
#define debug(...) cerr<<"#"<<__LINE__<<": "<<__VA_ARGS__<<endl
const int N=10010,M=40010;
int n,m,sum=0,a[N],deg[N],p[N],L[N],R[N];
bool s[N];
struct edge{
	int nxt,to,flow;
}e[M<<1];
int mf=0,dis[N<<1],cur[N<<1],head[N<<1],S0,T0,S,T,tot=1,v[N],ans[N];
inline void adde(int x,int y,int z){
	// cout<<x<<" "<<y<<":"<<z<<endl;
	e[++tot]=(edge){head[x],y,z}; head[x]=tot;
	e[++tot]=(edge){head[y],x,0}; head[y]=tot;
}
queue<int> q;
bool bfs(){
	fill(dis+1,dis+1+T,0);
	dis[S]=1;
	q.push(S);
	int x;
	while(!q.empty()){
		x=q.front();
		q.pop();
		cur[x]=head[x];
		for(int i=head[x],to;i;i=e[i].nxt){
			to=e[i].to;
			if(!e[i].flow) continue;
			if(!dis[to]){
				dis[to]=dis[x]+1;
				q.push(to);
			}
		}
	}
	return dis[T];
}
int dfs(int x,int flow){
	if(x==T) return flow;
	int res=0;
	for(int &i=cur[x],to,tmp;i;i=e[i].nxt){
		to=e[i].to;
		if(!e[i].flow || dis[to]!=dis[x]+1) continue;
		tmp=dfs(to,min(flow,e[i].flow));
		flow-=tmp;
		res+=tmp;
		e[i].flow-=tmp;
		e[i^1].flow+=tmp;
		if(!flow) break;
	}
	return res;
}
void Dinic(){
	while(bfs()) mf+=dfs(S,m+sum);
}
signed main(){ios::sync_with_stdio(false),cin.tie(nullptr);
	cin>>n>>m;
	S0=n+m+1; T0=S0+1; S=T0+1; T=S+1;
	For(i,1,n) cin>>s[i];
	int x,y;
	For(i,1,n) cin>>a[i];
	For(i,1,m){
		cin>>x>>y;
		deg[x]++,deg[y]++;
		L[i]=x,R[i]=y;
	}
	For(i,1,n){
		if(s[i]){
			a[i]+=deg[i];
			if(a[i]<0 || a[i]&1) return cout<<"NO"<<endl,0;
			a[i]>>=1;
		}else a[i]=0;
	}
	// debug("?");
	For(i,1,n) sum+=a[i];
	if(sum>m) return cout<<"NO"<<endl,0;
	For(i,1,m){
		adde(S,i+n,1);
		p[i]=tot+1;
		adde(i+n,L[i],1);
		adde(i+n,R[i],1);
	}	
	adde(S0,T,m);
	adde(S,T0,sum);
	adde(T0,S0,m);
	For(i,1,n)
		if(s[i]) adde(i,T,a[i]);
		else adde(i,T0,deg[i]);
	Dinic();
	if(mf!=m+sum) return cout<<"NO"<<endl,0;
	cout<<"YES"<<endl;
	For(i,1,m){
		if(e[p[i]].flow) cout<<L[i]<<" "<<R[i]<<endl;
		else cout<<R[i]<<" "<<L[i]<<endl;
	}
return 0;}
```