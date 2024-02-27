# P8187 [USACO22FEB] Robot Instructions S

### Preface

妈妈再也不用担心我使用 `unordered_map` 了，因为被卡。

### Analysis

由于 $2^N$ 接受不了，而 $2^{N/2}$ 可以，想到 meet in middle。

处理出只但不必全使用前一半的指令能到达的位置和需要的步数，同理处理出哪些点使用后一半指令能够到达终点 $g$（也就是说从 $g$ 倒着处理）。

这样两部分点集数量均为 $2^{20}$，我们将他们按平面上的位置排序（任意偏序关系），只有两个点集中同一位置上的点才能构成答案。使用双指针即可 AC。

### Code

```cpp
//Said no more counting dollars. We'll be counting stars.
#include<bits/stdc++.h>
using namespace std;
#define IOS ios::sync_with_stdio(0);cin.tie(0);cout.tie(0)
#define fir first
#define sec second
#define mkp make_pair
#define pb emplace_back
#define For(i,j,k) for(int i=j;i<=k;i++)
#define Rof(i,j,k) for(int i=j;i>=k;i--)
#define int long long
#define Pos pair<int,int>
#define Tup pair<Pos,int>
#define N 50
const int inf=1e18;
Pos a[N],t,tar=mkp(inf,inf);
int cnt[N],ans[N];
vector<Tup> b,c,cur;
int gap,n;
inline Tup mkt(int x,int y,int z){return mkp(mkp(x,y),z);}
signed main(){IOS;
	cin>>n;
	cin>>t.fir>>t.sec;
	For(i,1,n) cin>>a[i].fir>>a[i].sec;
	gap=n>>1;
	b.pb(mkt(0,0,0));
	For(i,1,gap){
		cur.clear();
		for(auto j:b) cur.pb(mkt(j.fir.fir+a[i].fir,j.fir.sec+a[i].sec,j.sec+1));
		for(auto j:cur) b.pb(j);
	}
	sort(b.begin(),b.end());
	c.pb(mkp(t,0));
	For(i,gap+1,n){
		cur.clear();
		for(auto j:c) cur.pb(mkt(j.fir.fir-a[i].fir,j.fir.sec-a[i].sec,j.sec+1));
		for(auto j:cur) c.pb(j);
	}
	sort(c.begin(),c.end());
	auto it=c.begin();
	for(auto i:b){
		if(i.fir!=tar){
			tar=i.fir;
			For(j,0,n) cnt[j]=0;
			while(it!=c.end() && it->fir<i.fir) it++;
			while(it!=c.end() && it->fir==i.fir){
				cnt[it->sec]++;
				it++;
			}
		}
		For(j,i.sec,n) ans[j]+=cnt[j-i.sec];
	}
	For(i,1,n) cout<<ans[i]<<endl;
return 0;}
```