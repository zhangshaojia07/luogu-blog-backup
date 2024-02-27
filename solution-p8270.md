# P8270 [USACO22OPEN] Subset Equality S

### Analysis

以下 $n$ 表示串长，$\Sigma$ 表示字符集。

每次询问线性扫描肯定是吃不消的。

我们考虑预处理。

------------

**结论：** 若 $s,t$ 两个字符串中**每种字符出现的次数相同**，且**任意两种字符** $a,b$，均使得 $s,t$ 中提取出 $a,b$（顺序保持不变）的**子序列相同**，则 $s=t$。

**证明：** 假设存在满足上述条件且不同的两个字符串 $s,t$。找到两者第一个不同的位置 $x$。设 $s_x=a,t_x=b(a\ne b)$，则将 $s,t$ 中字符 $a,b$ 分别提取后这一位上是第一个不同的位置，矛盾。假设不成立，原命题得证。

------------

所以，我们可以 $O(|\Sigma|^2n)$ 预处理出 $s,t$ 中提取任意两个字符 $a,b$ 后是否相等，然后 $O(T|\Sigma|^2)$ 求出每个询问的答案。

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
#define ll long long
const ll mod=1000000007;
inline ll pw(ll x,ll y){ll r=1;while(y){if(y&1)r=r*x%mod;x=x*x%mod;y>>=1;}return r;}
inline void mad(ll &a,ll b){a=(a+b)%mod;while(a<0)a+=mod;}
inline void mmu(ll &a,ll b){a=a*b%mod;while(a<0)a+=mod;}
#define inv(a) pw(a,mod-2)
#define int long long
#define N -1

string s,t,ss,tt,q;
bool a[18][18];
int cnt[18]; 
bool check(int x,int y){
	ss=tt="";
	for(char i:s) if(i-'a'==x || i-'a'==y) ss=ss+i;
	for(char i:t) if(i-'a'==x || i-'a'==y) tt=tt+i;
	return ss==tt;
}
signed main(){IOS;
	int T,n;
	bool flag;
	cin>>s>>t>>T;
	for(char i:s) cnt[i-'a']++;
	for(char i:t) cnt[i-'a']--;
	For(i,0,16) For(j,i+1,17) a[i][j]=check(i,j);
	while(T--){
		cin>>q;
		sort(q.begin(),q.end());
		n=q.size();
		flag=1;
		for(char i:q) if(cnt[i-'a']!=0){
			cout<<'N';
			flag=0; 
			break;
		}
		if(!flag) continue;
		For(i,0,n-2){For(j,i+1,n-1) if(!a[q[i]-'a'][q[j]-'a']){
			cout<<'N';
			flag=0;
			break;
		}
		if(!flag) break;
		}
		if(flag) cout<<'Y';
	}
	cout<<endl;
return 0;}
```