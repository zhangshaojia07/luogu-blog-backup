# P2202 [USACO13JAN]Square Overlap S

### Preface

简洁易懂的 $O(n)$ 解法！（最慢点 81ms）

思路类似于 [P7883 平面最近点对（加强加强版）](https://www.luogu.com.cn/problem/P7883)，可以先做一下。

### Analysis

两个正方形重叠 $\iff$ 两个中心的切比雪夫距离 $<K$。

我们将整个平面划分成 $K\times K$ 的无数个（实际用到的最多 $n$ 个）正方形，称为 Block。

对于一个正方形（其中心在 Block x），与她重叠的正方形必然满足中心 Block 为 Block x 周围的 $9$ 个 Block 中。

考虑将中心点一个一个放入平面对应的 Block 中，并遍历周围 $9$ 个 Block 中的中心点，判断是否重叠即可。

时间复杂度的证明：

前面 Block 标号用 Hash，所以不带 $\log$。（我用的 `unordered_map` 可能较慢）

由于我们遇到 $\ge2$ 对重叠时就停止算法，所以在任意时刻最多有一对重叠的正方形。显然每个点判断时 $9$ 个 Block 里中心点的个数是 $O(1)$ 的，于是总时间 $O(n)$。

### Details

记得读入时将坐标偏移一下，以免负数除法的问题。

对于暂时没有中心点存入的 Block，暂时不标号，节省空间。

### Code

[Link](https://www.luogu.com.cn/record/67669477)

头文件 + 快读比较长，其实主体还短的。

```cpp
//Said no more counting dollars. We'll be counting stars.
#include<bits/stdc++.h>
using namespace std;
#define fir first
#define sec second
#define mkp make_pair
#define pb emplace_back
#define For(i,j,k) for(int i=j;i<=k;i++)
#define Rof(i,j,k) for(int i=j;i>=k;i--)
#define gc getchar
#define pc putchar
inline int read(){
	int x=0;char c=gc();bool f=0;
	while(!isdigit(c)){if(c=='-')f=1;c=gc();}
	while(isdigit(c)){x=(x<<3)+(x<<1)+c-48;c=gc();}
	if(f)x=-x;return x;
}
#define N 50500
#define base 3000000
#define B 13331
#define ll long long
#define ull unsigned long long

const int dx[9]={1,1,1,0,0,0,-1,-1,-1},dy[9]={-1,0,1,-1,0,1,-1,0,1}; 
int n,m,tot=0,cnt=0;
pair<int,int> a[N],ans;
vector<int> b[N];
unordered_map<ull,int> mp;
inline int block(int x,int y,bool build){
	ull xx=x/m,yy=y/m;
	int tmp=xx*B+yy;
	if(build && mp[tmp]==0) mp[tmp]=++tot;
	return mp[tmp];
}
inline bool check(int x,int y){return abs(a[x].fir-a[y].fir)<m && abs(a[x].sec-a[y].sec)<m;}
void see(int x,int y){
	if(x==0) return ;
	for(int i:b[x]) if(check(i,y)){
		cnt++;
		ans=mkp(i,y);
		if(cnt>1) return ;
	}
}
void work(int t){
	int x,y;
	For(i,0,8){
		x=a[t].fir+m*dx[i];
		y=a[t].sec+m*dy[i];
		see(block(x,y,0),t);
		if(cnt>1) return ;
	}
}
signed main(){
	n=read(),m=read();
	For(i,1,n) a[i].fir=read()+base,a[i].sec=read()+base;
	For(i,1,n){
		work(i);
		if(cnt>1){
			puts("-1");
			return 0;
		}
		b[block(a[i].fir,a[i].sec,1)].pb(i);
	}
	if(cnt==0){
		puts("0");
		return 0;
	}
	int x=m-abs(a[ans.fir].fir-a[ans.sec].fir);
	int y=m-abs(a[ans.fir].sec-a[ans.sec].sec);
	printf("%lld\n",(ll)x*y);
return 0;}
```