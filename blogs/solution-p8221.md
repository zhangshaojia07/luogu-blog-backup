# P8221 [WFOI - 02] I wanna reverse to reserve（翻转）

### Preface

比 I wanna 还难（确信）。

### Analysis

判无解很简单：对称的 $4$ 个点的权值是否可以重排到目标状态。

然后就是构造。

由于目标状态一列中值都相同，我们考虑将对称的 $4$ 个位置归位而每行最后都相当于没被 reverse 过，这样我们可以按靠外列到靠中心列求解。

将这 $4$ 个值取出为 $2\times 2$ 的矩阵，设目标为

$$\begin{bmatrix}x&y\\x&y\end{bmatrix}$$

设 L0 表示 reverse 靠左的这列，H0 表示 reverse 靠上的这行，L1、R1 同理表示 reverse 右列、下行。

公式 ~~魔方乱入~~：

#### 状态 1（对侧）

$$\begin{bmatrix}y&x\\y&x\end{bmatrix}$$

```
H0L0L1H0
```

#### 状态 2（上下）

$$\begin{bmatrix}y&y\\x&x\end{bmatrix}$$

```
H0L1H0
```

$$\begin{bmatrix}x&x\\y&y\end{bmatrix}$$

```
H1L1H1
```

#### 状态 3（斜侧）

$$\begin{bmatrix}y&x\\x&y\end{bmatrix}$$

```
L1H0L1H0
```

$$\begin{bmatrix}x&y\\y&x\end{bmatrix}$$

```
L1H1L1H1
```

分类结束。

由于对称的 $4$ 元组个数 $\le\frac{n\cdot m}4$，所以操作次数 $\le 4\frac{n\cdot m}4=n\times m$

### Code

代码中 $x,y,a,b$：

| 坐标 | y | b |
| -----------: | -----------: | -----------: |
| **x** | sth. | sth. |
| **a** | sth. | sth. |


```cpp
//Said no more counting dollars. We'll be counting stars.
#include<bits/stdc++.h>
using namespace std;
#define IOS ios::sync_with_stdio(0);cin.tie(0);cout.tie(0)
#define fir first
#define sec second
#define mkp make_pair
#define pb emplace_back
#define For(i,j,k) for(int i=j;i<=k;i++)
#define Rof(i,j,k) for(int i=j;i>=k;i--)
#define N 102
#define endl '\n'
int c[N][N],n,m;
bool rev[N];//列是否被翻转 
vector<pair<bool,int> > ans;//答案操作序列 
void modi(bool x,int y){
	if(x) rev[y]^=1;
	ans.pb(mkp(x,y));
}
inline int C(int x,int y){return rev[y]?c[n-x+1][y]:c[x][y];}
int cnt;
inline bool pan(int x,int y,int val){
	if(c[x][y]!=val && c[x][y]!=m-val+1) return false;
	if(c[x][y]==val) cnt++;
	return true;
}
#define a n-x+1//x 对称 
#define b m-y+1//y 对称 
inline void work(string s,int x,int y){
	int len=s.size();
	for(int i=0;i<len;i+=2){
		if(s.substr(i,2)=="L0") modi(1,y);
		if(s.substr(i,2)=="L1") modi(1,b);
		if(s.substr(i,2)=="H0") modi(0,x);
		if(s.substr(i,2)=="H1") modi(0,a);
	}
}
bool check(int x,int y){
	cnt=0;
	if(!pan(x,y,y) || !pan(x,b,y) || !pan(a,y,y) || !pan(a,b,y) || cnt!=2) return false;
	#define val y
	//以下 5 个与题解顺序相同 
	     if(C(x,b)==val && C(a,b)==val) work("H0L0L1H0",x,y);
	else if(C(a,y)==val && C(a,b)==val) work("H0L1H0"  ,x,y);
	else if(C(x,y)==val && C(x,b)==val) work("H1L1H1"  ,x,y);
	else if(C(x,b)==val && C(a,y)==val) work("L1H0L1H0",x,y);
	else if(C(x,y)==val && C(a,b)==val) work("L1H1L1H1",x,y);
	return true;
}
signed main(){IOS;
	cin>>n>>m;
	For(i,1,n) For(j,1,m) cin>>c[i][j];
	For(j,1,(m>>1)) For(i,1,(n>>1)) if(!check(i,j)){cout<<"NO"<<endl;return 0;}
	cout<<"YES"<<endl<<ans.size()<<endl;
	for(auto i:ans) cout<<i.fir<<" "<<i.sec<<endl;
return 0;}
```