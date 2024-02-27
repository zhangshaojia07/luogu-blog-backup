# CF1615D X(or)-mas Tree

### Analysis

我们设题目中**边权条件为 A 条件，路径条件为 B 条件**。

**以下 ppcnt 均指 __builtin_popcount 函数，即二进制下 $1$ 的个数。$f(x)=ppcnt(x)\mod 2$。设点 $x$ 到 $root$ 的路径边权异或和为 $g(x)$**

容易发现设 $x\leftrightarrow y$ 的路径异或和为 $z$，则 $g(x)\operatorname{xor}g(y)=z,f(g(x))\operatorname{xor}f(g(y))=f(z)$（读者自证）。

因为 B 条件只和异或和的 $f$ 有关，所以边权的具体数值根本没用（除了最后的构造），对于一条已知的边权 $x$，我们存 $f(x)$ 即可。就是说 **A 条件均可转换成 B 条件**。

我们很容易想到 NO 的方案，就是一系列条件推出两个点 $x,y$ 的 $f(g(x))$ 与 $f(g(y))$ 既相同又不同，自相矛盾。

发现这可以用**带边权的并查集**维护，并查集的边 $(x,y)$ 边权表示点 $x,y$ 的 $f(g(x)),f(g(y))$ 是（$0$）否（$1$）相同。

最后，如果判定为 YES（即在并查集的过程中没有矛盾），则这样构造边权初始为 $-1$ 的边：

* 如果这条边两端 $x,y$ 在同一个并查集内，则边权设为 $f(g(x))\ !=f(g(y))$（bool 类型）

* 否则，边权任意（因为两者的关系现在不明确），再将这两个并查集合并即可。

### Code

```cpp
int n,m;
int f[N],g[N];
struct node{int x,y,val;};
vector<node> v,inp;
inline int gf(int x){//get father in dsu
	if(x==f[x]) return x;
	int tmp=gf(f[x]);
	g[x]^=g[f[x]];
	return f[x]=tmp;
}
inline void mou(int x){//make root in dsu
	int tmp=gf(x);
	if(x==tmp) return ;
	f[tmp]=x;
	g[tmp]=g[x];
	g[x]=0;
	f[x]=x;
}
inline int dis(int x){//the val to the root in dsu
	int tmp=gf(x);
	if(x==tmp) return 0;
	return g[x];
}
int calc(int x,int y){
	if(gf(x)!=gf(y)){
		mou(x);
		mou(y);
		f[x]=y;
		g[x]=0;
		return 0;
	}else{
		return dis(x)^dis(y);
	}
}
void work(){
	cin>>n>>m;
	v.clear();
	inp.clear();
	For(i,1,n) f[i]=i;
	For(i,1,n) g[i]=0;
	int x,y,z;
	For(i,1,n-1){
		 cin>>x>>y>>z;
		 inp.pb((node){x,y,z});//for construction
		 if(z>=0){
		 	v.pb((node){x,y,__builtin_popcount(z)&1});
		 }
	}
	For(i,1,m){
		cin>>x>>y>>z;
		v.pb((node){x,y,z});
	}
	for(auto i:v){
		if(gf(i.x)!=gf(i.y)){
			mou(i.x);
			mou(i.y);
			f[i.x]=i.y;
			g[i.x]=i.val;
		}else if(dis(i.x)^dis(i.y)^i.val){
			cout<<"NO"<<endl;
			return ;
		} 
	}
	cout<<"YES"<<endl;
	for(auto i:inp){
		cout<<i.x<<" "<<i.y<<" ";
		if(i.val>=0) cout<<i.val<<endl;
		else cout<<calc(i.x,i.y)<<endl;
	}
}
```