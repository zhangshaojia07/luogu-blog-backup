# CF1278E Tests for problem D

### Preface

自认为这是目前最短代码。

### Analysis

以 $1$ 为根，DFS 整棵树。每个节点如图构造，每个子树递归下去。由于下一个子树的起始位置由上一个子树的大小决定，我们 DFS 时传回结束位置即可。

![](https://cdn.luogu.com.cn/upload/image_hosting/f62cuqs9.png)

（图中虚线框表示子树递归）

时间 $O(n)$

### Code

[Link](https://codeforces.com/contest/1278/submission/150411285)

```cpp
//Said no more counting dollars. We'll be counting stars.
#pragma GCC optimize("Ofast")
#include<bits/stdc++.h>
using namespace std;
#define IOS ios::sync_with_stdio(0);cin.tie(0);cout.tie(0)
#define pb emplace_back
#define fir first
#define sec second
#define For(i,j,k) for(int i=j;i<=k;i++)
#define N 500010
#define endl '\n'
int n;
pair<int,int> a[N];
vector<int> e[N];
int dfs(int rt,int fa,int le){
	a[rt].sec=le+e[rt].size();//e.size=cnt_son+1
	int L=a[rt].sec,R=a[rt].sec;
	for(int i:e[rt]){
		if(i==fa) continue;
		a[i].fir=--L;
		R=dfs(i,rt,R);
	}
	return R;
}
signed main(){IOS;
	cin>>n;
	int x,y;
	For(i,1,n-1){
		cin>>x>>y;
		e[x].pb(y);e[y].pb(x);
	}
	e[1].pb(0);
	a[1].fir=1;
	dfs(1,0,1);
	For(i,1,n) cout<<a[i].fir<<" "<<a[i].sec<<endl;
return 0;}
```