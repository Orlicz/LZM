---
date: 2024.8.19
title: linear bu xing
---

linear 不是很行，得寸进尺，它甚至发我对它的话到犇犇！过分！

我以后要和他说一些无关痛痒的话 以及 dual 一些小题。万万不能劝告他“完蛋了”！

它甚至不会线段树二分！

还要我的 代码！

```cpp
#include<bits/stdc++.h>
using namespace std;
const int maxn = 2e5+10;

#define ls x<<1
#define rs ls|1
#define mid ((l+r) >> 1)
struct{
	int min;
}t[maxn << 2];
void upd(int x,int l,int r,int p,int v){
	if(l==r)return void(t[x]=v);
	if(p<=mid)upd(ls,l,mid,p,v);
	else upd(rs,mid+1,r,p,v);
	t[x]=min(t[ls],t[rs]);
}
int q_fir_les(int x,int l,int r,int p,int v){
	if(l==r || t[x].min>=v)return r+(t[x].min<v);
	if(mid<p)return q_fir_les(rs,mid+1,r,p,v);
	int la=q_fir_les(ls,l,mid,p,v);
	return la==mid+1?q_fir_les(rs,mid+1,r,p,v):la;
}

signed main() {
	ios::sync_with_stdio(0),cin.tie(0);

}
```
<!--stackedit_data:
eyJoaXN0b3J5IjpbMzYwNDc4MDkzXX0=
-->