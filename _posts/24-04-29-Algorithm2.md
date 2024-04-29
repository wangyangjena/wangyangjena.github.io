---
layout: post
title: "二项式反演"
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



# 二项式反演



## 式子 


$$
 f(m) = \sum_{i = m}^{n} C_m^i\ g(i)
$$

$$
 g(m) = \sum_{i = m}^{n} (-1) ^ {(i - n)}\  C_i^m\ f(i)
$$


$f(i)$ 表示钦定 i 个元素满足条件， 其他随意

$g(i)$ 表示只有 i 个元素满足条件， 其他的元素都不满足

n 表示总共 n 个元素， m 是上文中的 i



## 组合意义



式子 1： 每个 g(i) 都被 <= i 的 f(j) 算了 $C_i^j$ 次， 因为 f(j) 的含义为至少 j 个满足条件， g(i) 从满足条件的 i 个中任意选出 j 个都能对 f(j) 产生 1 的贡献



式子 2 ：容斥原理， 从 i 个中任选出 m 个都满足条件， 所以是 $C_i^m \ f(i)$， -1 的幂是容斥系数
