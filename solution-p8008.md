# P8008 「Wdsr-3」迷途竹林

### Preface

考场上卡精度差点死亡。

### Analysis

由于竹子（以下称为直线）是斜的且间距不为 $1$，非常难搞。

用线性代数基础知识将顶点逆时针旋转 $\alpha=-\theta$ 的角度（即顺时针旋转 $\theta$）（三角函数中请转成弧度）：

$$\begin{bmatrix}
\cos\alpha&-\sin\alpha
\\
\sin\alpha&\cos\alpha
\end{bmatrix}
\begin{bmatrix}x\\y\end{bmatrix}=
\begin{bmatrix}x'\\y'\end{bmatrix}$$

再纵向压缩：

$$\begin{bmatrix}
1&0
\\
0&\frac{1}{a}
\end{bmatrix}
\begin{bmatrix}x\\y\end{bmatrix}=
\begin{bmatrix}x'\\y'\end{bmatrix}$$

发现这样的操作不改变答案，我们在这张新图上处理。

问题转化成：一个封闭图形与 $x=k(k\in\mathbb{Z})$ 的交的长度之和。

由于按照顺时针顺序给出多边形的 $n$ 个顶点的坐标，可以通过相邻两点的坐标位置判断哪一侧是内部。

![](https://cdn.luogu.com.cn/upload/image_hosting/q69fpqh8.png)

最后答案相当于 $n$ 个等差序列（几何上：梯形）的和差。

### Detail

注意边必须一端开一端闭，否则经过顶点的直线会被算两次。

### Code

对不起，比较冗长。

```cpp
//Said no more counting dollars. We'll be counting stars.
#include<bits/stdc++.h>
using namespace std;
#define ll long long
#define vi vector<int>
#define pi pair<int,int>
#define fir first
#define sec second
#define mkp make_pair
#define pb emplace_back
#define mem(x,y) memset(x,y,sizeof(x))
#define debug cerr<<"Passed line #"<<__LINE__<<endl
#define IOS ios::sync_with_stdio(0);cin.tie(0);cout.tie(0)
#define For(i,j,k) for(register int i=j;i<=k;i++)
#define Rof(i,j,k) for(register int i=j;i>=k;i--)
#define cmx(a,b) a=max(a,b)
#define cmn(a,b) a=min(a,b)
#define db long double
#define N 100010
#define j2h(x) ((double)3.14159265358979323846*(x)/180.0)//角度 -> 弧度
#define pdd pair<db,db>
#define sm 0.000000000000001//卡精度
db al,len;
pdd p[N];
int n;
inline void chan(int x){//线性变换
	pdd res;
	res.fir=p[x].fir*cos(j2h(-al))-p[x].sec*sin(j2h(-al));
	res.sec=p[x].fir*sin(j2h(-al))+p[x].sec*cos(j2h(-al));
	res.sec/=len;
	p[x]=res;
}
#define B (double)1000000000000//卡精度（先将坐标放大，后将答案缩小）
inline db qu(pdd x,pdd y,db z){//query x 坐标（取整用）
	db bi=B*(x.sec-z)/(x.sec-y.sec);
	return (x.fir*(B-bi)+y.fir*bi)/B;
}
db calc(int x){//计算一条边的贡献
	pdd s=p[x],t=p[x+1];
	db ss,tt;
	if(s.sec>t.sec){
		ss=floor(s.sec-sm);
		tt=ceil(t.sec-sm);
		if(ss<tt) return 0;
	}else{
		ss=ceil(s.sec+sm);
		tt=floor(t.sec+sm);
		if(ss>tt) return 0;
	}
	pdd sss,ttt;
	sss.fir=qu(s,t,ss);
	sss.sec=ss;
	ttt.fir=qu(s,t,tt);
	ttt.sec=tt;
	s=sss;
	t=ttt;
	int sz=abs(t.sec-s.sec)+1+sm;
	db res=sz*(s.fir+t.fir)/2;
	if(p[x].sec<p[x+1].sec) res*=-1;
	return res;
}
db work(){
	db res=0;
	p[n+1]=p[1];
	db tmp;
	For(i,1,n){
		tmp=calc(i);
		res+=tmp;
	}
	return res;
}
signed main(){
	IOS;
	cin>>n;
	For(i,1,n) cin>>p[i].fir>>p[i].sec;
	For(i,1,n) p[i].fir*=B;
	For(i,1,n) p[i].sec*=B;
	cin>>al>>len;
	len*=B;
	len*=sin(j2h(al));
	For(i,1,n) chan(i);
	cout<<fixed<<setprecision(15)<<work()/B<<endl;
    return 0;
}
```