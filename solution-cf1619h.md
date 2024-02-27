# CF1619H Permutation and Queries

### Preface

~~H 是 Hydro，代表水题（不是）~~

分块好题（其实应该是根号分治？）

### Analysis

设 $B=\sqrt{n}$。

将原问题转化成有向图，发现是多个环。我们对于一个点 $x$，不仅存下一个是 $ne_x$，还维护上一个是 $pr_x$、以及下 $B$ 个是 $ju_x$（jump）。

对于操作 `1 x y`，我们 $O(1)$ 修改 $pr,ne$，发现 $ju$ 改变的只有 $x,y$ 的前驱 $B$ 个（包括本身）。通过 $pr$ 追溯到 $x$ 的上 $B-1$ 个，通过两个指针 $O(B)$ 求出（具体看代码），$y$ 同理。

对于询问 `2 i k`，先将 $i$ 向前用 $ju$ 数组 jump，再一步一步走，$O(\dfrac{n}{B})=O(B)$。

总时间 $O(n\sqrt{n})$ 预处理和 $O(q\sqrt{n})$ 维护、回答。

### Code

```cpp
#define N 100010
int pr[N],ne[N],ju[N];
int n,b;
inline int move(int x,int y){
	while(y--) x=ne[x];
	return x;
}
void calc(int x){
	int y=ne[x];
	For(i,1,b-1) x=pr[x];
	For(i,1,b){
		ju[x]=y;
		x=ne[x];
		y=ne[y];
	}
}
signed main(){
	int q;
	cin>>n>>q;
	b=sqrt(n);
	cerr<<"b"<<b<<endl;
	For(i,1,n) cin>>ne[i];
	For(i,1,n) pr[ne[i]]=i;
	For(i,1,n) ju[i]=move(i,b);
	int opt,x,y;
	while(q--){
		cin>>opt>>x>>y;
		if(opt==1){
			swap(pr[ne[x]],pr[ne[y]]);
			swap(ne[x],ne[y]);
			calc(x);
			calc(y);
		}else if(opt==2){
			while(y>=b){
				y-=b;
				x=ju[x];
			}
			cout<<move(x,y)<<endl;
		}else assert(0);
	}
    return 0;
}
```