---
title: 最小割树
date: 2024.8.21
---


太难了，我不会。但是 2016国家集训队论文中有 “浅谈。。。” 的一篇 p67左右。

应当认真阅读。

[P4214 [CERC2015] Juice Junctions](https://www.luogu.com.cn/problem/P4214)

本题等价于询问连通度，这个是经典的，直接网络流即可。

然而，我们发现可以使用最小割树求解任意两点最小割。就好了。

```cpp
//no know min-cut tree why
#include<bits/stdc++.h>
using namespace std;
const int maxn = 3e3+10, maxm = 4.5e3+10;
int n,m;
struct{
	int s,t;
	int head[maxn],ec=1;
	struct { int nxt,v,w; }edge[maxm << 1];
	void clear(){memset(head+1,0,n*sizeof head[0]);n=0,ec=1;}
	void add(int u,int v,int w){edge[++ec]={head[u],v,w};head[u]=ec;}
	int cur[maxn],dep[maxn],q[maxn],hd,tl;
	bool bfs(){
		memset(dep+1,0,n * sizeof dep[0]);
		dep[q[hd=tl=1]=s] = 1;
		while(hd <= tl) {
			int u = q[hd++];
			for(int i = cur[u] = head[u];i;i=edge[i].nxt) {
				int v = edge[i].v, w = edge[i].w;
				if(!dep[v] && w) dep[q[++tl] = v] = dep[u]+1;
			}
		}return dep[t];
	}
	int dfs(int u,int in){
		if(u == t) return in;
		int out=0;
		for(int&i=cur[u];i;i=edge[i].nxt){
			int v = edge[i].v,w = edge[i].w;
			if(w && dep[v] == dep[u]+1) {
				int to = dfs(v,min(in,w));
				if(!to) dep[v] = 0;
				in -= to, out += to;
				edge[i].w -= to, edge[i^1].w += to;
				if(!in) return out;
			}
		}return out;
	}
	int dinic(int S,int T) {
		for(int i=2;i<=ec;++i)edge[i].w=1;
		s = S, t = T;
		int ans = 0;
		while(bfs()) ans += dfs(s,INT_MAX);
		return ans;
	}
}f;

vector<pair<int,int> >es[4];
void adds(int u,int v,int w){es[w].emplace_back(u,v);}

int a[maxn];
void build(int l,int r){
	if(l==r)return;
	int flw=f.dinic(a[l],a[r]);
	adds(a[l],a[r],flw);
	int mid=partition(a+l,a+r+1,[](auto k){return f.dep[k];})-a-1;
	build(l,mid),build(mid+1,r);
}

int fa[maxn];
int gfa(int x){return fa[x]<0?x:fa[x]=gfa(fa[x]);}
bool mer(int u,int v){u=gfa(u),v=gfa(v);return u==v?0:(fa[u]>fa[v]&&(swap(u,v),1),fa[u]+=fa[v],fa[v]=u);}


signed main() {
	ios::sync_with_stdio(0),cin.tie(0);
	cin>>n>>m;for(int i=1,u,v;i<=m;++i)cin>>u>>v,f.add(u,v,1),f.add(v,u,1);
	iota(a+1,a+n+1,1),build(1,n);
	int res=0;
	memset(fa,-1,sizeof fa);
	for(int w:{3,2,1})
		for(auto[u,v]:es[w])
			res+=w*fa[gfa(u)]*fa[gfa(v)],
			mer(u,v);
	cout<<res;
}
```

---

我现在又会了！！！

---

### 最小割树

对于一个图 $G(V,E)$，称 $\lambda(u,v)$ 为 $u-v-\min cut$ 的边集。不区分一个集合与其权值总和。

称 $\lambda(U)$ 为 $\lambda(U,V\setminus U)$。

$\lambda(U)$ 是关于 $U$ 的 submod 函数。其中 submod 函数 $F$ 定义为：

$$
F(U_1)+F(U_2)\ge F(U_1\cup U_2)+F(U_1 \cap U_2)
$$

这就是anti四边形不等式：交叉劣于包含。

---
### 最小割树

对于一个图 $G(V,E)$，称 $\lambda(u,v)$ 为 $u-v-\min cut$ 的边集。不区分一个集合与其权值总和。

称 $\lambda(U)$ 为 $\lambda(U,V\setminus U)$。

$\lambda(U)$ 是关于 $U$ 的 $\operatorname{sub-mod}$ 函数。其中 submod 函数 $F$ 定义为：

$$
F(U_1)+F(U_2)\ge F(U_1\cup U_2)+F(U_1 \cap U_2)
$$

这就是anti四边形不等式：交叉劣于包含。

---

证明：调整法

<svg width="300px"height="300px"version="1.1"id="svg19"xmlns="http://www.w3.org/2000/svg"xmlns:svg="http://www.w3.org/2000/svg"><defs id="defs19"/><g id="g19"><g id="g13"><g id="g2"><path d="M 64.41644360834192 236.12169428647124 L 63.878305713526245 64.41644360834192"fill="none"stroke-width="2"stroke="orange"id="path1"/><path d="M 64.41644360834192 236.12169428647124 L 63.878305713526245 64.41644360834192"opacity="0"fill="none"stroke-width="30"stroke="orange"id="path2"/><text font-size="15px"dy="0.35em"text-anchor="middle"style="user-select: none;"x="73.50005578994751"y="150.2301014482946"id="text2">1</text></g><g id="g4"><path d="M 64.41644360834192 236.12169428647124 L 236.12169428647124 235.58355639165552"fill="none"stroke-width="2"stroke="orange"id="path3"/><path d="M 64.41644360834192 236.12169428647124 L 236.12169428647124 235.58355639165552"opacity="0"fill="none"stroke-width="30"stroke="orange"id="path4"/><text font-size="15px"dy="0.35em"text-anchor="middle"style="user-select: none;"x="150.0730813368088"y="223.42405891418457"id="text4">2</text></g><g id="g6"><path d="M 235.58355639165552 63.878305713526245 L 63.878305713526245 64.41644360834192"fill="none"stroke-width="2"stroke="orange"id="path5"/><path d="M 235.58355639165552 63.878305713526245 L 63.878305713526245 64.41644360834192"opacity="0"fill="none"stroke-width="30"stroke="orange"id="path6"/><text font-size="15px"dy="0.35em"text-anchor="middle"style="user-select: none;"x="149.53588578235411"y="51.71878910064697"id="text6">4</text></g><g id="g8"><path d="M 235.58355639165552 63.878305713526245 L 236.12169428647124 235.58355639165552"fill="none"stroke-width="2"stroke="orange"id="path7"/><path d="M 235.58355639165552 63.878305713526245 L 236.12169428647124 235.58355639165552"opacity="0"fill="none"stroke-width="30"stroke="orange"id="path8"/><text font-size="15px"dy="0.35em"text-anchor="middle"style="user-select: none;"x="245.20529413223267"y="149.69185965316137"id="text8">5</text></g><g id="g10"><path d="M 63.878305713526245 64.41644360834192 L 236.12169428647124 235.58355639165552"fill="none"stroke-width="2"stroke="orange"id="path9"/><path d="M 63.878305713526245 64.41644360834192 L 236.12169428647124 235.58355639165552"opacity="0"fill="none"stroke-width="30"stroke="orange"id="path10"/><text font-size="15px"dy="0.35em"text-anchor="middle"style="user-select: none;"x="159.35267877578735"y="137.5714282989502"id="text10">3</text></g><g id="g12"><path d="M 235.58355639165552 63.878305713526245 L 64.41644360834192 236.12169428647124"fill="none"stroke-width="2"stroke="orange"id="path11"/><path d="M 235.58355639165552 63.878305713526245 L 64.41644360834192 236.12169428647124"opacity="0"fill="none"stroke-width="30"stroke="orange"id="path12"/><text font-size="15px"dy="0.35em"text-anchor="middle"style="user-select: none;"x="140.64732122421265"y="137.64895463569366"id="text12">6</text></g></g><g id="g18"><g fixed="false"style="cursor: pointer;"id="g14"><circle stroke-width="2"fill="white"stroke="black"r="30"cx="64.41644360834192"cy="236.12169428647124"id="circle13"/><text font-size="14"dy=".35em"text-anchor="middle"stroke-width="1"fill="black"stroke="black"x="64.41644360834192"y="236.12169428647124"style="user-select: none;"opacity="1"id="text13">A∩B</text></g><g fixed="false"style="cursor: pointer;"id="g15"><circle stroke-width="2"fill="white"stroke="black"r="30"cx="63.878305713526245"cy="64.41644360834192"id="circle14"/><text font-size="14"dy=".35em"text-anchor="middle"stroke-width="1"fill="black"stroke="black"x="63.878305713526245"y="64.41644360834192"style="user-select: none;"opacity="1"id="text14">A\B</text></g><g fixed="false"style="cursor: pointer;"id="g16"><circle stroke-width="2"fill="white"stroke="black"r="30"cx="236.12169428647124"cy="235.58355639165552"id="circle15"/><text font-size="14"dy=".35em"text-anchor="middle"stroke-width="1"fill="black"stroke="black"x="236.12169428647124"y="235.58355639165552"style="user-select: none;"opacity="1"id="text15">B\A</text></g><g fixed="false"style="cursor: pointer;"id="g17"><circle stroke-width="2"fill="white"stroke="black"r="30"cx="235.58355639165552"cy="63.878305713526245"id="circle16"/><text font-size="14"dy=".35em"text-anchor="middle"stroke-width="1"fill="black"stroke="black"x="235.58355639165552"y="63.878305713526245"style="user-select: none;"opacity="1"id="text16">V\{A,B}</text></g></g></g><rect style="fill:none;stroke:#ff0000;stroke-width:2.09328;stroke-linecap:round;stroke-linejoin:miter;stroke-dasharray:none;stroke-opacity:1"id="rect1"width="82.906723"height="264.50189"x="26.065836"y="14.056974"/><rect style="fill:none;stroke:#0cff00;stroke-width:2;stroke-linecap:round;stroke-linejoin:miter;stroke-dasharray:none;stroke-opacity:1"id="rect2"width="264.59518"height="85"x="14.810926"y="188.13879"/></svg> 

容易发现 $\lambda(A)+\lambda(B)=\#4+\#6+\#3+\#2+\#1+\#5+\#6+\#3$，然后 交加并 显然更小。


---

$\lambda$ 还是对称的。显然。

---

$\lambda$ 是 $\operatorname{postive-mode}$ 的。什么叫做 pos mod 呢？就是
$$
\lambda(A)+\lambda(B)\ge \lambda(A\setminus B)+\lambda(B\setminus A)
$$
这个你固然可以把上面的图再用一边，但是国家队论文不能这样简单，需要更加高深莫测。调整的方法也很容易：

$submod+sym\to posmod$，为什么呢？因为
$$
\def\f{\lambda}
\f(A)+\f(B)=\f(\overline A)+\f(B)
$$

$$
\ge \f(\overline A\cap B)+\f(\overline A \cup B)
$$

$$
=\f(B\setminus A)+\f(A\setminus B)
$$

我们可能觉得这种推导对于这个问题是没有什么意义的；但是不能看得出来这种分析的不必要。因为事实既是如此，sub mod=>pos mod 其中必有联系；同时，这两种结构在广泛的图论结构、集合结构中出现：这就是某种凸性！



换言之，这还有另外的名字：sub mod就是广义的四边形不等式；pos mod 就是广义的区间包含单调性。

---

现在我们尝试应用凸优化的方法处理 min-cut 的结构。

---

我们的梦想是，

<div style="white-space:pre;font-family:Consolas;color:#333333;background-color:#fcfdfd;-moz-tab-size:4;tab-size:4;"><span style="color:#c695c6;font-style:italic;">int</span> a[maxn]<span style="color:#ac7a68;">;</span><br><span style="color:#c695c6;font-style:italic;">void</span> <span style="color:#5fb4b4;">build</span>(<span style="color:#c695c6;font-style:italic;">int</span> <span style="color:#f9ae58;">l</span><span style="color:#ac7a68;">,</span><span style="color:#c695c6;font-style:italic;">int</span> <span style="color:#f9ae58;">r</span>){<br>	<span style="color:#c695c6;">if</span>(l<span style="color:#f97b58;">==</span>r)<span style="color:#c695c6;">return</span><span style="color:#ac7a68;">;</span><br>	<span style="color:#c695c6;font-style:italic;">int</span> flw<span style="color:#f97b58;">=</span>f<span style="color:#f97b58;">.</span><span style="color:#6699cc;">dinic</span>(a[l]<span style="color:#ac7a68;">,</span>a[r])<span style="color:#ac7a68;">;</span><br>	<span style="color:#6699cc;">adds</span>(a[l]<span style="color:#ac7a68;">,</span>a[r]<span style="color:#ac7a68;">,</span>flw)<span style="color:#ac7a68;">;</span><br>	<span style="color:#c695c6;font-style:italic;">int</span> mid<span style="color:#f97b58;">=</span><span style="color:#6699cc;">partition</span>(a<span style="color:#f97b58;">+</span>l<span style="color:#ac7a68;">,</span>a<span style="color:#f97b58;">+</span>r<span style="color:#f97b58;">+</span><span style="color:#f9ae58;">1</span><span style="color:#ac7a68;">,</span>[](<span style="color:#c695c6;font-style:italic;">int</span> k){<span style="color:#c695c6;">return</span> f<span style="color:#f97b58;">.</span>dep[k]<span style="color:#ac7a68;">;</span>})<span style="color:#f97b58;">-</span>a<span style="color:#f97b58;">-</span><span style="color:#f9ae58;">1</span><span style="color:#ac7a68;">;</span><br>	<span style="color:#6699cc;">build</span>(l<span style="color:#ac7a68;">,</span>mid)<span style="color:#ac7a68;">,</span><span style="color:#6699cc;">build</span>(mid<span style="color:#f97b58;">+</span><span style="color:#f9ae58;">1</span><span style="color:#ac7a68;">,</span>r)<span style="color:#ac7a68;">;</span><br>}</div>

通过这个代码就可以建立最小割树，其中 adds 函数是最小割树的边；a 数组一开始等于 1~n。

这个代码若是正确的，则最小割需要具有这样的结构：
$$
\forall s,t,U,u,v\mid \f(U)=\lambda(s,t)
$$

$$
\exists W \in U\mid \f(u,v)=\f(W)
$$

[](文化大统一！原始论文中的 $\f,\alpha,F,c$ 等函数十分复杂，而我仅仅使用同一个函数就表示了其中的所有含义！)

然后事实上这个也是正确的。我们直接讨论一些局面：不妨设 $s\in W$（反正都一样）

1. $s\in W,t\in \overline W$

    ![image-20240822162414747](C:\Users\DELL\AppData\Roaming\Typora\typora-user-images\image-20240822162414747.png)

    易见 $\lambda(u,v)=\f(W)\ge \lambda(s,t)=\f(U)$ 同时却又 $\f(W)+\f(U)\ge \f(U\cap W)+\f(U\cup W)$

    翰文指出可以缩点是显然的，但是这样子文字形式化是困难的，所以我们只能用 sub=mod 性质，然后 $U \cup W$ 明显是 $\operatorname{s-t-cut}$，所以联立不等式马上就有我们只保留 $U\cap W$ 不劣。

2. $s\in W,t\in W$。

    这种情况我们考虑 $W'=U\setminus W$，对它而言，$\f(W'\cup U)\ge\lambda(U)$，所以 $\f(W')+\f(U)\ge\f(W'\cup U)+\f(W'\cap U)$，然后所以 $\f(W'\cap U)\le \f(W)$，所以易见 $W'\cap U$ 更好。图没有必要画。

就这样。翰文指出 递归左子树时直接对 $\overline U$ 缩点即可，反之亦然。这个他从流的角度。



就好了。缩点的话一个有点时图比较均匀时可能会好得。显然这个算法可以非递归，此时缩点复杂度时有改进的。



就是这样的一个道理。



<!--stackedit_data:
eyJoaXN0b3J5IjpbMTU0MDA3NDM5NCwtMTIzNzQ3NjU1NiwtMT
IwMDI0MTU4Nl19
-->