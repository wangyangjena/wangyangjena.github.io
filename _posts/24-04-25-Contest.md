---
layout: post
title: "04-25 模拟赛总结"
date:   2024-04-25
tags: [Apr-mx, 模拟赛]
comments: true
author: wangyangjena

---



<head>
    <script src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML" type="text/javascript"></script>
    <script type="text/x-mathjax-config">
        MathJax.Hub.Config({
            tex2jax: {
            skipTags: ['script', 'noscript', 'style', 'textarea', 'pre'],
            inlineMath: [['$','$']]
            }
        });
    </script>
</head>



# 04-25 模拟赛总结



## T1



从图上扣出一个简单环， 其中的每一个点的入度出度都是 1， 把这个环删掉后原图仍满足任意点的 「入度 - 出度」 不变



所以可以考虑每次找一个简单环， 删掉它， 然后再找一个……



以上做法是 $O(n^2)$ 级别的



### Others



想到环， 自然想到 dfs 树上的返祖边， 所以建 dfs 树， 每次遇到返祖边 (u, v) 时就把 u 到 v 的树边全都删掉， 再把 (u, v) 这条返祖边也删掉



说起来不难， 写起来挺麻烦



### Summary



赛时有删环的想法， 但是没有把想法概括成 ‘删简单环’ 这么具体的做法



满分解法实现挺复杂的



```cpp
#include <bits/stdc++.h> 
#define int long long
using namespace std;
const int N = 1e6 + 10;

int n, m;
int u, v;

struct Edge{
	int v, id;
};
vector <Edge> e[N];

struct Rec{
	int u, v, id;
};
vector <Rec> rec;

int dfn[N], del[N], cdfn, id[N], cid;
int ins[N], vis[N];

stack <int> st;

void Dfs(int x){
	ins[x] = 1;
	st.push(x);
    // 先删后 dfs
	for(auto i : e[x]){
        // 这条边已经被删了 / 这个点不在栈里（还没有入边）
		if(del[i.id] || !ins[i.v]) continue;
		
        // 否则找到了返祖边 (x, i.v)， i.v 在 x 上面
        int tp = st.top();
		del[i.id] = 1;
        // 删掉中间的所有树边
		while(st.top() != i.v){
			tp = st.top(), st.pop();
			ins[tp] = 0;
		}
        // 先不要给最上面的点 i.v 出栈（他还能连接别的简单环）
		ins[i.v] = 0;
		return;
	}
	for(auto i : e[x]){
		if(del[i.id]) continue;
		if(ins[i.v] || vis[i.v]) continue;
		Dfs(i.v);
        // i.v 没有被删掉， continue
		if(ins[x]) continue;

        // 这条边被删掉了
		del[i.id] = 1;
		if(x != st.top()){
            // 该简单环设最上面的点是 y
            // 如果 x 不是最上面的点， 那么 y 到 x 的树边都删光了， 没法再构成新的简单环， return
			return;
		}else{
            // 如果 x == y, 那么还可能构成新的简单环， 入栈
			ins[x] = 1;
		}
	}
    // 如果 dfs 完了 x 的所有子树， 他还在栈里， 说明没有包含他的简单环了， 标记为 ‘安全’
	vis[x] = 1;
	ins[x] = 0, st.pop();
}

signed main(){
	// freopen("1.in", "r", stdin);
	freopen("dag.in", "r", stdin);
	freopen("dag.out", "w", stdout);
	ios::sync_with_stdio(0);
	cin.tie(0); cout.tie(0);

	cin >> n >> m;
	for(int i = 1; i <= m; i++){
		cin >> u >> v;
		e[u].push_back({v, i});
		rec.push_back({u, v, i});
	}
	for(int i = 1; i <= n; i++){
		if(!vis[i]){
			Dfs(i);
		}
	}

	int cnt = 0;
	for(auto i : rec){
		if(!del[i.id]){
			cnt++;
		}
	}
	cout << cnt << "\n";
	for(auto i : rec){
		if(!del[i.id]){
			cout << i.u << " " << i.v << "\n";
		}
	}
}
```



