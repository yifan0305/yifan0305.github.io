## 扩展欧几里得算法
### 介绍
扩展欧几里得算法，常用来求像 $ax + by = c$ 这样的不定方程的一组可行解
### 解法
在此之前，我们可以确定 $c$ 一定是 $\gcd(a, b)$ 的倍数。
为什么？我们把原式分解一下
$ax + by$ 分解后，是$\gcd(a, b) \cdot m \cdot x + \gcd(a, b) \cdot n \cdot y$
其中
$\gcd(a, b) \cdot m = a\\ \gcd(a, b) \cdot n = b$
所以，$c$ 一定是 $\gcd(a, b)$ 的倍数
这道题转而可以转化成 $ax_1 + by_1 = \gcd(a, b)$
其中
$ax_1 = \dfrac{a \cdot \gcd(a, b)} {c}\\by_1 = \dfrac{b \cdot \gcd(a, b)}{c}$
由欧几里得定理可以得知
$\gcd(a, b) = \gcd(b, a \bmod b)$
所以 $ax_1 + by_1 = \gcd(a, b)$ 可以转化为 $bx_2 + (a \bmod b)y_2 = \gcd(b, a \bmod b)$
又因为 $\gcd(a, b) = \gcd(b, a \bmod b)$
所以 $ax_1 + by_1 = bx_2 + (a \bmod b)y_2$
又因为$a \bmod b = a - (\lfloor \frac{a}{b} \rfloor \times b)$
所以 $bx_2 + (a \bmod b)y_2 = bx_2 + (a - (\lfloor \frac{a}{b} \rfloor) \times b)y_2$
合并同类项得 $ay_2 + b(x_2 - \lfloor \frac{a}{b} \rfloor y_2)$
又因为 $a = a, b = b$
所以 $x_1 = y_2 , y_1 = x_2 - \lfloor \frac{a}{b} \rfloor y_2$
将 $x_2, y_2$ 不断带入递归，直到有返回值后在进行计算
### 代码

```cpp
void exgcd(int a, int b, int &x, int &y) {
	if (!b)	{
		x = 1;
		y = 0;
	}	
	else {
		exgcd(b, a % b, y, x);
		y -= (a / b) * x;
	} 
}
```

## 同余方程
类似于 $ax \equiv c \pmod y$ 这样的方程就是同余方程。
我们可以将同余方程转化为不定方程 $ax + by = c$，通过扩展欧几里得算法解得特解 $x_0, y_0$。
则 我们可以得到通解

$$
\begin{cases}
x = x_0 + \dfrac{b}{gcd(a, b)} \cdot t;\\
y = y_0 - \dfrac{a}{gcd(a, b)} \cdot t;\\
\end{cases}
$$

正确性？
你带入看看式子是否成立即可。
