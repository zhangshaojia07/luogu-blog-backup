# P3120 [USACO15FEB]Cow Hopscotch G（cdq）

超级好想好推好写的 cdq 分治解法！


我们设 $f_{i,j}$ 表示到达 $(i,j)$ 的方案数。

显然有转移：
$$
f_{i,j}=\sum_{i'<i}\sum_{j'<j}[a_{i,j}\ne a_{i',j'}]f_{i',j'}
$$
发现是偏序关系，cdq 没跑了。

我们按 $i$ 分治，设 $i\in[l,mid]$ 的所有 $f_{i,j}$ 为集合 $L$，同理 $i\in(mid,r]$ 的所有 $f_{i,j}$ 为集合 $R$。

我们 $O((r-l)m)$ 计算 $L$ 对 $R$ 的贡献即可 $O(nm\log n)$ 解决原问题。

具体地，我们按 $j$ 从小到大扫描 $L$ 中的元素存入桶。扫完一列 $L$ 后更新对应右侧一列的 $R$ 中的元素（记得做完后清空桶）。

```cpp
//We'll be counting stars.
#include<bits/stdc++.h>
using namespace std;
#define For(i,j,k) for(int i=(j);i<=(k);i++)
#define Rof(i,j,k) for(int i=(j);i>=(k);i--)
#define int long long
char buf[1<<21],*p1,*p2;
#define gc() (p1==p2&&(p2=(p1=buf)+fread(buf,1,1<<21,stdin),p1==p2)?EOF:*p1++)
inline int read() {
	int x=0,f=1;
	char c=gc();
	while(c<'0'||c>'9'){if(c=='-')f=-1;c=gc();}
	while(c>='0'&&c<='9'){x=(x<<3)+(x<<1)+(c^48);c=gc();}
	return x*f;
}
const int mod=1000000007,N=751;
int n,m,k,a[N][N],f[N][N],g[N*N];
void solve(int l,int r){
	if(l==r) return ;
	int mid=(l+r)>>1,sum=0;
	solve(l,mid);
	For(j,1,m){
		For(i,mid+1,r) (f[i][j]+=sum-g[a[i][j]]+mod)%=mod;
		For(i,l,mid){
			(g[a[i][j]]+=f[i][j])%=mod;
			(sum+=f[i][j])%=mod;
		}
	}
	For(i,l,r) For(j,1,m) g[a[i][j]]=0;//clear bucket
	solve(mid+1,r);
}
signed main(){
	n=read(),m=read(),k=read();
	For(i,1,n) For(j,1,m) a[i][j]=read();
	f[1][1]=1;
	solve(1,n);
	printf("%lld",f[n][m]);
return 0;}
```