# P8868 [NOIP2022] 比赛（单调栈/历史和线段树）

每组询问 $[L,R]$ 要求
$$
\sum_{[l,r]\in[L,R]}(\max_{i\in[l,r]} A_i)(\max_{i\in[l,r]}B_i)
$$
顺序扫 $r$，维护 $\{a\},\{b\}$ 分别表示以每个位置为 $l$ 时的 $\max_{i\in[l,r]} A_i,\max_{i\in[l,r]}B_i$。

对于询问 $[L,R]$，答案为 $r$ 刚刚扫完 $R$ 时 $[L,R]$ 的历史乘积和。

就得到了 20pts 暴力：

```cpp
int orz,n,q,A[N],B[N],sa[N],sb[N],ta=0,tb=0;
ull a[N],b[N],c[N];
vector<pi> ask[N];
ull ans[N];
signed main(){ios::sync_with_stdio(false),cin.tie(nullptr);
	cin>>orz>>n;
	rep(i,1,n) cin>>A[i];
	rep(i,1,n) cin>>B[i];
	cin>>q;
	rep(i,1,q){
		int l,r;cin>>l>>r;
		ask[r].pb(l,i);
	}
	rep(i,1,n){
		while(ta && A[sa[ta]]<=A[i]) ta--;
		while(tb && B[sb[tb]]<=B[i]) tb--;
		rep(j,sa[ta]+1,i) a[j]=A[i];
		rep(j,sb[tb]+1,i) b[j]=B[i];
		sa[++ta]=i;
		sb[++tb]=i;
		rep(j,1,i) c[j]+=a[j]*b[j];
		for(auto j:ask[i]){
			ull x=0;
			rep(k,j.fir,i) x+=c[k];
			ans[j.sec]=x;
		}
	}
	rep(i,1,q) cout<<ans[i]<<"\n";
return 0;}
```

接下来参考 CF1083D 的做法，用两个单调栈维护后缀最大值位置，问题就转化为：

* $\{a\}$ 区间赋值。

* $\{b\}$ 区间赋值。

* 区间内 $\{c\}$ 加上对应项 $\{a\},\{b\}$ 相乘。

* 查询 $\{c\}$ 区间和。

线段树 $O(n\log n)$ 可做。

树节点维护：$c,a,b,ab$ 的和。

tag 维护：$ab,a,b,1$ 加到 $c$ 中的系数，$a,b$ 的赋值标记。

100pts 代码太丑了，不放了吧。

其实也可以用矩阵维护标记，代码更简单不过常数大。