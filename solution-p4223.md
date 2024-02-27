# P4223 期望逆序对

## （一）每两个初始位置最终的位置状态

设原序列为 $a_1,a_2,\dots,a_n$。

只考虑两个数 $a_L,a_R(L<R)$，我们分类讨论最后这两个数跑哪里去了（前者为 $a_L$ 去的地方，后者为 $a_R$ 的，$O$ 为除了 $L,R$ 剩下的位置）：

$$(L,R),(R,L),(O,R),(R,O),(L,O),(O,L),(O,O)$$

这一步 Swap 前各个状态的方案数和这一步后的方案数

$$Bef=\begin{bmatrix}
LR
\\RL
\\OR
\\RO
\\LO
\\OL
\\OO
\end{bmatrix}
,Aft=\begin{bmatrix}
LR'
\\RL'
\\OR'
\\RO'
\\LO'
\\OL'
\\OO'
\end{bmatrix}$$

则

$$M^k\cdot Bef=Aft$$

而且不论 $L,R$ 取啥，转移矩阵 $M$ 始终不变：

$$\begin{aligned}
A&=n-2
\\
B&=n-3
\\
C&=\binom{n-2}{2}
\\
D&=\binom{n-2}{2}+n-3
\\
E&=\binom{n-2}{2}+2(n-4)+1
\end{aligned}$$

$$M=\begin{bmatrix}
C&1&1&0&1&0&0
\\
1&C&0&1&0&1&0
\\
A&0&D&1&0&1&1
\\
0&A&1&D&1&0&1
\\
A&0&0&1&D&1&1
\\
0&A&1&0&1&D&1
\\
0&0&B&B&B&B&E
\end{bmatrix}$$

通过矩阵快速幂 $O(7^3\log k)$ 得出每两个初始位置最终的位置状态。

后面为方便列式子，将 $Aft$ 中第 $i(=0,\dots,6)$ 个元素的值设为 $p_i$。

## （二）每两个初始位置最后是否是逆序对

我们这里设 $a_L<a_R$，反之不再赘述。

按最终状态分类（开头为贡献）：

### LR

$$p_0\cdot 0$$

### RL

$$p_1\cdot 1$$

### OR

$$p_2\cdot\frac{n-R}{n-2}$$

解释：

$a_L$ 要跑到 $R$ 右边才形成逆序对。

因为 $n-2$ 个 $O$ 的位置完全等价（对于 $a_L$ 出现的概率等），所以$a_L$ 要跑到 $R$ 后面的概率为 $\frac{n-R}{n-2}$。

### RO

$$p_3\cdot\frac{R-2}{n-2}$$

解释：

$a_R$ 要跑到 $R$ 左边才形成逆序对。

剩下同理。

### LO

$$p_4\cdot\frac{L-1}{n-2}$$

解释：

$a_R$ 要跑到 $L$ 左边才形成逆序对。

### OL

$$p_5\cdot\frac{n-1-L}{n-2}$$

解释：

$a_L$ 要跑到 $L$ 右边才形成逆序对。

### OO

$$p_6\cdot\frac{1}{2}$$

解释：

相当于在 $O$ 位置之间取两个位置 $x,y(x\ne y)$，$a_x<a_y$ 的概率。

由于不存在 $a_x=a_y$ 的情况（题面说排列），易知 $a_x<a_y$ 的概率为 $\frac{1}{2}$。

* * *

这些柿子又臭又长？把她们写成矩阵的形式吧！

由于上面的贡献均可写成（$c$ 均为常数）

$$p_*(c_0^*+c_1^*L+c_2^*R)$$

的形式，最后 $a_L,a_R$ 之间的贡献可以表示为

$$c_0+c_1L+c_2R$$

所以我们

$$
T\begin{bmatrix}p_0\\p_1\\ \vdots\\ p_6\end{bmatrix}=\begin{bmatrix}c_0\\c_1\\ c_2\end{bmatrix}
$$

其中 $T$ 为 $3$ 行 $7$ 列的常数矩阵（其实已经暗含在上面分类中的贡献里了）。