## T3



#### 45 pts



枚举 i， j， 连边， 跑 Dijkstra



复杂度 $O(n^2\ m\ log\ m)$



### Others



#### 55 pts



以 s， t 分别为起点， 算出正反的最短路



枚举所有点对 i， j， $dis = dis_s[i]\ +\ dis_t[j]\ + (i - j)^2  $



复杂度 $O(m\ log\ m + n ^ 2)$



#### 100 pts



我们只需要把 55 pts 中的 $n^2$ 枚举部分优化一下就行



对于一个点 i， 他到终点的距离为 $ ans_i $


$$
ans_i\ =\ dis_s[i] + dis_t[j] + (i - j) ^ 2
$$

$$
ans_i = dis_s[i] + dis_t[j] + i^2 + j ^ 2 - 2ij
$$


我们把外层循环枚举不到的（包含 j ）的看作 y、 x


$$
dis_t[j] + j^2 = 2ij + ans_i - i^2 - dis_s[i]
$$

$$
x = j\\ y = dis_t[j] + j^2\\ k = 2i\\ b = ans_i - i^2 - dis_s[i]
$$


其中 k 是单调递增的， 我们要使得截距 b 最小， 相当于维护了一个下凸包， 类似斜率优化计算即可



### Summary



斜率优化不是非常熟练， 没想到正解



但是 55 pts 的也没有想到， 非常不应该



但是 45 pts 的又出了点 bug， 挂了 10 pts， 太不应该了啊啊啊



```cpp
#include <bits/stdc++.h> 
#define int long long
#define double long double
using namespace std;
const int INF = 1e18;
const int N = 1e6 + 10;

int n, m, s, t;
int u, v, w;

struct Edge{
	int v, w;
};
vector <Edge> e[N], ee[N];

struct Node{
	int id, dis;
	bool operator < (const Node &p)const{
		return dis > p.dis;
	}
}tp;
priority_queue <Node> q;

bool done[N];
int disa[N], disb[N];

void Dijkstra(int st, int dis[N], vector <Edge> e[N]){
	for(int i = 1; i <= n; i++){
		done[i] = 0;
		dis[i] = INF;
	}
	dis[st] = 0;
	q.push({st, 0});
	while(!q.empty()){
		tp = q.top(), q.pop();
		if(done[tp.id]){
			continue;
		}else{
			done[tp.id] = 1;
		}
		for(auto i : e[tp.id]){
			if(dis[i.v] > dis[tp.id] + i.w){
				dis[i.v] = dis[tp.id] + i.w;
				q.push({i.v, dis[i.v]});
			}
		}
	}
}

int ans[N];
int que[N], l, r;

int Y(int x){
	return disb[x] + x * x;
}

int X(int x){
	return x;
}

int K(int x){
	return 2 * x;
}

double F(int x, int y){
	return 1.0 * (Y(x) - Y(y)) / (X(x) - X(y));
}

signed main(){
	// freopen("1.in", "r", stdin);
	freopen("far.in", "r", stdin);
	freopen("far.out", "w", stdout);
	ios::sync_with_stdio(0);
	cin.tie(0); cout.tie(0);

	cin >> n >> m >> s >> t;
	for(int i = 1; i <= m; i++){
		cin >> u >> v >> w;
		e[u].push_back({v, w});
		ee[v].push_back({u, w});
	}
	Dijkstra(s, disa, e), Dijkstra(t, disb, ee);

	l = 1, r = 0;

	for(int i = 1; i <= n; i++){
		while(l < r && F(que[r], que[r - 1]) >= F(i, que[r])){
			r--;
		}
		que[++r] = i;
	}

	int mini = INF;
	for(int i = 1; i <= n; i++){
		while(l < r && F(que[l + 1], que[l]) <= K(i)){
			++l;
		}
		ans[i] = disa[i] + disb[que[l]] + (que[l] - i) * (que[l] - i);
		mini = min(mini, ans[i]);
	}
	cout << mini << "\n";
}
```

