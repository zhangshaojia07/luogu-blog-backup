# P8251 [NOI Online 2022 提高组] 丹钓战

### Preface

倍增，简单吧。

### Analysis

我们将每个二元组看作一个元素，原序列为 $\{a_1,a_2,\dots,a_n\}$。

我们将**整个序列模拟「丹钓战」的过程**，这部分时间 $O(n)$。

发现对于某个元素 $a_x$ 来说，设其在「丹钓战」的过程中被 $a_y$ 入栈的过程中弹掉（显然 $x<y$），那**从位置 $x$ 开始（即 $l=x$）** 的「丹钓战」的过程中 $a_x$ **紧接的下一个**「成功的」的位置就是 $y$（当然，在 $y\le r$ 的前提下）。

这样我们就得到了一张**跳转表**，$i\in[1,n]$ 每个位置最多有一个往右跳的位置（或者没有）。我们**倍增预处理**她。这样，对于每一个 $(l,r)$ 的询问，我们从 $l$ **倍增跳**即可。

时间 $O(n\log n-q\log n)$

~~话说为啥有人用常数巨大的主席树啊~~

### Code

```cpp
//Said no more counting dollars. We'll be counting stars.
#include<bits/stdc++.h>
using namespace std;
#define IOS ios::sync_with_stdio(0);cin.tie(0);cout.tie(0)
#define For(i,j,k) for(int i=j;i<=k;i++)
#define Rof(i,j,k) for(int i=j;i>=k;i--)
#define N 500010
#define C 18//log n
int a[N],b[N],f[N][C+1];
int st[N],tot=0,n,q;
inline bool check(int x,int y){return a[x]==a[y] || b[x]>=b[y];}//x replace y
signed main(){IOS;
	cin>>n>>q; 
	For(i,1,n) cin>>a[i];
	For(i,1,n) cin>>b[i];
	For(i,1,n+1) f[i][0]=n+1;
	For(i,1,n){
		while(tot && check(i,st[tot]))
			f[st[tot--]][0]=i;
		st[++tot]=i;
	}
	Rof(i,n+1,1) For(j,1,C) f[i][j]=f[f[i][j-1]][j-1];
	int l,r,ans;
	For(i,1,q){
		cin>>l>>r;
		ans=1;
		Rof(j,C,0) if(f[l][j]<=r) l=f[l][j],ans+=(1<<j);
		cout<<ans<<'\n';
	}
return 0;}
```