详见代码 `init()` 和 `solve()` 部分。 

## （三）树状数组统计贡献

~~书接上回~~，我们得到两个位置之间的贡献可表示为常数、两个下标的线性组合。

所以我们通过树状数组求出每个位置前面比她大的个数 和 下标和，然后计算答案即可。

## （四）代码

2KB ~~简短~~代码。

```cpp
//We'll be counting stars.
#include<bits/stdc++.h>
using namespace std;
#define For(i,j,k) for(int i=(j),i##_=(k);i<=i##_;i++)
#define Rof(i,j,k) for(int i=(j),i##_=(k);i>=i##_;i--)
#define int long long
const int mod=1e9+7,iv2=(mod+1)/2;
inline int pw(int x,int y){int r=1;while(y){if(y&1)r=r*x%mod;x=x*x%mod;y>>=1;}return r;}
#define N 500005
struct ma{
	int a[7][7];
	void init(){For(i,0,6)For(j,0,6)a[i][j]=(i==j);}
	void clear(){For(i,0,6)For(j,0,6)a[i][j]=0;}
	ma mul(ma x){
		ma res;res.clear();
		For(i,0,6)For(j,0,6)For(k,0,6)(res.a[i][j]+=a[i][k]*x.a[k][j])%=mod;
		return res;
	}
}M,R;
int k,p[7];
void kasumi(){
	R.init();
	while(k){
		if(k&1) R=R.mul(M);
		k>>=1; M=M.mul(M);
	}
	For(i,0,6) p[i]=R.a[i][0];
}
int n,a[N],c[N],cnt[N],pos[N],coe[3],m[3][7];
#define low (x&-x)
void add(int x,int y){
	while(x<=n) c[x]+=y,x+=low;
}
int que(int x){
	int res=0;
	while(x) res+=c[x],x-=low;
	return res;
}
int ans=0;
void solve(){
	For(i,0,2){
		coe[i]=0;
		For(j,0,6) (coe[i]+=m[i][j]*p[j])%=mod;
	}
	For(i,1,n) (ans+=cnt[i]*(coe[0]+i*coe[2]%mod)%mod+pos[i]%mod*coe[1]%mod)%=mod;
}
void reve(){
	For(i,1,n) cnt[i]=i-1-cnt[i];
	For(i,1,n) pos[i]=i*(i-1)/2-pos[i];
	For(i,0,6) m[0][i]=(1-m[0][i]+mod)%mod;
	For(j,1,2) For(i,0,6) m[j][i]=(-m[j][i]+mod)%mod;
}
void init(){
	int iv=pw(n-2,mod-2);
	m[0][1]=1,m[0][2]=n*iv%mod,m[0][3]=(mod-2)*iv%mod,m[0][4]=(mod-1)*iv%mod,m[0][5]=(n-1)*iv%mod,m[0][6]=iv2;
	m[1][4]=iv,m[1][5]=(mod-1)*iv%mod;
	m[2][2]=(mod-1)*iv%mod,m[2][3]=iv;
}
int32_t main(){
	ios::sync_with_stdio(false);
	cin.tie(nullptr);
	cin>>n>>k;
	int A=n-2,B=n-3,C=((n-2)*(n-3)+mod)%mod*iv2%mod,D=(C+n-3+mod)%mod,E=(C+2*n-7+mod)%mod;
	M=(ma){{
	{C,1,1,0,1,0,0},
	{1,C,0,1,0,1,0},
	{A,0,D,1,0,1,1},
	{0,A,1,D,1,0,1},
	{A,0,0,1,D,1,1},
	{0,A,1,0,1,D,1},
	{0,0,B,B,B,B,E},
	}};
	kasumi();
	For(i,1,n) cin>>a[i];
	For(i,1,n) cnt[i]=que(a[i]),add(a[i],1);
	For(i,1,n) c[i]=0;
	For(i,1,n) pos[i]=que(a[i]),add(a[i],i);
	For(i,1,n) c[i]=0;
	init();solve();
	reve();solve();
	cout<<ans<<endl;
return 0;}
```