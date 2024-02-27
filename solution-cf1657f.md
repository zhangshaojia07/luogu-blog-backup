# CF1657F Words on Tree（2-sat）

~~一眼 2-sat。~~ 用 2-sat 做是因为 $x\to y,y\to x$ 只有两种选法。

我们建立 bool 变量：

* 每一个题目给的路径条件建一个 bool，代表这个字符串正 / 反放。

* 每个树上的节点至多两个 bool：每个她可能取的值。

然后我们建边（记得建这些的对称边）：

* 路径上字符串每个摆放方式连向路径上每个节点的对应字符。

* 接下来对节点的考虑屏蔽没有被路径覆盖过的点。

* 对于字母只有一种选择的点，不可能不选这个字母。

* 对于字母有两种选择的点，不选一个字母连向选另一个，选一个连向不选另一个。

然后跑 Tarjan，通过 Tarjan 的反拓扑序来确定每个节点的最终字母。

注意最后考虑没有被路径覆盖过的点，随便选字母即可。

删减了 $5$ 次还是冗长的屑代码：

```cpp
//We'll be counting stars.
#pragma GCC optimize("Ofast")
#include<bits/stdc++.h>
using namespace std;
#define fir first
#define sec second
#define mkp make_pair
#define pb emplace_back
#define For(i,j,k) for(int i=(j),i##_=(k);i<=i##_;i++)
#define ckmn(a,b) a=min(a,b)
#define N 400004
int n,q,s[N],dep[N],fa[N],tot=0,dfn[6*N],low[6*N],col[6*N],tim=0,st[6*N],stt=0,ct=0;
vector<int> e[N],g[6*N];
vector<pair<char,int> > pos[2*N];
char c[N];
inline void adde(int x,int y){ g[x].pb(y); if(x!=(y^1)) g[y^1].pb(x^1); }
void nnd(int x,char y,char z){
	if(!pos[x].empty()) return ;
	pos[x].pb(mkp(y,tot+=2));
	if(y!=z){
		pos[x].pb(mkp(z,tot+=2));
		adde(pos[x][0].sec,pos[x][1].sec^1);
		adde(pos[x][0].sec^1,pos[x][1].sec);
	}else if(y!='0'){
		adde(pos[x][0].sec,1);
	}
}
inline int num(int x,char y){ for(auto i:pos[x]) if(i.fir==y) return i.sec; return 0; }
void dfs(int rt,int fat){
	dep[rt]=dep[fat]+1; fa[rt]=fat;
	for(int i:e[rt]) if(i!=fat) dfs(i,rt);
}
void path(int x,int y,int len){
	if(dep[x]<dep[y]) swap(x,y);
	int z=0;
	while(dep[x]>dep[y]) s[z++]=x,x=fa[x];
	while(x!=y) s[--len]=y,s[z++]=x,x=fa[x],y=fa[y];
	s[z]=x;
}
void tar(int x){
	dfn[x]=low[x]=++tim;
	st[++stt]=x;
	for(int i:g[x]){
		if(!dfn[i]) tar(i),ckmn(low[x],low[i]);
		else if(!col[i]) ckmn(low[x],dfn[i]);
	}
	if(dfn[x]==low[x]){
		col[x]=++ct;
		while(st[stt]!=x) col[st[stt--]]=ct;
		stt--;
	}
}
int main(){
	scanf("%d%d",&n,&q);
	int x,y,len;
	For(i,1,n-1){
		scanf("%d%d",&x,&y);
		e[x].pb(y);
		e[y].pb(x);
	}
	dfs(1,0);
	For(i,1,q){
		scanf("%d%d%s",&x,&y,c);
		path(x,y,len=strlen(c));
		For(j,0,(len-1)>>1){
			nnd(s[j],c[j],c[len-1-j]);
			nnd(s[len-1-j],c[j],c[len-1-j]);
			nnd(n+i,'0','0');
			adde(num(n+i,'0')^1,num(s[j],c[j])^1);
			adde(num(n+i,'0'),num(s[j],c[len-1-j])^1);
			adde(num(n+i,'0')^1,num(s[len-1-j],c[len-1-j])^1);
			adde(num(n+i,'0'),num(s[len-1-j],c[j])^1);
		}
	}
	adde(1,0);
	For(i,0,tot+1) if(!dfn[i]) tar(i);
	for(int i=0;i<tot+2;i+=2) if(col[i]==col[i^1]){
		puts("NO");
		return 0;
	}
	puts("YES");
	fill(c+1,c+1+n,'a');
	For(i,1,n)
		for(auto j:pos[i])
			if(col[j.sec]>col[j.sec^1])
				c[i]=j.fir;
	c[n+1]='\0'; 
	printf("%s\n",c+1);
return 0;}
```