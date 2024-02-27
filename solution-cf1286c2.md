# CF1286C2题解

有任何关于此篇题解的问题皆可私信[我](https://www.luogu.com.cn/user/101868)。

本蒟蒻与 Dilute 大佬的想法~~相同~~类似，可以首先看看那一篇。

### 题目分析

当我们询问一段区间 $[l,r]$ 时，可以知道以区间中心点为对称轴，对称两数的值（但不知道对应关系），且当区间长度为奇数时，可以知道中点元素的值。

推理：

先设 $cnt[i][j]$ 表示字符 $j$ 在区间 $[l,r]$ 内所有长度为 $i$ 的子串中出现的次数。

例如：
$$str=\texttt{aabc}$$
长度为2的子串：
$$\texttt{aa,ab,bc}$$
则：
$$cnt[2]['a'-'a']=3$$
$$cnt[2]['b'-'a']=2$$
$$cnt[2]['c'-'a']=1$$

推出：

**字符 $j$ 在 $l+k$ 至 $r-k$ 区间内出现的次数为 $cnt[k+1][j]-cnt[k][j]$**。

非常好证，若字符 $j$ 不在此区间内，则对 $cnt[k+1][j]$ 和 $cnt[k][j]$ 的贡献相等（列表观察即可）。

**字符 $j$ 在位置 $l+k$ 和 $r-k$ 上出现的次数为 $2\times cnt[k+1][j]-cnt[k][j]-cnt[k+2][j]$**。

非常好证，前者结论错位相减即可。

我们可以预处理出所有 $cnt$ 判断字符 $j$ 在位置 $l+k$ 和 $r-k$ 上出现了几次，就知道以区间中心点为对称轴，对称两数的值（但不知道对应关系）。

我们回到原题，三次询问，所有子串的长度总和不能大于  $\lceil0.777(n+1)^2\rceil$。

有一种可行的方案：

1. 通过两次询问蚊香法（下文会讲）处理出前 $\frac{2}{3}$ 的元素的值。

2. 再一次后 $\frac{2}{3}$ 的询问通过前 $\frac{2}{3}$ 的元素的值推出剩下的值。

**蚊香法（自创的，不喜轻喷）：**

若想知道一段区间 $[l,r]$ （长度为奇数）内元素的定值，可两次询问：$[l,r]$ 和 $[l+1,r]$。

如下图询问 $[1,5]$ 和 $[2,5]$。

![](https://i.loli.net/2021/07/27/XeBnwpbhJD7KuvV.png)

其中每次询问得到的值可能左右对称位相反，已用蓝红线标出。

开始蚊香法（跟着用手指画一画）：

1. 由蓝线可知下标为 3 的值一定是 $\texttt{b}$。

2. 红线下标为 3 和 4 的值要互换。

3. 下标为 4 的值一定是 $\texttt{c}$。

4. 蓝线下标为 2 和 4 的值不用互换。

5. 下标为 2 的值一定是 $\texttt{a}$。

6. 红线下标为 2 和 5 的值不用互换。

7. 下标为 5 的值一定是 $\texttt{a}$。

8. 蓝线下标为 1 和 5 的值要互换。

9. 下标为 1 的值一定是 $\texttt{d}$。

10. 得出原序列为 dabca。

回顾一下过程：

![](https://i.loli.net/2021/07/27/S6tTex1vBuUkW5M.png)

像不像从中间点燃的蚊香？

所以，先将前 $k$ （为最接近 $\frac{2n}{3}$ 的奇数）个实行蚊香操作。

后面呢？

![](https://i.loli.net/2021/07/27/5Ro6DlaG9tqSkI3.png)

这样即可。

子串的个数约为 $0.666n^2$。

### Details

$n\leq3$ 时要特判。

计算 $k$ 时记得按 $n$ 对 $3$ 的模数分类讨论。

### ACcode

记得选 c++11.

```cpp
#pragma GCC optimize(2)
#include<bits/stdc++.h>
using namespace std;
#define ll long long
#define FOR(i,j,k) for(register int i=j;(j<k)?(i<=k):(i>=k);i+=(j<k)?1:(-1))
#define IO ios::sync_with_stdio(0),cin.tie(0),cout.tie(0)
#define N 110

pair<int,int> qry[3];//三个询问的区间
int n;
char val[N][3];//三次询问得到的答案 
int cnt[N][26];//cnt[i][j]（对于每次询问）字符(j+'a')在长为i的字符串中出现了几次 
string str;
void query(int x){//询问+处理 
	memset(cnt,0,sizeof cnt); 
	int l=qry[x].first;//询问区间左边界 
	int r=qry[x].second;//询问区间右边界 
	cout<<"? "<<l<<" "<<r<<endl;
	cout.flush();
	FOR(i,1,(r-l+1)*(r-l+2)/2){
		cin>>str;
		for(auto j:str){//不存str，直接遍历存入cnt 
			cnt[str.size()][j-'a']++;
		}
	}
	FOR(i,0,(r-l)/2){//枚举的上界：长度的一半 
		FOR(j,0,25){
			switch(2*cnt[i+1][j]-cnt[i][j]-cnt[i+2][j]){//(char)(j+'a')在原字符串中下标为l+i和r-i中出现了几次 
				case 0:break;
				//达咩 
				case 1:if(val[l+i][x])val[r-i][x]=j+'a';else val[l+i][x]=j+'a';break;
				//存入左右对称位置中任意空位置
				case 2:val[l+i][x]=val[r-i][x]=j+'a';break;
				//存入左右各一个 
			}
		} 
//		FOR(j,1,n)cout<<val[j][x]<<" ";cout<<endl;
	}
}
int mirror(int id,int layer){//id在第layer次询问中的对称位置 
	return qry[layer].second+qry[layer].first-id;
}
signed main(){
    IO;
    cin>>n;
    if(n<=3){//小数据特判 
    	string ans="";
    	FOR(i,1,n){
			cout<<"? "<<i<<" "<<i<<endl;//直接一个一个问 
			cout.flush();
			cin>>str;
			ans+=str;
		}
		cout<<"! "<<ans<<endl;
    	return 0;
	}
    if(n%3==0){//按n对3模数分类方案 
    	qry[0]=make_pair(1,n/3*2-1);
    	qry[1]=make_pair(2,n/3*2-1);
    	qry[2]=make_pair(n/3,n);
	}else if(n%3==1){
    	qry[0]=make_pair(1,n-(n-1)/3);
    	qry[1]=make_pair(2,n-(n-1)/3);
    	qry[2]=make_pair(n-(n-1)/3*2+1,n);
	}else{
    	qry[0]=make_pair(1,n-(n+1)/3);
    	qry[1]=make_pair(2,n-(n+1)/3);
    	qry[2]=make_pair((n+1)/3+1,n);
	}
//	cout<<qry[0].first<<" "<<qry[0].second<<endl;
//	cout<<qry[1].first<<" "<<qry[1].second<<endl;
//	cout<<qry[2].first<<" "<<qry[2].second<<endl;
	query(0);
//	FOR(i,1,n)cout<<val[i][0]<<" ";cout<<endl;
	query(1);
//	FOR(i,1,n)cout<<val[i][1]<<" ";cout<<endl;
	query(2);
//	FOR(i,1,n)cout<<val[i][2]<<" ";cout<<endl;
	int x=(qry[0].second+qry[0].first)/2,layer=0;
	while(x!=1){//蚊香式遍历处理 
//		cout<<x<<endl;
		layer^=1;//在0和1层之间反复横跳 
		if(val[x][layer]!=val[x][layer^1])swap(val[x][layer],val[mirror(x,layer)][layer]);
		x=mirror(x,layer);
	}
	//上面确定好了前两次询问的区间所有数的值 
	FOR(i,qry[2].first,qry[0].second){//用 前两次询问的区间所有数的值 确定第三次的 
		if(val[i][2]!=(val[i][0]|val[i][1]))swap(val[i][2],val[mirror(i,2)][2]);
	}
//	FOR(i,1,n)cout<<val[i][0]<<" ";cout<<endl;
//	FOR(i,1,n)cout<<val[i][1]<<" ";cout<<endl;
//	FOR(i,1,n)cout<<val[i][2]<<" ";cout<<endl;
	cout<<"! ";
	FOR(i,1,n)cout<<(char)(val[i][0]|val[i][1]|val[i][2]);//同位置的值不为零即相等 
	cout<<endl;
	cout.flush();
    return 0;
}
/*
测试数据string:ccba 
4
c
c
b
cc
cb
ccb
c
b
cb
b
a
ba
*/
```