# CF1305F题解

~~良心题解，管理员求过。~~

有任何关于此篇题解的问题皆可私信[我](https://www.luogu.com.cn/user/101868)。

### 题意简述

简述不了 qwq。

### 题目分析

设最终答案为 $ans$,即最少操作次数。

两个显而易见的结论：① **$ans$ 最大不超过 $n$**。

证明（反证）：

若 $ans>n$ 则，

**将序列中所有奇数都加 $1$，满足 $2$ 是它们全部的公因数，$2 \mid \gcd$，成立。**

此时操作数为原序列奇数个数，不大于 $n$，所以小于 $ans$，假设矛盾。

②**必有一半及以上的元素不变、加 $1$ 或减 $1$。**

证明（反证）：

若在 $ans$ 中没有一半及以上的元素不变、加 $1$ 或减 $1$ 则，

有一半以上的元素变化量至少为 $2$。

此时 $ans\leq n=2\times \frac{n}{2} <$ 操作数，此方法一定不是最优方案，假设矛盾。

若一个数在 $ans$ 中不变、加 $1$ 或减 $1$，将它加一减一，共 $3$ 个数（排除 $0$）**分别分解质因数，将质因数存入 $set$，其中必有一个素数是最终 $\gcd$ 的因数**。

而每个数在 $ans$ 中不变、加 $1$ 或减 $1$的概率不小于一半。

所以我们在序列中随机找一个数 $50$ 次，钦定它不变、加 $1$ 或减 $1$，作上述操作。

结束后我们 $set$ 里有至多 $600$ 个质数（每个数至多 $12$ 个），遍历每一个素数，**看看序列里每个数操作至该素数的倍数的最小操作数之和是否能更新 $ans$** 即可。

时间复杂度 $O(600N+10^6C)$ 除 $N$ 外均为常数

### Details

要是不对，原因是 $50$ 次随机找数找到的元素变化量均至少为 $2$。

概率小于 $\frac{1}{2^{50}}$ 你要是没过可以去买彩票了。

### ACcode
```cpp
#include<bits/stdc++.h>
using namespace std;
#define inf 0x3fffffffffffffff
#define ll long long
#define FOR(i,j,k) for(register int i=j;(j<k)?(i<=k):(i>=k);i+=(j<k)?1:(-1))
#define IO ios::sync_with_stdio(0),cin.tie(0),cout.tie(0)
#define N 200010
#define C 1000000

int n;
ll ans=inf,sum;
ll a[N],pr[C/10];
set<ll> s;//要处理的素数集合 
bool p[C+1];
void init(){//埃氏筛素数（<=10^6） 
	FOR(i,2,C)p[i]=1;
	FOR(i,2,C){
		if(!p[i])continue;
		pr[++pr[0]]=i;
		for(ll j=1ll*i*i;j<=C;j+=i) p[j]=0;
	}
}
int Rand(int x,int y){//随机函数 
	int res=0;
	FOR(i,1,9)res=res*10+rand()%10;
	return x+res%(y-x+1);
}
void work(ll x){//质因数分解x后质因数插入set 
	FOR(i,1,pr[0]){
		if(x==1)break;
		if(x%pr[i]==0){
			x/=pr[i];
			s.insert(pr[i]);
		}
		while(x%pr[i]==0)x/=pr[i];
	}
	if(x>1)s.insert(x);
}
signed main(){
	srand(time(NULL));//seed 
    IO;
    init();
    cin>>n;
    FOR(i,1,n)cin>>a[i];
	int x;
	FOR(i,1,50){//50次，保险 
		x=Rand(1,n);
		//Δ=0/1/-1 
		work(a[x]+1);
		work(a[x]);
		if(a[x]>1)work(a[x]-1);
	}
	for(set<ll>::iterator it=s.begin();it!=s.end();it++){//遍历set 
		sum=0;
		FOR(i,1,n){
			if(a[i]<*it)sum+=*it-a[i];
			else sum+=min(a[i]%(*it),(*it)-a[i]%(*it));
		}
		ans=min(sum,ans);//update 
	}
	cout<<ans<<endl;
    return 0;
}

```

~~看完了，点个赞再走吧。~~