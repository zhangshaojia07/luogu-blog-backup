# P8252 [NOI Online 2022 提高组] 讨论

### Preface

这题简单啊，考场上一堆人放弃……

### Analysis

为简便，设两集合 $A\Box B$ 表示满足

$$A\cap B\ne\varnothing\ \land\ A\nsubseteq B\ \land\ B\nsubseteq A$$

这些条件，即**交叉且不包含**。

------------

**请一定认真仔细阅读此结论，因为这是本题解的核心。**

#### 结论 A：

$$S_x\Box S_y\ \land\ S_x\subseteq S_z\ \land\ |S_z|\le|S_y|\implies S_z\Box S_y$$

#### 证明：

$$\begin{aligned}
S_x\cap S_y\ne\varnothing\ \land\ S_x\subseteq S_z &\implies S_z\cap S_y\ne\varnothing
\\
S_x\nsubseteq S_y\ \land\ S_x\subseteq S_z &\implies S_z\nsubseteq S_y
\\
|S_z|\le|S_y|\ \land\ S_x\nsubseteq S_y\ \land\ S_x\subseteq S_z &\implies S_y\nsubseteq S_z
\end{aligned}$$

------------

由此，我们可以将 $S_1,S_2,\dots,S_n$ **排序**，使得 $|S_1|\le|S_2|\le|S_3|\le\dots\le|S_n|$（**注意**最后输出方案时要输出排序前的编号）。

我们遍历第 $x=1\to n$ 个人。

我们维护数组 $a_i$ 表示第 $i$ 道题目现在第 $a_i$ 个人会做（$i\in S_{a_i}$）。特别地，$a_i=0$ 表示第 $i$ 道题目目前没有人会做。

我们**开桶统计** $a_i\ |\ i\in S_x$。若 $\exists y<x\ |\ S_x\Box S_y$，则 $y$ 在桶中出现的次数一定 $>0$ 且 $<|S_y|$（因为与 $S_x$ 交叉且不包含）。存在 $y$ 的话直接输出 $(x,y)$ 作为答案即可。否则把所有 $i\in S_x$，$a_i$ **都赋值**为 $x$（由结论 A，**这些被 $S_x$ 包含的集合就无用了**）。

上文「无用了」的含义是：设某个被 $S_x$ 包含的集合为 $S_c$。**若之后存在**集合 $S_k$ 使得 $S_k\Box S_c$，则有 $S_k\Box S_x$，即 $(x,k)$ **也为答案**，所以 $S_c$ **变得无用**。

时间 $O(n\log n+m)$

### Code

```cpp
//Said no more counting dollars. We'll be counting stars.
#include<bits/stdc++.h>
using namespace std;
#define IOS ios::sync_with_stdio(0);cin.tie(0);cout.tie(0)
#define pb emplace_back
#define For(i,j,k) for(int i=j;i<=k;i++)
#define N 1000010
int n,sz[N],p[N],a[N],t[N];
vector<int> e[N];
inline bool cmp(int x,int y){return sz[x]<sz[y];}
void work(){
	cin>>n;
	For(i,1,n) e[i].clear();
	For(i,1,n) a[i]=t[i]=0;
	int x;
	For(i,1,n){
		cin>>sz[i];
		For(j,1,sz[i]) cin>>x,e[i].pb(x);
	}
	For(i,1,n) p[i]=i;
	sort(p+1,p+1+n,cmp);//sort S_1~n,but just sort id 
	For(i,1,n){
		x=p[i];
		for(int j:e[x]) if(a[j]) t[a[j]]++;
		for(int j:e[x]) if(t[a[j]]){
			if(t[a[j]]!=sz[a[j]]){cout<<"YES\n"<<x<<" "<<a[j]<<"\n";return ;}
			t[a[j]]=0;
		}
		for(int j:e[x]) a[j]=x;
	}
	cout<<"NO\n";
}
signed main(){IOS;int T;cin>>T;while(T--)work();return 0;}
```