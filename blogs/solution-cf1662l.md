# CF1662L Il Derby della Madonnina（转二维平面问题）

将每一个 Kick 看作二维平面上的点。若一个 Kick 在时间 $t$，在位置 $x$，人的速度为 $v$，则 Kick 的坐标为

$$
(v\cdot t,x)
$$

这样，当人现在在坐标 $(x,y)$（当然人初始坐标为 $(0,0)$），她能够赶到的 Kick 在她的右上 $45^{\circ}$ 到右下  $45^{\circ}$ 的半平面交里。

![](https://cdn.luogu.com.cn/upload/image_hosting/yzfdwk0w.png)

（↑样例中的三个 Kick 坐标）

当然，这样还是难于计算，我们将所有点按原点顺时针旋转 $45^{\circ}$。具体地，我们将每一个坐标

$$
(x,y)\to (x+y,-x+y)
$$

（这样每个点到原点的距离会扩大至原来的 $\sqrt2$ 倍，但是无伤大雅不影响计算）

这样每个平面上的点能到达的区域为右下角 $90^{\circ}$ 的半平面交。

![](https://cdn.luogu.com.cn/upload/image_hosting/04krnyjd.png)

（↑三个 Kick 坐标进行变换）

我们将 Kick 按照当前坐标排序，先按 $x$ 升序，次按 $y$ 降序。

这样排序在后的 Kick 不可能赶到排序在前的 Kick，满足了 DP 的**无后效性**。

设 $f_i$ 表示当前在排序后第 $i$ 个 Kick 的位置上，最多能看几个 Kick（包括 $i$ 本身）。

然后从后往前 $n\to1$ 枚举 Kick，其

$$
f_i=\max_{j\in[i+1,n],y_j\le y_i}f_j+1
$$

我们用 BIT（树状数组）维护每个 $y$，$f_j(\text{s.t.}\ y_j=y)$ 的最大值（当然 $y$ 坐标要离散化）。

即可 $O(n\log n)$ 求解所有 $f$。最后 $O(n)$ 枚举第一个看的 Kick 求答案即可。 

### Code

```cpp
//Said no more counting dollars. We'll be counting stars.
#pragma GCC optimize("Ofast")
//#pragma GCC optimize("unroll-loops")//DONT use rashly,I have suffered
//#pragma GCC target("sse,sse2,sse3,ssse3,sse4,popcnt,abm,mmx,avx,avx2,tune=native")//DONT use rashly,I have suffered
#include<bits/stdc++.h>
using namespace std;
#define IOS ios::sync_with_stdio(0);cin.tie(0);cout.tie(0)
#define fir first
#define sec second
#define mkp make_pair
#define pb emplace_back
#define mem(x,y) memset(x,y,sizeof(x))
#define For(i,j,k) for(int i=j;i<=k;i++)
#define Rof(i,j,k) for(int i=j;i>=k;i--)
#define Fe(x,y) for(int x=head[y];x;x=e[x].nxt)
#define ckmx(a,b) a=max(a,b)
#define ckmn(a,b) a=min(a,b)
#define fin(s) freopen(s,"r",stdin)
#define fout(s) freopen(s,"w",stdout)
#define file(s) fin(s".in");fout(s".out")
#define cerr cerr<<'_'
#define debug cerr<<"Passed line #"<<__LINE__<<endl
template<typename T>T ov(T x){cerr<<"Value: "<<x<<endl;return x;}
#define ll long long
const ll mod=1000000007;
inline ll pw(ll x,ll y){ll r=1;while(y){if(y&1)r=r*x%mod;x=x*x%mod;y>>=1;}return r;}
inline void mad(ll &a,ll b){a=(a+b)%mod;while(a<0)a+=mod;}
inline void mmu(ll &a,ll b){a=a*b%mod;while(a<0)a+=mod;}
#define inv(a) pw(a,mod-2)
#define int long long
#define N 200010
#define Pos pair<int,int>
#define low (x&(-x))
int n,lim,b[N],c[N];
Pos a[N];
void add(int x,int y){
	while(x<=lim){
		ckmx(c[x],y);
		x+=low;
	}
}
int que(int x){
	int res=0;
	while(x){
		ckmx(res,c[x]);
		x-=low;
	}
	return res;
}
bool cmp(Pos x,Pos y){
	if(x.fir!=y.fir) return x.fir<y.fir;
	else return x.sec>y.sec;
}
signed main(){IOS;
	int x,y;
	cin>>n>>x;
	For(i,1,n) cin>>a[i].fir;
	For(i,1,n) cin>>a[i].sec;
	a[0]=mkp(0,0);
//	For(i,0,n) cout<<i<<":"<<a[i].fir<<" "<<a[i].sec<<endl; cout<<endl;
	For(i,0,n) a[i].fir*=x;
	For(i,0,n){
		x=a[i].fir+a[i].sec;
		y=a[i].sec-a[i].fir;
		a[i]=mkp(x,y);//rotate 45 clockwise
	}
	For(i,0,n) b[i+1]=a[i].sec;
	sort(b+1,b+2+n);
	lim=unique(b+1,b+2+n)-b-1;
	For(i,0,n) a[i].sec=lower_bound(b+1,b+1+lim,a[i].sec)-b;
	int qx=a[0].fir,qy=a[0].sec;
	sort(a,a+1+n,cmp);
	Rof(i,n,0){
		x=que(a[i].sec);
		if(a[i].fir==qx && a[i].sec==qy){
			cout<<x<<endl;
			return 0;
		}
		add(a[i].sec,x+1);
	}
return 0;}
```

