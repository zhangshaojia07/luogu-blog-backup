# UVA1655题解

~~一定有很多人在想整除分块和杜教筛。~~

### 题目转化

求三元有序组 $(a,b,c)$ 使得 $a\times b\times c\leqslant n$ 的个数（下文记作 $ans$）。

### 题目分析

由于枚举有序组是 $O($ 不能过 $)$ 的，考虑转化成无序组。

设 $p=(x,y,z) \quad (x\leqslant y\leqslant z)$：

- 若 $x=y=z$ 则对 $ans$ 的贡献为 $1$。

（多重集合 $S=\{a,a,a\}$ 排列数为 $1$）

- 若 $x=y<z$ 或 $x<y=z$ 则对 $ans$ 的贡献为 $3$。

（多重集合 $S=\{a,a,b\}$ 排列数为 $3$）

- 若 $x<y<z$ 则对 $ans$ 的贡献为 $6$。

（多重集合 $S=\{a,b,c\}$ 排列数为 $6$）

所以枚举无序三元组，就对应出有序三元组的个数。

时间复杂度：$O(\approx T\times n^{\frac{3}{4}})$

### ACcode

细节看代码

```cpp
#include<bits/stdc++.h>
using namespace std;
#define For(i,j,k) for(register long long i=j;i<=k;i++)
#define ll long long

ll n;
ll limit;//sqrt[3](n) 
ll work(){
	ll tmp,res;
	
	res=limit;//x=y=z
	
	For(i,1,limit){
		
		tmp=n/(i*i)-i;
		res+=3*tmp;//x=y<z
		
		for(ll j=i+1;i*j*j<=n;j++){
			tmp=n/(i*j)-j;
			res+=6*tmp;//x<y<z
			res+=3;//x<y=z
		}
	}
	return res;
}
signed main(){
	int tot=0;
	
	while(cin>>n){
		//make_limit 
		limit=pow(n,1.0/3);
		while((limit+1)*(limit+1)*(limit+1)<=n)
			limit++;
			
		cout<<"Case "<<++tot<<": "<<work()<<endl;
	}
	
    return 0;
}

```