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

称 $\lambda(U)$ 为 $\lambd(U,V\setminus U)$
<!--stackedit_data:
eyJoaXN0b3J5IjpbMjY2NzE0NDI5XX0=
-->