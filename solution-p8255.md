# P8255 [NOI Online 2022 入门组] 数学游戏

### Preface

省流：被题目中的「你需要输出最小的正整数 $y$」诈骗了。

### Analysis

其实答案 $y$ **最多只有一个**。

设 $v=\dfrac{z}{x}$，若非整数即无解。接下来求 $y\cdot\gcd(x,y)=v$ 的 $y$。

发现**对于每一个素因数可以分开讨论**，设当前考察的素因子为 $p$，且 $x,y,v$ 的素因数分解中分别恰好含有 $a,b,c$ 个 $p$。

则 $b+\min(a,b)=c$，分类讨论后得到 $b=\max(c-a,\dfrac{c}{2})$，进一步 $2b=\max(2c,c+2a)-2a$。

再从 $p$ 的幂次转到真正的值，得到

$$y^2=\frac{\text{lcm}(v^2,vx^2)}{x^2}$$

由于 $v<2^{63}$ 过大，不好放在 $\text{lcm}$ 里，由 $\text{lcm}(x,y)=\dfrac{xy}{\gcd(x,y)}$ 变换

$$y^2=\frac{v^2}{\gcd(v,x^2)}$$

$$y=\frac{v}{\sqrt{\gcd(v,x^2)}}$$

当根号内**不为平方数则无解**，否则**分子必然整除分母**，输出 $y$ 即可。

### Code

```cpp
//Said no more counting dollars. We'll be counting stars.
#include<bits/stdc++.h>
using namespace std;
#define IOS ios::sync_with_stdio(0);cin.tie(0);cout.tie(0)
#define int long long
#define endl '\n'
signed main(){IOS;
	int T,x,z,v,t,s;cin>>T;
	while(T--){
		cin>>x>>z;
		if(z%x){cout<<-1<<endl;continue;}
		t=__gcd(v=z/x,x*x);
		s=sqrt(t);
		if(s*s!=t){cout<<-1<<endl;continue;}
		cout<<v/s<<endl;
	}
return 0;}
```