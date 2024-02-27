# CF1623D Robot Cleaner Revisit

### Analysis

下文用 $p$ 代替题目中的 $\dfrac{p}{100}$，表示每秒机器人清扫的概率。

期望的一般求法：$\sum\limits_{i}p_i\times v_i$，其中 $p_i$ 表示这种情况发生的概率，$v_i$ 表示这种情况的代价（或价值，本题中指时间）。

容易发现机器人走动的路线会以 $cy=2\times\operatorname{lcm}(n-1,m-1)
$ 秒时间为循环节，由于 $cy\leqslant 2\times10^5$，我们暴力遍历 $0\to(cy-1)$ 秒，依次记录有可能能清理垃圾的时间 $t_0,t_1,\dots,t_{k-1}$。

$t_i$ 秒正好清扫垃圾的概率为 $(1-p)^{i}p$，表示前面 $i$ 次机会都没把握住而这次把握了。

但是答案并不是 $tans=\sum\limits_{i=0}^{k-1}(1-p)^{i}pt_i$ 这么简单，因为有可能一次路线循环后机器人仍然没有清扫垃圾。

但是观察接下来的期望公式：

第二轮时：

$$\begin{aligned}
&\sum\limits_{i=0}^{k-1}(1-p)^{i+k}p(t_i+cy)
\\=&(1-p)^{k}\sum\limits_{i=0}^{k-1}(1-p)^{i}p(t_i+cy)
\\=&(1-p)^{k}(tans+cy\cdot p\sum\limits_{i=0}^{k-1}(1-p)^{i})
\end{aligned}$$

第 $L$ 轮时：

$$\begin{aligned}
&\sum\limits_{i=0}^{k-1}(1-p)^{i+(L-1)k}p(t_i+(L-1)cy)
\\=&(1-p)^{(L-1)k}(tans+(L-1)cy\cdot p\sum\limits_{i=0}^{k-1}(1-p)^{i})
\end{aligned}$$

将每一轮的结果累加即为答案（使用等比公式，设 $tmp=(1-p)^k$ 表示一轮路径没有清扫的概率）：

$$\begin{aligned}
&\sum\limits_{L=1}^{\infty}(1-p)^{(L-1)k}(tans+(L-1)cy\cdot p\sum\limits_{i=0}^{k-1}(1-p)^{i})
\\&=\sum\limits_{L=0}^{\infty}tmp^Ltans+\sum\limits_{L=0}^{\infty}L\cdot tmp^Lcy\cdot p\sum\limits_{i=0}^{k-1}(1-p)^{i}
\\&=tans\sum\limits_{L=0}^{\infty}tmp^L+cy\cdot p(\sum\limits_{i=0}^{k-1}(1-p)^{i})\sum\limits_{L=0}^{\infty}L\cdot tmp^L
\\&=\dfrac{1}{1-tmp}tans+cy(1-(1-p)^{k})\sum\limits_{L=0}^{\infty}L\cdot tmp^L
\end{aligned}$$

其中

$$\sum\limits_{L=0}^{\infty}L\cdot tmp^L
=\sum\limits_{i=1}^{\infty}tmp^i(\sum\limits_{L=0}^{\infty}tmp^L)
=\dfrac{\sum\limits_{i=1}^{\infty}tmp^i}{1-tmp}
=\dfrac{\dfrac{1}{1-tmp}-1}{1-tmp}
=\dfrac{tmp}{(1-tmp)^2}$$

### Detail

注意数学细节。

### Code

```cpp
void work(){
	t.clear();
	cin>>n>>m>>pos.fir>>pos.sec>>v.fir>>v.sec>>p;
	mmu(p,inv(100));//mod mul,inv 为乘法逆元
	cy=2*lcm(n-1,m-1);
	s=mkp(1,1);//make_pair
	For(i,0,cy-1){
		if(pos.fir==v.fir || pos.sec==v.sec) t.pb(i);//push_back
		if(pos.fir==n && s.fir==1) s.fir*=-1;
		if(pos.fir==1 && s.fir==-1) s.fir*=-1;
		if(pos.sec==m && s.sec==1) s.sec*=-1;
		if(pos.sec==1 && s.sec==-1) s.sec*=-1;
		pos.fir+=s.fir;
		pos.sec+=s.sec;
	}
   //此处为简化代码，计算过程与推导不同
	int ans=0,tmp=pw((mod+1-p)%mod,t.size()),ss=(mod+1-p)%mod;//pw:quick power
	int tot=0;
	for(int i:t){
		ans+=pw(ss,tot)*p%mod*i%mod;
		tot++;
	}
	mad(ans,cy);//mod add
	mmu(ans,inv((mod+1-tmp)%mod));
	mad(ans,-cy);
	cout<<ans<<endl;
} 
```