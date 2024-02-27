# P8866 [NOIP2022] 喵了个喵（构造/分讨）

想到就很难了，$O(n)$ 精细实现也难。

首先 $k=2n-2$，我们想到 [NOIP2020] 移球游戏 的类似做法，将两种颜色的牌放在一堆里，这样会有一个空堆。

加入牌 $x$ 时，当其所属堆已有两张不同颜色的牌（否则直接 1 操作压入）。

若 $x$ 在顶部，我们 1 操作压入即可。

若 $x$ 在底部，我们 1 操作压入空堆，然后 2 操作两个堆底消掉即可。

这样我们保证了牌堆大小 $\le 2$，所以上述情况全矣。

为了方便拓展，当两种情况均可操作时，优先选择堆顶抵消。

考虑推广到 $k=2n-1$。

发现空堆的固定和牌颜色两两搭配的固定是非必要的，也就是说，我们可以暂时不满足上述性质，来处理这多出来的一种颜色，最后也不必还原回原来的空堆和配对。

当一张牌 $x$ 加入前局面时这样的：一个空堆，剩下每堆两个，共 $2n-2$ 种与 $x$ 不同的颜色。我们就需要更高妙的操作。

如果在下一次使用空堆前出现了另一张 $x$，则我们直接将这两个在空堆抵消。

否则我们找到下一张使用空堆消除的牌，记为颜色 $y$，她当前必然是某堆的底，设其堆顶为 $z$。

若 $z$ 在总牌堆在 $(x,y)$ 之间出现了偶数次，我们：

1. 将 $x$ 放在 $y$ 所在堆顶。

2. 持续按原来的套路消除，$z$ 直接在 $x$ 上方消除。

3. $y$ 通过底部消除。

若出现了奇数次，则接下来空堆会换位置：

1. 将 $x$ 放在空堆。

2. 持续按原来的套路消除。

3. 现在 $y$ 堆已经没有 $z$ 了，$y$ 通过顶部消除。

4. $y$ 堆空了，将空堆设为这个堆。

讲完了，$O(n)$ 代码实现非常有难度（对于我来说）。

```cpp
#define M 2000010
#define N 303
vector<pi> out;
struct node{
	int a[2],sz;
	void push(int x){
		if(sz && a[sz-1]==x) sz--;
		else a[sz++]=x;
	}
	void pop(){
		sz--;
		if(sz) a[0]=a[1];
	}
	int bot(){ return a[0]; }
	int top(){ return a[sz-1]; }
}t[N];
int n,m,k,tar,a[N],ta,pos[N<<1],pre[N<<1],p[M],cnt,L,R;
bool ban;//for tar
void ins(int x){
	if(pos[x]){
		assert(t[pos[x]].sz);
		if(t[pos[x]].top()==x){
			out.pb(0,pos[x]);
			t[pos[x]].push(x);
		}else if(t[pos[x]].bot()==x){
			assert(!ban);
			out.pb(0,tar);
			out.pb(pos[x],tar);
			t[pos[x]].pop();
		}else assert(0);
		if(t[pos[x]].sz==1) a[++ta]=pos[x];
		pos[x]=0;
		cnt--;
	}else{
		assert(ta);
		out.pb(0,a[ta]);
		t[a[ta]].push(x);
		pos[x]=a[ta];
		cnt++;
		if(t[a[ta]].sz==2) ta--;
	}
}
void work(){
	out.clear();
	cin>>n>>m>>k;
	rep(i,1,m) cin>>p[i];
	tar=n;//empty
	rep(i,1,n) t[i].sz=0;
	ta=n-1;
	iota(a+1,a+n,1);
	fill(pos+1,pos+1+k,0);
	cnt=0;
	L=1;
	ban=false;
	bool pan;
	int x,y,z,now;
	while(L<=m){
		x=p[L];
		if(!pos[x] && cnt==2*n-2){
			assert(!ta);
			R=L+1;
			while((y=p[R])!=p[L] && y==(z=t[now=pos[y]].top())){
				assert(now && t[now].sz==2);
				R++;
			}
			if(x==y){
				out.pb(0,tar);
				ban=true;
				rep(i,L+1,R-1) ins(p[i]);
				ban=false;
				out.pb(0,tar);
			}else{
				pan=false;
				rep(i,L+1,R-1) if(p[i]==z) pan^=1;
				if(pan){//odd
					out.pb(0,tar);
					ban=true;
					rep(i,L+1,R-1){
						if(p[i]==z){
							out.pb(0,now);
						}else{
							ins(p[i]);
						}
					}
					ban=false;
					out.pb(0,now);
					t[now].sz=0;
					pos[z]=0;
					pos[y]=0;
					pos[x]=tar;
					t[tar].push(x);
					a[++ta]=tar;
					tar=now;
					cnt--;
				}else{//even
					out.pb(0,now);
					ban=true;//虽然此时 tar 是空的，但确实没有 2 操作
					rep(i,L+1,R-1){
						if(p[i]==z){
							out.pb(0,now);
						}else{
							ins(p[i]);
						}
					}
					ban=false;
					out.pb(0,tar);
					out.pb(tar,now);
					t[now].pop();
					t[now].push(x);
					pos[y]=0;
					pos[x]=now;
				}
			}
			L=R+1;
		}else{
			ins(x);
			L++;
		}
	}
	assert(!cnt);
	cout<<siz(out)<<"\n";
	for(auto i:out)
		if(i.fir) cout<<"2 "<<i.fir<<" "<<i.sec<<"\n";
		else cout<<"1 "<<i.sec<<"\n";
}
```