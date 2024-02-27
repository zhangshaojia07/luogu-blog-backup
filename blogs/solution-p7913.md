# P7913 [CSP-S 2021] 廊桥分配 题解

考试写挂了 qwq。

作为 T1 好像有点难吧……

### 分析

飞机数量为 $10^5$ 级别，不是线性就是带 $\log$。

先将每一个航班飞机存为一个 `pair<int,int>`，为此航班飞机抵达、离开时刻。

然而最终所求停靠廊桥的飞机数量的最大值可以转化成 $\max(f(x)+g(n-x)) \quad (0\leqslant x\leqslant n)$，其中 $f(x),g(x)$ 分别表示将 $x$ 个廊桥分配给国内航班 / 国际航班，此类航班停靠廊桥的飞机数量的最大值。

下文只讲述 $f(x)$ 的求法，$g(x)$ 的求法类似，不再赘述。

边界值：$f(0)=0$

考虑将廊桥一条一条供应至国内航班，用 `std::set<pair<int,int> >` 维护当前还在远机位的飞机。

当一条廊桥加入后，从 `set` 中删去这些飞机：

* `set` 中最早抵达的飞机 $p$

* `set` 中在 $p$ 离开后最早抵达的飞机 $p'$

* `set` 中在 $p'$ 离开后最早抵达的飞机 $p''$

* $\dots$

（建议使用 `lower_bound`）

最后当前的 $f=m_1-set.size()$

这样就可以在 $O(m\log m)$ 内 A 过此题。

### Code

不加注释应该也看得懂吧……

```cpp
#include<bits/stdc++.h>
using namespace std;
#define For(i,j,k) for(register int i=j;i<=k;i++)
#define ckmx(a,b) if(a<b){a=b;}
#define fir first
#define sec second
#define mkp make_pair
#define pii pair<int,int>
#define N 100010

int n,at,bt;
set<pii > a,b;
pii tmp;
set<pii >::iterator id;
int aa[N],bb[N];
int ma(){
	int res=0,now=0;
	while(true){
		id=a.lower_bound(mkp(now,now));
		if(id==a.end()) break;
		res++;
		now=id->sec;
		a.erase(id);
	}
	return res;
}
int mb(){
	int res=0,now=0;
	while(true){
		id=b.lower_bound(mkp(now,now));
		if(id==b.end()) break;
		res++;
		now=id->sec;
		b.erase(id);
	}
	return res;
}
signed main(){
	scanf("%d%d%d",&n,&at,&bt);
	For(i,1,at){
		scanf("%d%d",&tmp.fir,&tmp.sec);
		a.insert(tmp);
	}
	For(i,1,bt){
		scanf("%d%d",&tmp.fir,&tmp.sec);
		b.insert(tmp);
	}
	aa[0]=0;
	For(i,1,n)
		aa[i]=aa[i-1]+ma();
	bb[0]=0;
	For(i,1,n)
		bb[i]=bb[i-1]+mb();
	int ans=0;
	For(i,0,n)
		ckmx(ans,aa[i]+bb[n-i]);
	printf("%d\n",ans);
    return 0;
}
```