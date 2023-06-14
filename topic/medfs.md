由于我一直对搜索情有独钟，因此，如果能写记忆化搜索的绝不会写 `for` 循环 DP。
文章部分内容来自 $\texttt{OI-Wiki}$
## 引入
记忆化搜索是一种通过记录已经遍历过的状态的信息，从而避免对同一状态重复遍历的搜索实现方式。
因为记忆化搜索确保了每个状态只访问一次，它也是一种常见的动态规划实现方式。

我们通过下面一道题来引入。

> P1434 [SHOI2002] 滑雪
有一个 $R \times C$ 的二维矩阵，可以从某个点到达上下左右相邻四个点之一，当且仅当高度会减小，即这是一个下降序列。求这个下降序列长度的最大值。

我们的朴素 DFS 做法：

```cpp
#include <bits/stdc++.h>
using namespace std;
typedef long long ll;

inline ll read() {
	ll x = 0;
	int fg = 0;
	char ch = getchar();
	while (ch < '0' || ch > '9') {
		fg |= (ch == '-');
		ch = getchar();
	}
	while (ch >= '0' && ch <= '9') {
		x = (x << 3) + (x << 1) + (ch ^ 48);
		ch = getchar();
	}
	return fg ? ~x + 1 : x;
}

const int fx[4] = {0, 1, 0, -1};
const int fy[4] = {1, 0, -1, 0};

int r, c, maxn;
int a[110][110], f[110][110];

int dfs(int x, int y) {
	int xx, yy, ma = 0;
	for (int i = 0; i <= 3; ++i) {
		xx = x + fx[i];
		yy = y + fy[i];
		if (xx > 0 && xx <= r && yy > 0 && yy <= c && a[xx][yy] < a[x][y]) {
			ma = max(dfs(xx, yy), ma);
		}
	}
	return ma + 1;
}

int main() {
	r = read(), c = read();
	for (int i = 1; i <= r; ++ i)
		for (int j = 1; j <= c; ++ j) {
			a[i][j] = read();
		}
	for (int i = 1; i <= r; ++ i)
		for (int j = 1; j <= c; ++ j) {
			maxn = max(maxn, dfs(i, j));
		}
	printf("%d", maxn);
	return 0;
}
```

交上去一看，T 了一个点。
为什么 T 了呢？
我们假设 $(i, j)$ 这个点当前被搜到，继续搜，得到最大值，返回了。
后来，又一次搜到了 $(i, j)$ 这个点，然后又重新搜了一遍；再后来，又搜到了这个点，又重新搜了一遍......
因此，导致我们的这份代码跑得慢的原因就是多次进行同一个操作，搜索同一个变量。
为了提升速度，防止重复搜一种情况，我们设置记忆化数组来存储我们的值，同时阻止他继续重复搜索。

```cpp
#include <bits/stdc++.h>
using namespace std;
typedef long long ll;

inline ll read() {
	ll x = 0;
	int fg = 0;
	char ch = getchar();
	while (ch < '0' || ch > '9') {
		fg |= (ch == '-');
		ch = getchar();
	}
	while (ch >= '0' && ch <= '9') {
		x = (x << 3) + (x << 1) + (ch ^ 48);
		ch = getchar();
	}
	return fg ? ~x + 1 : x;
}

const int fx[4] = {0, 1, 0, -1};
const int fy[4] = {1, 0, -1, 0};

int r, c, maxn;
int a[110][110], f[110][110];

int dfs(int x, int y) {
	if (f[x][y])	return f[x][y];
	f[x][y] = 1;
	int xx, yy, ma = 0;
	for (int i = 0; i <= 3; ++i) {
		xx = x + fx[i];
		yy = y + fy[i];
		if (xx > 0 && xx <= r && yy > 0 && yy <= c && a[xx][yy] < a[x][y]) {
			ma = max(dfs(xx, yy), ma);
		}
	}
	f[x][y] += ma;
	return f[x][y];
}

int main() {
	r = read(), c = read();
	for (int i = 1; i <= r; ++ i) {
		for (int j = 1; j <= c; ++ j) {
			a[i][j] = read();
		}
	}	
	for (int i = 1; i <= r; ++ i) {
		for (int j = 1; j <= c; ++ j) {
			maxn = max(maxn, dfs(i, j));
		}
	}
	printf("%d\n", maxn);
	return 0;
}
```

