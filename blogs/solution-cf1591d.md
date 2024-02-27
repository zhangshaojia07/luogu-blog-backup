# CF1591D Yet Another Sorting Problem

### Analysis

首先，**如果有两个数相同，则一定 YES**。

#### Proof

先将这两个数（设为 $x,y$）移到正确的位置，定死。

对于任意两个其他的数 $a,b$，均可通过以下步骤 swap：

1. $a\to b\to x$

2. $x\to a\to y$

而我们知道任意交换可以进行排序，即得证。

剩下只有序列为 $n$- 排列的情况了。

我们先将 $1\to (n-2)$ 都归位（因为还剩 $(n-1)$ 和 $n$ 两个所以必然可以做到），如果此时 $(n-1)$ 和 $n$ 也归位了，即 YES，否则 NO。

#### Proof

$\{1,2,\dots,n-2,n-1,n\}$ 的逆序对数为 $0$。

$\{1,2,\dots,n-2,n,n-1\}$ 的逆序对数为 $1$。

我们证明**只有两个排列逆序对数奇偶性相同才能通过三者轮换互相转化**，以此证明原命题。

考虑一次轮换 $a,b,c\to c,a,b$（$a,b,c\to b,c,a$ 相当于两次这种操作）：

发现无论 $a,b,c$ 的大小关系如何，$\{a,b,c\}$ 三者间的逆序对奇偶不变（枚举即可），只需考虑原来 $a,b$ 之间、$b,c$ 之间的数与 $a,b,c$ 构成的逆序对数即可，枚举两区间的值域对应的贡献，发现奇偶还是一样，原命题得证。

其实这与 $8$ 数码问题解的判定类似，感兴趣的读者可以查一下。
 
### Code

```cpp
int n,a[N];
int t[N];
int pos[N];
void modify(int x,int y,int z){//x->y y->z z->x
	int xx=a[x],yy=a[y],zz=a[z];
	a[x]=zz,a[y]=xx,a[z]=yy;
	pos[zz]=x,pos[xx]=y,pos[yy]=z;
}
void work(){
	cin>>n;
	For(i,1,n) t[i]=0;
	For(i,1,n) cin>>a[i];
	For(i,1,n) t[a[i]]++;
	For(i,1,n) if(t[i]>1){
		cout<<"YES"<<endl;
		return ;
	}
	For(i,1,n) pos[a[i]]=i;
	int x,y;
	For(i,1,n-2){
		if(pos[i]==i) continue;
		x=pos[i];
		y=i+1;
		if(y==x) y++;
		modify(x,i,y);
	}
	if(a[n-1]==n-1) cout<<"YES"<<endl;
	else cout<<"NO"<<endl;
}
```