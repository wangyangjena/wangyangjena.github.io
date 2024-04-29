---
layout: post
title: "根号算法"
date:   2024-04-29
tags: [Algorithm]
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



# 根号算法



## 根号平衡



> 维护一个集合，支持：
> O(1) 插入一个数
> O(sqrt(n)) 查询k小

对值域分块， 插入时这个数 x 所属的块大小++

查询时 O(sqrt(n)) 遍历前缀块， O(sqrt(n)) 遍历第 k 小的数所属的块



> 维护一个集合，支持
> O(sqrt(n))插入一个数
> O(1)查询k小

对这个数是第几小分块， 用类似 deque 的结构维护

插入时数 x 所属的块会多出一个元素， push 到下一个块中， 以此类推， 直到每个块大小不大于 sqrt(n)

查询时直接算出第 k 小的数的位置即可



## 普通莫队



### 复杂度分析



序列长为 n， 每个块大小为 x， m 个询问

同一个块中， L 的移动是 O(x) 的； 相邻块是 O(2x) 的

同一个块中， R 的移动是 O(n) 的； 相邻块是 O(n) 的

相邻块移动次数为 n / x

所以总复杂度为 $ O(mx\  +\  \frac{n}{x} *\ 2x +\ \frac{n}{x}  *\ n) $

用基本不等式求得下限为 $O(n\ sqrt(m))$， 当且仅当 $x = \frac{n}{sqrt(m)} $ 时等号成立



一些卡常技巧： 奇偶排序， 移动指针时压缩常数



## 树上莫队



维护树上路径的信息， 第一反应是 dfs 序， 但是一条路径上的 dfs 序可能是不连续的， 所以不可行

我们使用括号序来表示路径

​	点 x 第一次出现的位置为 fst[x]， 最后一次为 lst[x]

然后开始分类讨论

​	对于路径 (u, v) (fst[u] < fst[v]) ：

​		lca(u, v) = u： 括号序列的 [fst[u], fst[v]] 区间只出现一次的点都在路径上

​		lca(u, v) != u： 括号序列的 [lst[u], fst[v]] 区间只出现一次的点都在路径上， lca 的出现次数为 2， 但是也在路径上， 特别记录一下



## 带修莫队



所谓带修， 就是给普通莫队多加一维时间戳维度

如果当前时间戳 < 询问的时间戳， 就进行修改操作， 反之退回修改操作

复杂度 $ O(n ^ {\frac{5}{3}}) $ But 不知道咋得出来的



## 分块、莫队的块长



对于非带修莫队及分块， 块长可以由基本不等式推出

过程在莫队复杂度分析里有， **<u>*最佳块长为 sqrt(n*</u>**)



对于带修莫队， 我也不知道咋推出来的， **<u>*最佳块长为*</u>** $ O(n ^ {\frac{2}{3}}) $



因为有些题卡常， 所以知道最优块长挺重要的
