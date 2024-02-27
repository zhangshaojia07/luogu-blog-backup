# CF1674F Desktop Rearrangement

### Preface

「看起来恐怖」的题。

### Analysis

若将桌面按**从左到右，从上到下**的顺序标号，则最终被放图标的位置一定是**前缀**.

所以动态维护**图标个数**和**不在此前缀的图标个数**即可，答案为后者。

还是说具体一点吧……

若当前新加入一个图标，位置为 $y$，当前归位前缀为 $[1,x]$，当前答案为 $ans$。

步骤依次进行：

1. 若 $y>x$，则 $ans$ 加 $1$。

2. $x$ 加 $1$。

3. 若 $x$ 位置有图标，则 $ans$ 减 $1$。

删掉一个图标与之类似；

1. 若 $y>x$，则 $ans$ 减 $1$。

1. 若 $x$ 位置有图标，则 $ans$ 加 $1$。

2. $x$ 减 $1$。

### Code

```cpp
//Said no more counting dollars. We'll be counting stars.
#pragma GCC optimize("Ofast")
#include<bits/stdc++.h>
using namespace std;
#define IOS ios::sync_with_stdio(0);cin.tie(0);cout.tie(0)
#define For(i,j,k) for(int i=j;i<=k;i++)
#define Rof(i,j,k) for(int i=j;i>=k;i--)
#define N 1002
int n,m,q,tot=0,a[N*N],ans=0;
char ch;
inline int num(int x,int y){return (y-1)*n+x;}
signed main(){IOS;
	cin>>n>>m>>q;
	For(i,1,n){
		For(j,1,m){
			cin>>ch;
			a[num(i,j)]=(ch=='*');
			tot+=a[num(i,j)];
		}
	}
	For(i,tot+1,n*m) ans+=a[i];
	int x,y,z;
	while(q--){
		cin>>x>>y;
		z=num(x,y);
		a[z]^=1;
		if(a[z]){//add
			if(z>tot) ans++;
			tot++;
			if(a[tot]) ans--;
		}else{//del
			if(z>tot) ans--;
			if(a[tot]) ans++;
			tot--;
		}
		cout<<ans<<endl;
	}
return 0;}
```