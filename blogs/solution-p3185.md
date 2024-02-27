# P3185 [HNOI2007]分裂游戏（SG 函数）

**Upd:** 感谢 [Little09](https://www.luogu.com.cn/user/151475) 把我 Hack（数据真的水），现已修改。

我们发现每一个豆子都是一个独立的游戏，所以我们将每一个豆子的 SG 异或和就可判断是否先手必胜。

而对于每一个位置上的豆子，若有偶数个则异或抵消，所以我们最后每个位置保留 $0/1$ 个豆子。

（以下下标从 $1$ 开始）

设 $c_i$ 表示 $i$ 位置上的一颗豆子的 SG 值，则答案为
$$
\sum_{i=1}^nc_i[a_i\equiv1\pmod2]
$$
同时有递推式
$$
c_i=\text{mex}\{c_j\ \text{xor}\ c_k\ |\ i<j\le k\le n\}
$$
最后枚举第一步来求出第一步如何必胜即可。

时间 $O(n^3)$。

```cpp
//We'll be counting stars.
//#pragma GCC optimize("Ofast")
#include<bits/stdc++.h>
using namespace std;
#define fir first
#define sec second
#define mkp make_pair
#define pb emplace_back
#define For(i,j,k) for(int i=(j),i##_=(k);i<=i##_;i++)
#define Rof(i,j,k) for(int i=(j),i##_=(k);i>=i##_;i--)
#define ckmx(a,b) a=max(a,b)
#define ckmn(a,b) a=min(a,b)
#define debug(...) cerr<<"#"<<__LINE__<<": "<<__VA_ARGS__<<endl
#define ll long long
const ll mod=1;
inline ll pw(ll x,ll y){ll r=1;while(y){if(y&1)r=r*x%mod;x=x*x%mod;y>>=1;}return r;}
#define int ll
#define N 25
#define M 215
int n,a[N],b[N][M],c[N];
void solve(int id,int val){
    For(i,id+1,n) For(j,i,n) if((c[i]^c[j])==val){
        cout<<id-1<<" "<<i-1<<" "<<j-1<<endl;
        return ;
    }
}
void work(){
    cin>>n;
    For(i,1,n) fill(b[i],b[i]+M,0);
    int x;
    Rof(i,n,1){
        For(j,i+1,n) For(k,j,n) b[i][c[j]^c[k]]++;
        x=0;
        while(b[i][x]) x++;
        c[i]=x;
    }
    // For(i,1,n) cout<<c[i]<<" "; cout<<endl;
    For(i,1,n) cin>>a[i];
    int tar=0;
    For(i,1,n) if(a[i]&1) tar^=c[i];
    if(tar==0){
        cout<<"-1 -1 -1\n0"<<endl;
        return ;
    }
    int ans=0;
    For(i,1,n) if(a[i]){
        if(ans==0 && b[i][c[i]^tar]!=0) solve(i,c[i]^tar);
        ans+=b[i][c[i]^tar];
    }
    cout<<ans<<endl;
}
signed main(){ios::sync_with_stdio(false),cin.tie(nullptr);
    int T;cin>>T;
    while(T--)work();
return 0;}
```