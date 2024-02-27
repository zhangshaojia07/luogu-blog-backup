# P8266 [USACO22OPEN] Photoshoot B

### Analysis

为方便，设更赛牛处于队列中的偶数位置的个数为 $Ans$。

先将两个两个相邻的分组。设 $m=\frac{n}{2}$，则我们可以得到一个序列 $a_1,a_2,\dots,a_m$，其中 $a_i$：

* 若她代表的两个字符相等，则为 $2$，代表 reverse 这两个字符对 $Ans$ 不影响。

* 若为 `'GH'`，则为 $1$，表示 reverse 后能使 $Ans+1$。

* 若为 `'HG'`，则为 $0$，表示 reverse 后会使 $Ans-1$。

------------

**结论：** 取得 $Ans$ 最大的任意最终奶牛序列的 $\{a\}$ 一定不含值 $1$。

**证明：** 假设含有，设 $a_x=1$，则将前 $2x$ 个奶牛 reverse，再将前 $2x-2$ 个奶牛 reverse，就会将 $a_x$ 变为 $0$，其他不变，得到更优 $Ans$。矛盾，原命题得证。

------------

所以，$1\to m$ 枚举 $a$ 的下标 $x$，由于我们最终要消灭 $a_i=1$，每步满足 $a_{[1,x)}$ 中没有 $0$ 或没有 $1$。

然后我们暴力 reverse 是 $O(n^2)$ 的，所以我们存一个 tag $O(n)$ 做即可。

注意最后如果没有 $0$ 还要再 reverse 一下。

### Code

```cpp
#define N 200010
int n,a[N],ans=0;
char c1,c2;
signed main(){IOS;
	cin>>n;n>>=1;
	For(i,1,n){
		cin>>c1>>c2;
		a[i]=(c1==c2?2:(c1=='G'?1:0));
	}
	int now=2;
	For(i,1,n){
		if(now==2){
			now=a[i];
			continue;
		}
		if(a[i]<2 && now!=a[i]){
			ans++;
			now=a[i];
		}
	}
	if(now==1) ans++;
	cout<<ans<<endl;
return 0;}
```