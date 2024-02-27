# CF1611F ATM and Students 题解

### UPD

发现这道题尺取法能 $O(n)$ 做，是我太蒻了 qwq。

### 题意简述

给一个长度为 $n$ 序列 $\{a\}$ 和一个整数 $s$，求一个最长的子串 $\{b\}$ 使得 $\{b\}$ 的任何前缀和均 $\geqslant -s$。

### 题目分析

由于是关于前缀和的问题，我们求出 $\{a\}$ 的前缀和 $\{c\}$。

枚举子串的左端点 $x$，接下来的问题就是如何 $O(\log n)$ 找到最靠右的右端点 $y$ 使得：
$$\forall z \ | \ x\leqslant z\leqslant y,c_z\geqslant c_{x-1}-s$$

我们想到倍增往右，可用 ST 表维护区间 $\{c\}$ 的最小值。

最后的最后再取长度 $\max$ 输出即可。

### Code

**注：代码变量名与上文推导变量名不同，请仔细辨别。**

```cpp
#define For(i,j,k) for(register int i=j;i<=k;i++)
#define Rof(i,j,k) for(register int i=j;i>=k;i--)
#define fir first
#define sec second
#define mkp make_pair
#define pii pair<int,int>
#define N 200010
#define C 20
#define int long long
int n,m,a[N];
pii ans;
int b[N][C+1];//ST 表
bool flag;
int check(int x){//求长度最大值
	int pos=x,tar=b[x-1][0]-m;
	Rof(i,C,0){
		if(pos-1+(1<<i)<=n && b[pos][i]>=tar){
			pos+=(1<<i);
		}
	}
	return pos-x;
}
void work(){
	cin>>n>>m;
	ans=mkp(0,0);
	For(i,1,n) cin>>a[i];
	flag=1;
	For(i,1,n)
		if(a[i]>=-m)
			flag=0;
	if(flag){//无人生还
		cout<<-1<<endl;
		return ;
	}
	For(i,1,n) b[i][0]=b[i-1][0]+a[i];//前缀和
	For(j,1,C){
		For(i,1,n){
			if(i+(1<<j)>n+1) break;
			b[i][j]=min(b[i][j-1],b[i+(1<<(j-1))][j-1]);
		}
	}
	For(i,1,n)//枚举左端点
		ckmx(ans,mkp(check(i),i));
	cout<<ans.sec<<" "<<ans.sec+ans.fir-1<<endl;
}
```