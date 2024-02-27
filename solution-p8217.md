# P8217 [THUPC2022 初赛] 数正方体

### Preface

[本题の逆（make_data）](https://www.luogu.com.cn/problem/P1058)

学霸题，数正方体，头顶~~浇筑~~标注法！

### Analysis

通过首行前缀 `.` 个数可知从前往后的层数 $layer$。

通过末行后缀 `.` 个数可知从左往右的层数 $n$（好像与题面变量名重了 qwq）。

由于不存在中间凹的情况，存在一种 simple 的做法：

```
......+---+........
...../   /|........
....+---+ |........
....|   | +---+....
....|   |/   /|....
....+---+---+ |....
.../   /|   | +---+
..+---+ |   |/   /|
..|   | +---+---+ |
..|   |/   /   /| +
..+---+---+---+ |/|
./   /   /|   | + |
+---+---+ |   |/| +
|   |   | +---+ |/.
|   |   |/   /| +..
+---+---+---+ |/...
|   |   |   | +....
|   |   |   |/.....
+---+---+---+......
```

![解法](https://cdn.luogu.com.cn/upload/image_hosting/rc5ojrk5.png)

（其实就是差分转原序列）

这样就得到了头顶标注，加起来即可。

### Code

```cpp
//Said no more counting dollars. We'll be counting stars.
#include<bits/stdc++.h>
using namespace std;
#define For(i,j,k) for(int i=j;i<=k;i++)
#define gc getchar
#define pc putchar
inline int read(){
	int x=0;char c=gc();bool f=0;
	while(!isdigit(c)){if(c=='-')f=1;c=gc();}
	while(isdigit(c)){x=(x<<3)+(x<<1)+c-48;c=gc();}
	if(f)x=-x;return x;
}
inline void write(int x){if(x<0){pc('-');x=-x;}if(x>9)write(x/10);pc(x%10+'0');}
#define G 450
#define N 55
int r,c,ans=0,layer=0;
char mp[G][G];
int a[N],n,xx[N],yy[N];
int upward(int& x,int& y){
	int res=0;
	while(mp[x-1][y]=='|' || mp[x-1][y]=='+'){
		x--;
		res++;
	}
	return res/3;
}
void calc(){
	For(i,1,n) a[i]+=upward(xx[i],yy[i]);
	For(i,1,n) ans+=a[i];
	For(i,1,n) xx[i]-=2;
	For(i,1,n) yy[i]+=2;
}
signed main(){
	r=read();
	c=read();
	For(i,1,r){
		For(j,1,c) mp[i][j]=gc();
		gc();
	}
	while(mp[1][layer+1]=='.') layer++;
	layer>>=1;
	while(mp[r][n+1]!='.') n++;
	n=(n-1)>>2;
	For(i,1,n) xx[i]=r;
	For(i,1,n) yy[i]=i*4+1;
	For(i,1,layer) calc();
	write(ans);
return 0;}
```