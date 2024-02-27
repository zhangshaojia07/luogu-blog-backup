# P5302 [GXOI/GZOI2019]特技飞行

## Preface

缝合题刺激。

好欸，[黄题 逆序对](https://www.luogu.com.cn/problem/P1908) $+$ [蓝题【模板】扫描线](https://www.luogu.com.cn/problem/P5490) $=$ 本黑题。

## Analysis

发现 特技飞行的得分 和 嘉宾观测的得分 可以分开算。

### 特技飞行得分

这部分的得分不是固定的。

我们先算出交叉点个数（逆序对思想）。

#### 「对向交换」最多

即所有交叉点都是「对向交换」。

#### 「对向交换」最少

将飞行员按起点高度排序，他们终点高度 rank 的排列设为 $p_1,p_2,\dots,p_n$。

设 $cy$ 为 $\{p\}$ 置换环的个数。

「对向交换」最少 $n-cy$ 个。因为手玩一下发现每一个置换环（设个数为 $len$）至少要 $len-1$ 次「对向交换」。证明不难。

### 嘉宾观测得分

这部分的得分是固定的。

发现嘉宾观测范围就是一个斜 $45^{\circ}$ 的正方形，我们用矩阵乘法将坐标系旋转 $45^{\circ}$，就是模板扫描线了。

扫描线甚至可以用树状数组。

## Code

（丑死，但是还是贴了）

```cpp
//Said no more counting dollars. We'll be counting stars.
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
#define int long long
#define N 100003
#define M 500010
#define db double
#define Pos pair<db,db>
#define low (x&(-x))
//P5302 [GXOI/GZOI2019]????
const db inf=1e16;
const db eps=1e-6;
const db sq2=(db)1.4142135623730950488016887242097;
const db cs=sq2/2;
const db sn=-cs;
int n,m,q,A,B,C,a1,a2,ans;
db S,T;
pair<int,int> a[N],b[N];
int c[N],d[N];
namespace scl{
	Pos ext=mkp(inf,-inf);
	vector<Pos> inter;
	vector<pair<Pos,db> > obs;
	vector<pair<pair<db,bool>,Pos> > line;
	db v[M];
	int tot=0;
	int c[M];
	void add(int x,int y){
		while(x<=tot){
			c[x]+=y;
			x+=low;
		}
	}
	int que(int x){
		int res=0;
		while(x){
			res+=c[x];
			x-=low;
		}
		return res;
	}
	void init(){
		ext=mkp(inf,-inf);
		for(auto &i:inter){
			Pos j=i;
			i.fir=j.fir*cs-j.sec*sn;
			i.sec=j.fir*sn+j.sec*cs;
			ckmn(ext.fir,i.fir);
			ckmx(ext.sec,i.fir);
		}
		for(auto &i:obs){
			Pos j=i.fir;
			i.fir.fir=j.fir*cs-j.sec*sn;
			i.fir.sec=j.fir*sn+j.sec*cs;
			i.sec*=sq2/2;
			i.sec+=eps;
		}
		db l,r; 
		for(auto i:obs){
			l=i.fir.fir-i.sec;r=i.fir.fir+i.sec;
			if(r<ext.fir-eps || l>ext.sec+eps) continue;
			line.pb(mkp(mkp(i.fir.sec-i.sec,1),mkp(l,r)));
			line.pb(mkp(mkp(i.fir.sec+i.sec,0),mkp(l,r)));
		}
//		cerr<<"s_inter:"<<endl; for(auto i:inter) cerr<<i.fir<<" "<<i.sec<<endl;
//		cerr<<"s_obs:"<<endl; for(auto i:obs) cerr<<i.fir.fir<<" "<<i.fir.sec<<" "<<i.sec<<endl;
//		cerr<<"Line:"<<endl; for(auto i:line) cerr<<i.fir.fir<<" "<<i.fir.sec<<" "<<i.sec.fir<<" "<<i.sec.sec<<endl;
	}
	void lisan(){
		for(auto i:inter) v[++tot]=i.fir;
		sort(v+1,v+1+tot);
		tot=unique(v+1,v+1+tot)-v-1;
//		cerr<<"%%% ";For(i,1,tot) cerr<<v[i]<<" "; cerr<<endl;
		for(auto &i:inter) i.fir=lower_bound(v+1,v+1+tot,i.fir)-v;
		for(auto &i:line){
			i.sec.fir=lower_bound(v+1,v+1+tot,i.sec.fir)-v;
			i.sec.sec=upper_bound(v+1,v+1+tot,i.sec.sec)-v-1;
		}
	}
	bool cmp(Pos x,Pos y){return x.sec<y.sec;}
	int work(){
		int res=0;
		init();
		lisan();
		sort(inter.begin(),inter.end(),cmp);
		sort(line.begin(),line.end());
//		cerr<<"s_l_inter:"<<endl; for(auto i:inter) cerr<<i.fir<<" "<<i.sec<<endl;
//		cerr<<"l_Line:"<<endl; for(auto i:line) cerr<<i.fir.fir<<" "<<i.fir.sec<<" "<<i.sec.fir<<" "<<i.sec.sec<<endl;
		auto pos=line.begin();
		tot++;
//		cerr<<"work"<<endl;
		for(auto i:inter){
			while(pos!=line.end() && pos->fir.fir<i.sec){
				add(pos->sec.fir,(pos->fir.sec?1:-1));
				add(pos->sec.sec+1,(pos->fir.sec?-1:1));
				pos=next(pos);
			}
			assert(que(i.fir)>=0);
//			cerr<<i.fir<<" "<<i.sec<<" "<<que(i.fir)<<endl;
			if(que(i.fir)>0) res++;
		}
		return res;
	}
}
int e[N];
bool v[N]; 
inline Pos ckpos(int x,int y){
	Pos xx=a[x],yy=a[y];
	double res=(yy.fir-xx.fir)*(T-S)/((xx.sec-xx.fir)-(yy.sec-yy.fir))+S;
	return mkp(res,xx.fir+(xx.sec-xx.fir)/(T-S)*(res-S));
}
inline int ms(int l,int r){
	if(l>=r) return 0;
	int mid=(l+r)>>1,res=ms(l,mid)+ms(mid+1,r),tl=l,tr=mid+1,tot=l;
	while(tl<=mid || tr<=r){
		if(tr>r || (tl<=mid && c[tl]<c[tr])){
			res+=tr-mid-1;
			For(i,mid+1,tr-1) scl::inter.pb(ckpos(c[tl],c[i]));
			d[tot++]=c[tl++]; 
		}else{
			d[tot++]=c[tr++];
		}
	}
	For(i,l,r) c[i]=d[i];
//	cerr<<l<<" "<<r<<":"<<res<<endl;
	return res;
}
inline int out(int x){return x*A+(m-x)*B;}
inline void see(int x){
	v[x]=1;
	if(!v[e[x]]) see(e[x]);
}
signed main(){IOS;
	cin>>n>>A>>B>>C>>S>>T;
	For(i,1,n) cin>>a[i].fir;
	For(i,1,n) cin>>a[i].sec;
	//a1
	sort(a+1,a+1+n);
	For(i,1,n) b[i]=mkp(a[i].sec,i);
	sort(b+1,b+1+n);
	For(i,1,n) e[i]=c[i]=b[i].sec;
//	cerr<<fixed<<setprecision(3);
//	cerr<<"c: "; For(i,1,n) cerr<<c[i]<<" "; cerr<<endl;
	a1=ms(1,n);
	m=scl::inter.size();
//	cerr<<"a1:"<<a1<<endl;
//	cerr<<"inter "<<m<<":"<<endl;for(auto i:(scl::inter)) cerr<<i.fir<<" "<<i.sec<<endl;
	//a2
	a2=n;
	For(i,1,n) if(!v[i]){see(i);a2--;}
//	cerr<<"a2:"<<a2<<endl;
	//ans
	cin>>q;
	scl::obs.resize(q);
	for(auto &i:(scl::obs)) cin>>i.fir.fir>>i.fir.sec>>i.sec;
	ans=scl::work();
	a1=out(a1);a2=out(a2);
	cout<<min(a1,a2)+ans*C<<" "<<max(a1,a2)+ans*C<<endl;
return 0;}
```