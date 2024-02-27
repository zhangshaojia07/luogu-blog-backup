# P8271 [USACO22OPEN] COW Operations S

### Analysis

居然是结论题……

------------

**结论：** 设字符串中 `C`、`O`、`W` 的个数分别为 $c,o,w$。这个字符串能变成 `C` 当且仅当

$$\begin{aligned}
2&\ |\ o+w
\\
2&\nmid c+o
\end{aligned}$$

**引理 A：** 任意 $2$ 个相邻的不同字符总是可以变换成 $1$ 个，且必然是这两种以外的另一种字符。

正确性显然，故不证。

**证明：** 

**必要性：**

我们先将相邻的 `W` 消掉，再用引理 A 将所有 `W` 消掉。这样 $c+o$ 仍然为奇数，且 $o$ 为偶数，$c$ 为奇数。再把相邻的 `C`、`O` 消掉，最后一定是 `COCOC` 或 `OCOCOCO` 类型之一，显然都可以化成 `C`。

**充分性：**

若 $2\nmid o+w$，则不论怎样 $o+w$ 一定是奇数，不可能变化成单个 `C`。（`O` 和 `W` 至少留一个)

若 $2\ |\ c+o$。则不论怎样 $o+c$ 一定是偶数，一定不可能变化成单个 `C`。（若存在奇数个 `C`，则一定存在 `O`)

$\blacksquare$

------------

所以我们处理一下前缀和就可以线性解决。

### Code

```cpp
//Said no more counting dollars. We'll be counting stars.
#include<bits/stdc++.h>
using namespace std;
#define IOS ios::sync_with_stdio(0);cin.tie(0);cout.tie(0)
#define For(i,j,k) for(int i=j;i<=k;i++)
#define Rof(i,j,k) for(int i=j;i>=k;i--)
#define N 200010

string s;
int a[N][3],n,q;
signed main(){IOS;
	cin>>s;
	n=s.size();
	For(i,1,n){
		For(j,0,2) a[i][j]=a[i-1][j];
		if(s[i-1]=='C') a[i][0]++;
		if(s[i-1]=='O') a[i][1]++;
		if(s[i-1]=='W') a[i][2]++;
	}
	int x,y;
	cin>>q;
	while(q--){
		cin>>x>>y;x--;
		cout<<((((a[y][0]-a[x][0]+a[y][1]+a[x][1])&1) && !((a[y][2]-a[x][2]+a[y][1]+a[x][1])&1))?'Y':'N');
	}
return 0;}
```