---
layout: post
title: "04-24 模拟赛总结"
date:   2024-04-24
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



```cpp
#include <bits/stdc++.h> 
#define int long long
#define i64 __int128
using namespace std;
const int N = 1e5 + 10;

int n;
int a[N];

void write(int x){
    if(x < 0) putchar('-'), x = -x;
    if(x > 9) write(x / 10);
    putchar(x % 10 + '0');
    return;
}


i64 Gcd(i64 a, i64 b){
	if(!b) return a;
	return Gcd(b, a % b);
}

struct Num{
	i64 a, b;	// a/b

	Num operator + (const int x)const{
		i64 aa = x * b + a;
		i64 gcd = Gcd(aa, b);
		return {aa / gcd, b / gcd};
	}

	Num operator + (const Num x)const{
		i64 lcm = b * x.b / Gcd(b, x.b);
		i64 aa = a * (lcm / b) + x.a * (lcm / x.b);
		i64 gcd = Gcd(aa, lcm);
		return {aa / gcd, lcm / gcd};
	}

	Num operator * (const Num x)const{
		i64 aa = a * x.a, bb = b * x.b;
		i64 gcd = Gcd(aa, bb);
		return {aa / gcd, bb / gcd};
	}

	Num operator * (const int x)const{
		i64 aa = a * x, gcd = Gcd(aa, b);
		return {aa / gcd, b / gcd};
	}
}sm[N], ave[N], fc[N], temp, tmp, ma;

void Print(Num x){
	if(x.b == 1) write(x.a);
	else write(x.a), putchar('/'), write(x.b);
}

signed main(){
	// freopen("1.in", "r", stdin);
	freopen("math.in", "r", stdin);
	freopen("math.out", "w", stdout);	
	ios::sync_with_stdio(0);
	cin.tie(0); cout.tie(0);

	scanf("%lld", &n); // cin >> n;
	for(int i = 1; i <= n; i++){
		scanf("%lld", &a[i]); // cin >> a[i];
	}
	i64 sum = 0, pw = 0;
	for(int i = 1; i <= n; i++){
		ma = {1ll, i};
		pw += a[i] * a[i];

		sum += a[i];
		sm[i] = {sum, 1};

		ave[i] = ma * sum;

		temp = ave[i] * ave[i] * i;
		tmp = ave[i] * sum * 2, tmp.a = -tmp.a;
		fc[i] = temp * ma + tmp * ma + ma * pw;
	}
	for(int i = 1; i <= n; i++){
		Print(sm[i]), putchar(' ');
		Print(ave[i]), putchar(' ');
		Print(fc[i]), putchar('\n');
	}
}
```





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



```cpp
#include <bits/stdc++.h> 
#define int long long
using namespace std;
const int MOD = 998244353;
const int N = 1e7 + 10;

int n, x, y, z, m[2], c[N];
int mini, sum, ans;

struct Node{
	int sum, cnt;
};
vector <Node> st;

signed main(){
	// freopen("1.in", "r", stdin);
	freopen("algorithm.in", "r", stdin);
	freopen("algorithm.out", "w", stdout);	
	ios::sync_with_stdio(0);
	cin.tie(0); cout.tie(0);

	cin >> n >> x >> y >> z;
	cin >> m[0] >> m[1] >> c[0] >> c[1];

	st.push_back({0, 1}), mini = 0;
	for(int i = 0; i < n; i++){
		if(i > 1){
			c[i] = (c[i - 1] * x + c[i - 2] * y + z) % m[i % 2] + 1;
		}
		// cout << c[i] << " ";
		if(i % 2 == 0){
			sum += c[i];
		}else{
			sum -= c[i];
			while(!st.empty() && st.back().sum > sum){
				ans += st.back().cnt;
				st.pop_back();
			}

			if(!st.empty() && st.back().sum == sum){
				ans += st.back().cnt;
				st.back().cnt++;
			}else{
				st.push_back({sum, 1});
			}
			if(sum <= mini){
				// cout << "Case1 ";
				ans += (c[i] - (mini - sum) - 1);
			}else{
				// cout << "Case2 ";
				ans += c[i];
			}
			mini = min(mini, sum);
			// cout << ans << "ans ";
		}
		// cout << sum << "sum\n";
	}
	cout << ans << "\n";
}
```





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



```cpp
#include <bits/stdc++.h> 
#define int long long
using namespace std;
const int MOD = 998244353;
const int N = 4e3 + 10;

int n, m, s;
int a[N], pos[N];
int dp[1 << 12][1 << 12];
int sum[1 << 12], tot[N][1 << 12];

void Mod(int &a){
	a = a > MOD ? (a - MOD) : a;
}

signed main(){
	// freopen("1.in", "r", stdin);
	freopen("count.in", "r", stdin);
	freopen("count.out", "w", stdout);	
	ios::sync_with_stdio(0);
	cin.tie(0); cout.tie(0);

	cin >> n >> m >> s;
	for(int i = 1; i <= n; i++){
		cin >> a[i];
	}

	int ans = 0, up = (1 << m) - 1;
	dp[0][0] = 1, sum[0] = 1;
	for(int i = 1; i <= n; i++){
		dp[i][0] += sum[0];
		sum[i] += dp[i][0];
		for(int j = 1; j < i; j++){
			int num = (a[i] ^ a[j] ^ s);
			dp[i][j] += sum[j] - tot[j - 1][num];
			dp[i][j] = dp[i][j] % MOD + MOD;
			tot[i][a[i] ^ a[j]] += dp[i][j];
			sum[i] += dp[i][j];
		}
		for(int j = 0; j <= up; j++){
			tot[i][j] += tot[i - 1][j], Mod(tot[i][j]);
		}
		ans = (ans + sum[i]) % MOD;
	}
	cout << ans << "\n";
}
```