然后，你就可以愉快的 AC 了！
由此你也发现了，记忆化搜索相较于一般搜索速度快是因为避免了对同一状态的重复遍历。

## 写记忆化搜索方法
### 方法一
1. 把这道题的 dp 状态和方程写出来
2. 根据它们写出 dfs 函数
3. 添加记忆化数组
### 方法二
1. 写出这道题的暴搜程序（最好是 dfs）
2. 将这个 dfs 改成**无需外部变量**的 dfs
3. 添加记忆化数组
## 与递推的区别
记忆化搜索和递推，都确保了同一状态至多只被求解一次。而它们实现这一点的方式则略有不同：递推通过设置明确的访问顺序来避免重复访问，记忆化搜索虽然没有明确规定访问顺序，但通过给已经访问过的状态打标记的方式，也达到了同样的目的。
与递推相比，记忆化搜索因为不用明确规定访问顺序，在实现难度上有时低于递推，且能比较方便地处理边界情况，这是记忆化搜索的一大优势。但与此同时，记忆化搜索难以使用滚动数组等优化，且由于存在递归，运行效率会低于递推。因此应该视题目选择更适合的实现方式。
## 题目
> P1220 关路灯
在一条路线上安装了 $n$ 盏路灯，每盏灯的功率有大有小（即同一段时间内消耗的电量有多有少）。老张就住在这条路中间某一路灯旁，他有一项工作就是每天早上天亮时一盏一盏地关掉这些路灯。他每天都是在天亮时首先关掉自己所处位置的路灯，然后可以向左也可以向右去关灯。现在已知老张走的速度为 $1m/s$，每个路灯的位置（是一个整数，即距路线起点的距离，单位：$m$）、功率（$W$），老张关灯所用的时间很短而可以忽略不计。问：怎样最省电？$n \le 50$

记忆化搜索代码：

```cpp
#include <bits/stdc++.h>
using namespace std;
typedef long long ll;

const int N = 60;

int n, c;
int s[N], w[N], sum[N];
int dp[N][N][2];

int dfs(int l, int r, int las) {
	if (~dp[l][r][las]) {
		return dp[l][r][las];
	}
	if (l == 1 && r == n) {
		return dp[l][r][las] = 0;
	}
	int minn = 1e9 + 5;
	if (las == 0) {
		if (l != 1 && r != n) {
			minn = min(dfs(l - 1, r, las) + (sum[n] - sum[r] + sum[l - 1])
				* (s[l] - s[l - 1]), dfs(l, r + 1, las ^ 1) + (sum[n] - 
					sum[r] + sum[l - 1]) * (s[r + 1] - s[l]));
		}
		else if (l != 1 && r == n) {
			minn = min(minn, dfs(l - 1, r, las) + (sum[n] - sum[r] + 
				sum[l - 1]) * (s[l] - s[l - 1]));
		}
		else if (l == 1 && r != n) {
			minn = min(minn, dfs(l, r + 1, las ^ 1) + (sum[n] - sum[r]
				+ sum[l - 1]) * (s[r + 1] - s[l]));
		}
	}
	else {
		if (l != 1 && r != n) {
			minn = min(dfs(l - 1, r, las ^ 1) + (sum[n] - sum[r] + sum[l - 1])
				* (s[r] - s[l - 1]), dfs(l, r + 1, las) + (sum[n] - 
					sum[r] + sum[l - 1]) * (s[r + 1] - s[r]));
		}
		else if (l != 1 && r == n) {
			minn = min(minn, dfs(l - 1, r, las ^ 1) + (sum[n] - sum[r] + 
				sum[l - 1]) * (s[r] - s[l - 1]));
		}
		else if (l == 1 && r != n) {
			minn = min(minn, dfs(l, r + 1, las) + (sum[n] - sum[r]
				+ sum[l - 1]) * (s[r + 1] - s[r]));
		}
	}
	return (dp[l][r][las] = minn);
}

int main() {
	ios::sync_with_stdio(false);
	cin.tie(0), cout.tie(0);
	memset(dp, -1, sizeof dp);
	cin >> n >> c;
	for (int i = 1; i <= n; ++ i) {
		cin >> s[i] >> w[i];
		sum[i] = sum[i - 1] + w[i];
	}
	cout << min(dfs(c, c, 0), dfs(c, c, 1)) << '\n';
	return 0;
}
```

