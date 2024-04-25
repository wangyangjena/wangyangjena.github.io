---
layout: post
title: "04-24 模拟赛总结"
date:   2024-04-25
tags: [模拟赛]
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

# 04-24 模拟赛总结



## T1



写个分数类， 把方差的括号拆开来， 用 __int128  硬算即可



### Others

推式子

只有方差这里可能爆 i64， 原因是两个 1e10 级别的互质分母一通分就炸了

$$
对于方差： 原式 = \frac{1}{i}(\sum A_j ^ 2 - 2 * \sum A_j B_i + \sum B_i ^ 2)
$$

$$
= \frac{1}{i} (\sum A_j ^ 2 - 2B_i\sum A_j + iB_i^2)
$$

$$
 = \frac{1}{i}\sum A_j ^ 2 - 2B_iB_i + B_i^2
$$

$$
= \frac{1}{i} \sum A_j^2 - B_i ^ 2
$$

这样一来， 本来通分会炸的东西化没了， 所以 i64 也可过



### Summary



做题的时候大体算一算结果的范围， 决定开不开 __int128 或者再优化 / 重新想， 有时候结果范围不同做法可能也不同（做到这样的题再更）



## T2



### Others



画图解决



判断是否匹配的一种做法就是做前缀和， '(' + 1， ')'  - 1， 要求是 sum 时刻 >= 0 并且结束时 = 0

那么在图上， 对于每个点， 能匹配上的点对满足如下条件 ： 

​	中间没有比他们更大的

这样， 第 i 组右括号的一段前缀就能和他匹配， 形成一个新区间

​	注意：如果有多个高度相同的段， 要把他们加在一起， 因为每一个都是合法的

用一个单调栈维护， pop 是累加上面所述的情况的答案

栈里剩下的**<u>*那个元素*</u>**一定能使第 i 组的右括号全都匹配上， 形成 c[i]  个新区间

如果栈为空， 即 $sum_i <= mini$ 则 i 低于 mini 的那部分匹配不上， 没有贡献， 因为在 pop 时 mini 这组的答案已经加上了， 所以在这里**<u>*再减去 1*</u>** （我们假设算重了的是最后的 1 个， 即上一段所指的 '最后一个'）



### Summary



括号匹配的几种思路

1. 数形结合， 如本题的图像
2. 用栈贪心的匹配， + Dp 计算区间个数（我的暴力）



## T3



### Others



#### 60 pts

35 pts 的思路是 dp[i]\[a]\[b]\[c] 表示第 i 位， 最后四位的后三位是 $a_a, a_b, a_c$ （变量重名了哈哈哈）， 由 dp[j]\[a']\[b']\[c'] (j < i) 转移过来

然后就发现 dp[i]\[a]\[b]\[c] 除了选了第 i 位的是 dp[i]\[a]\[b][i] 之外其余都是从 i - 1 继承过来的， 而选第 i 位只会发生一次， dp[i]\[a]\[b] 也完全可以表示原来状态的含义， 所以去掉一维即可

dp[i]\[a]\[b] 由 「 $\sum dp[a][b][c] $ 并且 i, a, b, c 位置的异或值不为 s 」转移过来， 因为和 i, a, b 异或值为 s 的数只有一个， 所以对每个 dp[i] 求和， 算的时候再减去那一个不合法的即可



#### 100 pts



所有元素都不相等就挺有意思

因为这样的话容斥起来不会多算， 简单

$$
dp[i][j] = \sum_{j < i \&\& k < j} dp[j][k] - \sum_{a_i\ xor\ a_j xor\ a_k xor \ a_l xor\ a_m == s \ \ l < k\  \&\& m < l} dp[l][m]
$$

$$
xor 是可以移项的， a_i\ xor\ a_j\ xor\ a_k\ xor\ a_l\ xor\ a_m\ = s 即 a_i\ xor\ a_j\ xor\ s\ =  a_l\ xor\ a_m
$$

以上的东西可以开个数组维护， 详情看代码



### Summary



% MOD 这个东西很慢， 对于加法， 我们可以

```cpp
x = x > MOD ? x - MOD : x;
```



dp 的转移通常是可以有暴力一步一步优化的， 所以写完暴力之后想想能不能再优化
