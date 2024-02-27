# CF1662N Drone Photo

### Preface

这谁又想得到呢！（哭腔）

### Analysis

考虑如何简化这个举横幅 $4$ 人的年龄关系。

------------

**引理：** 给矩形四条边定向，从年轻 $\to$ 年长（这样明显是 DAG），将 $4$ 人分为了

* $\mathtt{A}$ 入度 $2$ 出度 $0$

* $\mathtt{B}$ 入度 $1$ 出度 $1$

* $\mathtt{C}$ 入度 $0$ 出度 $2$

三类。其中 $\mathtt{B}$ 类点 $0$ 或 $2$ 个，且当且仅当 $\mathtt{B}$ 类点在总共 $4$ 个中有 $2$ 个时，横幅有效。

**证明：** 首先显然由点的度得出 $\mathtt{B}$ 类点 $0$ 或 $2$ 个。

若 $0$ 个，则必然是类似如下形态的↓

![](https://cdn.luogu.com.cn/upload/image_hosting/e0i9qpny.png)

不成立。

若 $2$ 个，则必然是类似如下两种形态之一的↓

![](https://cdn.luogu.com.cn/upload/image_hosting/88gyy3hg.png)

![](https://cdn.luogu.com.cn/upload/image_hosting/b02pa97y.png)

成立。

------------

所以，统计每个人当 $\mathtt{B}$ 类点的次数之和除以 $2$ 即为答案。

而一个人当 $\mathtt{B}$ 类点的次数为

$$
a(n-1-b)+b(n-1-a)
$$

其中 $a,b$ 分别表示这个人当前行、列比她年纪小的人数。

### Code

```cpp
int r[N],c[N],n; 
pair<int,int> pos[N*N];
signed main(){IOS;
	cin>>n;
	int x,y;
	For(i,1,n){
		For(j,1,n){
			cin>>x;
			pos[x]=mkp(i,j);
		}
	} 
	int ans=0;
	For(i,1,n*n){
		x=r[pos[i].fir]++;
		y=c[pos[i].sec]++;
		ans+=x*(n-1-y)+y*(n-1-x); 
	}
	cout<<ans/2<<endl;
return 0;}
```