> P3607 [USACO17JAN]Subsequence Reversal P
FJ 正在安排他的 $N$ 头奶牛排成一队以拍照 $(1 \le n \le 50)$。队列中的第i头奶牛的身高为 $a_i$，并且 FJ 认为如果奶牛的身高序列中含有一个很长的不下降子序列的话，这会是一张很好的照片。
回忆一下，子序列是由牛序列中的一些元素 $a_{i_1},a_{i_2},.....a_{i_k}$ 组成的子集。$(i_1<i_2< \cdots <i_k)$ 如果 $a_{i_1} \le a_{i_2} \le a_{i_3} \le \cdots \le a_{i_k}$ 的话，我们就说这个序列是不下降的。
FJ 想要在他的奶牛序列中包括一个长期增长的子序列（也就是很长的不下降子序列）。为了确保这一点，他允许自己在一开始选择任何子序列并逆转其元素。
观察这个子序列（上方英文）是如何反转并占据他们最初的相同的位置的，且其他元素保持不变。
在只能反转一次任意子序列的情况下，请找到不下降子序列的最大可能长度。

```cpp
#include <bits/stdc++.h>
using namespace std;
typedef long long ll;

const int N = 110;

int n;
int a[N], dp[60][60][60][60];
bool vis[60][60][60][60];

int dfs(int l, int r, int d, int u) {
	if (vis[l][r][d][u]) {
		return dp[l][r][d][u];
	}
	if (d > u)	return -1e8;
	if (l > r)	return 0;
	if (l == r) {
		if (d <= a[l] && a[r] <= u)	return dp[l][r][d][u] = 1;
		else	return 0;
	}
	dp[l][r][d][u] = max(dp[l][r][d][u], dfs(l + 1, r - 1, d, u));
	if (a[r] >= d) {
		dp[l][r][d][u] = max(dp[l][r][d][u], dfs(l + 1, r - 1, a[r], u) + 1);
	}
	if (a[l] <= u) {
		dp[l][r][d][u] = max(dp[l][r][d][u], dfs(l + 1, r - 1, d, a[l]) + 1);
	}
	if (a[l] <= u && a[r] >= d) {
		dp[l][r][d][u] = max(dp[l][r][d][u], dfs(l + 1, r - 1, a[r], a[l]) + 2);
	}
	dp[l][r][d][u] = max(dfs(l + 1, r, d, u), dp[l][r][d][u]);
	if (a[l] >= d) {
		dp[l][r][d][u] = max(dp[l][r][d][u], dfs(l + 1, r, a[l], u) + 1);
	}
	dp[l][r][d][u] = max(dfs(l, r - 1, d, u), dp[l][r][d][u]);
	if (a[r] <= u) {
		dp[l][r][d][u] = max(dp[l][r][d][u], dfs(l, r - 1, d, a[r]) + 1);
	}
	vis[l][r][d][u] = 1;
	return dp[l][r][d][u];
}

int main() {
	ios::sync_with_stdio(false);
	cin.tie(0), cout.tie(0);
	cin >> n;
	for (int i = 1; i <= n; ++ i) {
		cin >> a[i];
	}
	cout << dfs(1, n, 0, 50) << '\n';
	return 0;
}
```
