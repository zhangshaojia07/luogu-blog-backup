# CF1567B MEXor Mixup题解

### 题意

给出一个序列的 $\operatorname{MEX}$ 和 $\operatorname{XOR}$，求原序列的最小长度。

### 分析

由于 $\operatorname{MEX}$ 的定义，序列里 $0\to\operatorname{MEX}-1$ 的数必取，而且 $\operatorname{MEX}$ 必不取。

我们设 $0\to i$ 的异或和为 $pre_i$。

若 $pre_{mex-1}=\operatorname{XOR}$，则显然序列中 $0\to\operatorname{MEX}-1$ 的数全取一遍即为答案。

否则我们设 $m=pre_{mex-1} \ xor \ \operatorname{XOR}$。

若 $m\ne \operatorname{MEX}$，则序列中取 $0\to\operatorname{MEX}-1$ 的数各一遍和 $m$ 即为答案。

若 $m=\operatorname{MEX}$，则序列中不能取 $m$， $0\to\operatorname{MEX}-1$ 的数各一遍和异或为 $m$ 的任意两个正整数即为答案。

可以证明这个方案是最优的。

### Code

```cpp
#include<bits/stdc++.h>
using namespace std;
#define IOS ios::sync_with_stdio(0);cin.tie(0);cout.tie(0)
#define For(i,j,k) for(register int i=j;i<=k;i++)
#define N 400000
int a,b,c;
int pre[N+10];//0~i的异或和 
void init(){
	pre[0]=0;
	For(i,1,N){
		pre[i]=pre[i-1]^i;
	}
}
signed main(){
	IOS;
	init();
	int T;
	cin>>T;
	while(T--){
		cin>>a>>b;
		c=pre[a-1]^b;
		if(c==0)
			cout<<a<<endl;
		else if(c==a)
			cout<<a+2<<endl;
		else
			cout<<a+1<<endl;
	}
    return 0;
}
```