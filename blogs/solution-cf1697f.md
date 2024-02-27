# CF1697F Too Many Constraints（2-sat 变形）

2-sat 好题。

首先我们每一个 bool 变量表示「$a_i$ 是否 $\ge j$」，由于必然 $a_i\ge 1$，所以我们的变量数为 $n(k-1)$。

然后我们根据题目条件来建有向边，注意 2-sat 要满足**对称性**，所以下文中每一个条件的**逆否命题都要建边**：

* $a_i\le a_{i+1}$，等价于「对于所有的 $v$，若 $a_i\ge v$，则 $a_{i+1}\ge v$」。

* $a_i\ne x$，等价于「若 $a_i\ge x$，则 $a_i\ge x+1$」。

* $a_i+a_j\le x$，等价于「对于所有的 $v$，若 $a_i\ge v$，则 $\neg(a_j\ge x-v+1)$」。

* $a_i+a_j\ge x$，等价于「对于所有的 $v$，若 $\neg(a_i\ge v)$，则 $a_j\ge x-v+1$」。

当然还有一个最浅显的别忘了：

* 若 $a_i\ge v$，则 $a_i\ge v-1$。

跑 Tarjan 缩点即可。

```cpp
//We'll be counting stars.
#pragma GCC optimize("Ofast")
#include<bits/stdc++.h>
using namespace std;
#define pb emplace_back
#define For(i,j,k) for(int i=(j),i##_=(k);i<=i##_;i++)
#define ckmn(a,b) a=min(a,b)
#define N 360000
vector<int> e[N];
int n,m,k,dfn[N],low[N],col[N],tim,ct,s[N],st;
inline void adde(int x,int y){e[x].pb(y);if(x!=(y^1))e[y^1].pb(x^1);} 
inline int num(int id,int x,bool y){return ((id-1)*(k-1)+(x-2))<<1|y;}//第 id 个数 >= x 的 y 状态的下标 
void build(int x){For(i,2,k-1)adde(num(x,i,0),num(x,i+1,0));}
void le(int x,int y){For(i,2,k) adde(num(x,i,1),num(y,i,1));}
void ne(int x,int val){
	if(val==1) adde(num(x,val+1,0),num(x,val+1,1));
	else if(val==k) adde(num(x,val,1),num(x,val,0));
	else adde(num(x,val,1),num(x,val+1,1));
}
void sumle(int x,int y,int val){
	For(i,2,k)
		if(i+1>val) adde(num(x,i,1),num(x,i,0)),adde(num(y,i,1),num(y,i,0));
		else if(val-i+1<=k) adde(num(x,i,1),num(y,val-i+1,0));
}
void sumge(int x,int y,int val){
	For(i,2,k)
		if(i+k<=val) adde(num(x,i,0),num(x,i,1)),adde(num(y,i,0),num(y,i,1));
		else if(val-i+1>=2) adde(num(x,i,0),num(y,val-i+1,1));
}
void dfs(int x){
	dfn[x]=low[x]=++tim;
	s[++st]=x;
	for(int i:e[x]){
		if(!dfn[i]) dfs(i),ckmn(low[x],low[i]);
		else if(!col[i]) ckmn(low[x],dfn[i]);
	}
	if(low[x]==dfn[x]){
		col[x]=++ct;
		while(s[st]!=x) col[s[st--]]=ct;
		st--;
	}
}
void work(){
	scanf("%d%d%d",&n,&m,&k);
	int lim=(k-1)*2*n;
	For(i,0,lim-1) e[i].clear();
	For(i,1,n) build(i);
	For(i,1,n-1) le(i,i+1);
	int x,y,val,opt;
	For(i,1,m){
		scanf("%d",&opt);
		if(opt==1){
			scanf("%d%d",&x,&val);
			ne(x,val);
		}else if(opt==2){
			scanf("%d%d%d",&x,&y,&val);
			sumle(x,y,val);
		}else{
			scanf("%d%d%d",&x,&y,&val);
			sumge(x,y,val);
		}
	}
	fill(dfn,dfn+lim,0);
	fill(col,col+lim,0);
	tim=ct=st=0;
	For(i,0,lim-1) if(!dfn[i]) dfs(i);
	for(int i=0;i<lim;i+=2) if(col[i]==col[i^1]){
		puts("-1");
		return ;
	}
	For(i,1,n){
		val=1;
		For(j,2,k) if(col[num(i,j,1)]<col[num(i,j,0)]) val=j;
		printf("%d ",val);
	}
	puts("");
}
int main(){
	int T;scanf("%d",&T);
	while(T--)work();
return 0;}
```