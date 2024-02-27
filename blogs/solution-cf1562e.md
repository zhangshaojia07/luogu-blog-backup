# CF1562E题解

### 题目分析

先设原串中以 $x$ 位置为开头的所有子串的集合为 $sw(x)$，$s(x,y)$ 表示原串中从 $x$ 到 $y$ 的子串。

由于同一个 $x$ 对应的 $sw(x)$ 在最后的 LIS 里出现的一定是连续的，

而且一定有一种最优方案使得若 $sw(x)$ 中存在一个串在 LIS 中出现过，则 $s(x,n)$ 也出现过，（证明先鸽着，有需要的催我）

所以设 `dp[i]` 表示以 $s(i,n)$ 为结尾的最长上升子序列的长度。

线性枚举 `dp[i]` 是从哪个 `dp[j]`$(j<i)$ 推过来。

### Details

先 init 出 `lcp[i][j]`，即 $i$ 和 $j$ 的最长公共后缀的长度，便于决策。

### ACcode

复杂度 $O(Tn^2)$。

```cpp
#include<bits/stdc++.h>
using namespace std;
template<class T> inline void ckmx(T &a,T b){ if(a<b)a=b; }
#define IOS ios::sync_with_stdio(0);cin.tie(0);cout.tie(0)
#define For(i,j,k) for(register int i=j;i<=k;i++)
#define Rof(i,j,k) for(register int i=j;i>=k;i--)
#define N 5050

int T,n;
string s;
int ans;
int lcp[N][N];//i和j的最长公共后缀的长度 
int dp[N];//以[i,n]为结尾的最长上升子序列的长度 
bool bigger(int x,int y){//以x为开头的后缀 是否比 以y为开头的后缀 大（x>y） 
	if(x+lcp[x][y]==n)return false;
	else return s[x+lcp[x][y]]>s[y+lcp[x][y]];
}
int contri(int x,int y){//j更新i 
	if(bigger(x,y)) return dp[y]+n-x-lcp[x][y];
	else return 0;
}
signed main(){
	IOS;
	cin>>T;
	while(T--){
		cin>>n;
		cin>>s;
		For(i,0,n)For(j,0,n)lcp[i][j]=0;//归零 
		Rof(i,n-1,0)
			Rof(j,n-1,0)
				if(i==j)
					lcp[i][j]=n-i;//之后都相等 
				else if(s[i]==s[j])
					lcp[i][j]=lcp[i+1][j+1]+1;//递推 
		ans=n;
		dp[0]=n;
		For(i,1,n-1){
			dp[i]=n-i;
			For(j,0,i-1)
				ckmx(dp[i],contri(i,j));
			ckmx(ans,dp[i]);
		}
		cout<<ans<<endl;
	} 
    return 0;
}

```