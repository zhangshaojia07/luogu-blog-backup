# CF508E题解

~~良心题解 管理员求过~~
## 做题历史
我在赛场上想都没想直接 DP

$O(n^3)$ 过了

但别人说正解是栈+贪心
## 讲讲 DP
设计状态（区间 DP）：

`bool dp[i][j]`表示**从第 $i$ 对括号至第 $j$ 对括号是否在最终答案中能变成一段连续的区间**

转移`check(a,b)`表示`a=(a||b)`：

* 可以第 $i$ 对括号中间包含第 $i+1$ 对括号至第 $j$ 对括号：

`check(dp[i][j],dp[i+1][j]);`

* 可以由多段相接的连续区间组成($i\leq k<j$): 

`check(dp[i][j],dp[i][k] && dp[k+1][j]);`

发现不用优化 $O(n^3)$ 就能过

（记得记录每个 `dp[i][j]` 是从哪里推过来的）

**ACcode**
```cpp
#include<bits/stdc++.h>
using namespace std;
#define ll long long
#define FOR(i,j,k) for(register int i=j;(j<k)?(i<=k):(i>=k);i+=(j<k)?1:(-1))
#define IO ios::sync_with_stdio(0),cin.tie(0),cout.tie(0)
#define N 666

int n,r;
bool dp[N][N];//从第i对括号至第j对是否能是一段连续区间 
int fang[N][N];//我从哪里推来~ 
pair<int,int> ku[N];
void dfs(int x,int y){
	if(x==y) cout<<"()";
	else if(fang[x][y]==-1){
		cout<<"(";
		dfs(x+1,y);
		cout<<")";
	}else{
		dfs(x,fang[x][y]);
		dfs(fang[x][y]+1,y);
	}
} 
signed main(){
    IO;
    memset(dp,0,sizeof dp);
    cin>>n;
    FOR(i,1,n)cin>>ku[i].first>>ku[i].second;
    FOR(i,1,n)dp[i][i]=(bool)(ku[i].first==1);
    FOR(i,2,n){
    	FOR(l,1,n-i+1){
    		r=l+i-1;
			//dp[l][r]准备就绪 
    		if(dp[l+1][r] && ku[l].first-1<=(r-l)*2 && (r-l)*2<=ku[l].second-1){
				dp[l][r]=1;
				fang[l][r]=-1; 
			}else{
    			FOR(k,l,r-1){//k~k+1之间为断点
					if(dp[l][k] && dp[k+1][r]){
						dp[l][r]=1;
						fang[l][r]=k;
						break;
					}
				}
			}
		}
	}
	if(dp[1][n]){
		dfs(1,n);
		cout<<endl;
	}else cout<<"IMPOSSIBLE"<<endl;
    return 0;
}
```
## 讲讲栈
这是一道括号匹配问题，因此我们考虑使用栈模拟。

因为两对括号要么包含要么相离，

所以每次存左括号，

右括号肯定优先匹配栈顶左括号。

剩下的就是模拟。