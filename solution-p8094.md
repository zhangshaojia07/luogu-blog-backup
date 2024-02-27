# P8094 [USACO22JAN] Cow Frisbee S

### Preface

为啥有人要用 $O(n\log n)$ 树状数组啊（我不理解）。

### Analysis

单调栈维护值递减的位置集合。加入新值时向前 pop 的同时更新答案（飞盘右边的奶牛为当前奶牛的方案数）。

时间 $O(n)$。

由此得到推论：本题能飞飞盘的奶牛对数量不会超过 $n$。

### Code

```cpp
//Said no more counting dollars. We'll be counting stars.
#include<bits/stdc++.h>
using namespace std;
#define IOS ios::sync_with_stdio(0);cin.tie(0);cout.tie(0)
#define For(i,j,k) for(int i=j;i<=k;i++)

long long n,a[300010],ans=0;
stack<long long> s;
signed main(){IOS;
	cin>>n;
	For(i,1,n) cin>>a[i];
	For(i,1,n){
		while(!s.empty() && a[s.top()]<a[i]) ans+=i-s.top()+1,s.pop();
		if(!s.empty()) ans+=i-s.top()+1;
		s.push(i);
	}
	cout<<ans<<endl;
return 0;}
```