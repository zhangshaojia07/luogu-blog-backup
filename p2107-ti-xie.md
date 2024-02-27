# P2107题解

### 题目分析

显然 $n\leq 10^5$ **带悔贪心**。

先假设从 0 开始向右经过的所有机房全 AK，直到时间不够用时，选取所有经过的机房中耗时最多的放弃，不断更新答案即可。

### details

对于我这种不~~太~~会写堆的蒟蒻来说每次时间不够排序就完事了。

### code

~~我没开 long long 见祖宗了三回~~

写堆时间复杂度 $O(nlogn)$。

```cpp
#include<bits/stdc++.h>
using namespace std;
#define ll long long
#define For(i,j,k) for(register ll i=j;i<=k;i++)
#define io ios::sync_with_stdio(0),cin.tie(0),cout.tie(0)
#define N 100010
inline void ckmx(ll &a,ll b){if(a<b)a=b;}

ll n,m,tim=0,tot=0,ans=0;
pair<ll,ll> a[N],b[N];
bool cmp(pair<ll,ll> x,pair<ll,ll> y){return x.second<y.second;}
signed main(){
    io;
    cin>>n>>m;
    For(i,1,n)cin>>a[i].first>>a[i].second;
	sort(a+1,a+1+n);
	For(i,1,n){
		tim+=a[i].first-a[i-1].first+a[i].second;
		b[++tot]=a[i];
		if(tim>m){
			sort(b+1,b+1+tot,cmp);
			while(tot>0 && tim>m){
				tim-=b[tot--].second;
			}
		}
		if(tim>m)break;
		ckmx(ans,tot);
	}
	cout<<ans<<endl;
    return 0;
}
```