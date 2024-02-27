# CF1714E Add Modulo 10

~~这玩意有算法吗。~~

首先我们达成共识：相同的两个数经过操作后必然相同（这不是映射吗）。

我们考察十进制末位，即数经过一次变换后末位会从啥变成啥：

![](https://cdn.luogu.com.cn/upload/image_hosting/ieb4ba3a.png)

由图得 $\{a\}$ 中不能同时存在 $5$ 的倍数和非 $5$ 的倍数。

若全为 $5$ 的倍数，将她们的末尾全部操作为 $0$，判断相等即可。

若全非 $5$ 的倍数，将她们的末尾全部操作为 $2$。由于 $2\to 4\to 8\to 6\to 2$ 绕一圈后原数会 $+20$，所以我们判断这些数是否 $\bmod\ 20$ 同余即可。

```cpp
//We'll be counting stars.
#pragma GCC optimize("Ofast")
#include<bits/stdc++.h>
using namespace std;
#define For(i,j,k) for(int i=(j),i##_=(k);i<=i##_;i++)
#define N 200010
int n,a[N];
void calc(int &x){ while(x%10!=2 && x%10!=0) x+=x%10; }
void work(){
	cin>>n;
	For(i,1,n) cin>>a[i];
	int cnt=0;
	For(i,1,n) if(a[i]%5==0) cnt++;
	if(cnt>0 && cnt<n){
		cout<<"NO"<<endl;
		return ;
	}
	For(i,1,n) calc(a[i]);
	if(!cnt) For(i,1,n) a[i]%=20;
	For(i,2,n) if(a[i]!=a[i-1]){
		cout<<"NO"<<endl;
		return ;
	}
	cout<<"YES"<<endl;
}
signed main(){ios::sync_with_stdio(false),cin.tie(nullptr);
	int T;cin>>T;
	while(T--)work();
return 0;}
```