---
title: Dynamic-Programming
abbrlink: ef1e907a
date: 2020-08-04 17:02:27
tags:
	- LeetCode
	- Array
	- Dynamic-Programming
description: 动态规划相关题解
---

## 动态规划

一般流程: 暴力的递归解法 -> 带备忘录的递归解法 -> 迭代的动态规划解法

思考流程: 找到状态和选择 -> 明确 dp 数组/函数的定义 -> 寻找状态之间的关系

最优子结构:



### 问题形式

动态规划问题一般形式是求最值, 核心问题是==穷举==.

- 动态规划的穷举存在**重叠子问题**, 需要 dp 或者 备忘录 来避免不必要的计算.
- 动态规划问题一定具有**最优子结构**, 才能通过子问题的最值得到原问题的最值.
- 穷举依赖于正确的**状态转移方程**.

### 思维框架

明确状态 -> 定义dp数组/函数的含义 -> 明确 选择 -> 明确 base case

### 例题

递归算法的时间复杂度, 子问题的时间复杂度✖️子问题个数

子问题个数: 递归树中的节点数

### [509] Fibonacci Number

**暴力递归**

子问题个数: 二叉树, 指数级别, $O(2^N)$

子问题时间: $O(1)$

所以暴力穷举法的时间复杂度是指数级别, 主要问题是存在大量重复计算.

```javascript
/* 暴力穷举法 */
var fib1 = function (N) {
  if (N == 0 || N == 1) return N;
  return fib(N - 1) + fib(N - 2);
};
```



**带备忘录的递归解法**

将计算过的子问题的答案记录到备忘录中, 后面可以直接拿出来用, 一般使用数组或字典充当备忘录.

```javascript
/* 备忘录法 */
// 将计算过的结果记录下来
var fib = function (N) {
  let note = {};
  return helper(N, note);
};

var helper = (N, note) => {
  if (N == 0 || N == 1) return N;
  if (note.hasOwnProperty(N)) {
    return note[N];
  } else {
    let res = helper(N - 1, note) + helper(N - 2, note);
    note[N] = res;
    return res;
  }
}
```

由递归树可得, 自顶向下计算时, ,每个 `F(N)` 只需要计算一遍, 其他节点的计算均可以复用结果.

因此时间复杂度是: 子问题的个数✖️子问题的复杂度, 由于子问题的计算没有什么循环, 因此为常数, 个数是N的数量, 因此总共的时间复杂度是 $O(N)$ .

> **自顶向下**: 画递归树时, 由上往下画, 直至碰到 base case 触底, 然后逐层返回结果.
>
> **自底向上**: 得到最小问题的答案, 然后层层向上推演计算, 直至得到目标答案. **动态规划**思想, 因此动态规划一般都使用循环迭代得到答案.



**DP 数组的迭代解法**

```javascript
/* dp 数组解法 */
var fib = (N) => {
  let dp = new Array(N + 1);
  // base case
  dp[0] = 0;
  dp[1] = 1;
  for (let i = 2; i <= N; i++) {
    dp[i] = dp[i - 1] + dp[i - 2];
  }
  return dp[N];
}
```

计算结果时, 只需要两个值, 因此只用存储两个值就可以了, 空间优化后的代码.

```javascript
var fib = (N) => {
  let dp = new Array(2);
  // base case
  dp[0] = 0;
  dp[1] = 1;
  for (let i = 2; i <= N; i++) {
    let res = dp[0] + dp[1];
    dp[0] = dp[1];
    dp[1] = res;
  }
  return N == 0 ? dp[0] : dp[1];
}
```

**状态转移方程**

$f(N)=\begin{cases}0,\ N=0 \\ 1,\ N=1 \\ f(N-1) + f(N-2),\ N>1 \end{cases}$



### [322] Coin Change

求最值问题.

**暴力穷举**, 找到所有可能, 广度优先遍历, 找到最短路径长度即是解.

但是答案显示超时.

```javascript
// 暴力解法超时
var coinChange = function (coins, amount) {
  let queue = [amount];
  if (amount == 0) return 0;
  return bfs(queue, coins, 0)
};

var bfs = (queue, coins, depth) => {
  let n = queue.length;
  if (n == 0) return -1;
  for (let i = 0; i < n; i++) {
    let num = queue.shift();
    for (let j of coins) {
      let left = num - j;
      if (left == 0) return depth + 1;
      if (left > 0) queue.push(left);
    }
  }
  return bfs(queue, coins, depth + 1);
}
```

本题是动态规划问题, 因为具有最优子结构, 因为子问题互相独立, 由自顶向下递归树可知. 当目标金额是11 时, 只要凑到 10 的硬币最少, 再加 1 即是解(有点困惑).

> 分析过程

**状态**: 原问题与子问题中变化的量, 这里是目标金额 `amount`.

`dp` 函数含义: `dp(n)` 目标金额为 `n` 时, 所需要的最少硬币数.

**选择并择优**: 对于每一个状态, 可以做出什么选择改变当前状态. 本题就是 `coins` 中挑选硬币, 然后目标金额减去该面值.

**base case**: 当目标金额为 0 时, 硬币数量为 0, 目标金额小于 0 时, 无解返回 -1.



对于 $dp(n)$ 来说, 假设其中包含的最后一个硬币的面值是 `c`, 则有以下公式成立:

$dp(n) = dp(n-c) + 1$

但是并不知道 `c` 的具体值, 于是我们需要从 `coins` 中遍历, 找到 $min( dp(n-coins[i]) )$, 最后得到答案.

因此状态转移方程是:

$dp(n) = \begin{cases} 0,\ n=0 \\ -1,\ n<0 \ 或者 \ coins \ 为空 \\ min(dp(n-coins[i]))+1, \ n>0 \end{cases}$

> 代码

**备忘录解法(递归)**

自顶向下. 记得将返回值为 -1 的 target 也写进备忘录中.

```javascript
// 备忘录
var coinChange = function (coins, amount) {
  let note = {}
  return helper(amount, coins, note);
};

var helper = (target, coins, note) => {
  if (target == 0) return 0;
  if (target < 0) return -1;
  if (note.hasOwnProperty(target)) return note[target];
  let min = Infinity;
  for (let coin of coins) {
    let subPro = helper(target - coin, coins, note);
    if (subPro < 0) continue;
    min = Math.min(min, subPro + 1);
  }
  note[target] = !Number.isFinite(min)? -1 : min;
  return note[target];
}
```

**dp数组解法(迭代)**

自底向上.

需要注意的是, 当 $dp[i - coin] < 0$ 时, 不更新 `min` . 只有大于 0 时, 才需要更新 `min` .

目标金额为 `amount` 时, 最多由 `amount` 个硬币组成, 因此初始化数组大小为 `amount + 1` 即可计算每个目标值的最少硬币数.

```javascript
// dp 迭代解法
var coinChange = function (coins, amount) {
  let dp = new Array(amount + 1);
  dp[0] = 0;
  for (let i = 1; i <= amount; i++) {
    let min = Infinity;
    for (let coin of coins) {
      if (i - coin < 0) continue;
      if (dp[i - coin] >= 0) // 注意区分 >0
        min = Math.min(min, dp[i - coin] + 1);
    }
    dp[i] = !Number.isFinite(min) ? -1 : min;
  }
  return dp[amount];
};
```



### [10] Regular Expression Matching

只要经过不同的路径到达同一个节点, 就是**重叠子问题**.

可以使用定性分析法, 看同一个 $dp(i, j)$ 会不会通过不同的路径得到, 如果有, 就是重叠子问题, 就能通过动态规划解决.

时间复杂度: 假设 `text` 长度为 $m$ , `pattern` 长度为 $n$ , 由于 $i$ 与 $j$ 可能任意两两组合, 但是每个组合仅仅计算一次, 计算结果会被存储进备忘录中, 因此时间复杂度为 $O(mn)$ .

```javascript
// 进行子问题的优化
// 使用备忘录 存在重叠子问题 递归解法
var isMatch = (text, pattern) => {
  let note = {};
  var dp = (i, j) => {
    let key = i + '/' + j;
    if (!note.hasOwnProperty(key)) {
      if (j >= pattern.length) return i >= text.length;
      let firstMatch = i < text.length && (pattern[j] == '.' || pattern[j] == text[i]);
      if (j < pattern.length - 1 && pattern[j + 1] == '*') {
        note[key] = firstMatch && dp(i + 1, j) || dp(i, j + 2);
      } else {
        note[key] = firstMatch && dp(i + 1, j + 1);
      }
    }
    return note[key];
  }
  return dp(0, 0);
}
```



### [300] Longest Increasing Subsequence

动态规划的核心思想是**数学归纳法**. 假设 $dp[i-1]$ 已经得到结果, 如何推演出 $dp[i]$ 的结果, 但是需要先知道 $dp[i]$ 的含义.

这道题中, $dp[i]$ 表示以 $nums[i]$ 结束的子串中, 最长递增子序列的长度.

计算 $dp[i]$ 的过程为: 看 $nums[i]$ 能不能加在 $nums[j] \ (j\in[0, i-1])$ 后面 ( 即是否 $nums[i] > nums[j])$, 如果可以, 则 $dp[i] = dp[j] +1$, 但是 $dp[i]$ 取的是所有接入后的子序列中,  最长的子序列长度, 所以 $dp[i] = max(dp[j]+1)$.

时间复杂度: 由于有双层循环, 时间复杂度为 $O(N^2)$.

```javascript
var lengthOfLIS = (nums) => {
  let n = nums.length;
  if (n == 0) return 0; // 特殊情况
  // 初始化 dp 数组
  let dp = new Array(n);
  dp.fill(1);
  // 最后返回的结果-最大值
  let res = 1;
  for (let i = 1; i < n; i++) {
    let max = 1;
    for (let j = 0; j < i; j++) {
      if (nums[j] < nums[i]) {
        max = Math.max(dp[j] + 1, max);
      }
    }
    // dp[i] 的值是所有接入后子序列中最大的长度
    dp[i] = max;
    res = Math.max(res, max);
  }
  return res;
}
```









#### [62] Unique Paths

> A robot is located at the top-left corner of a *m* x *n* grid (marked 'Start' in the diagram below).
>
> The robot can only move either down or right at any point in time. The robot is trying to reach the bottom-right corner of the grid (marked 'Finish' in the diagram below).
>
> How many possible unique paths are there?



##### Solution 1: 排列组合

因为机器到底右下角，向下几步，向右几步都是固定的，

比如，m=3, n=2，我们只要向下 1 步，向右 2 步就一定能到达终点。

所以结果= (m+n−2)!/((m−1)!*(n-1)!).

```javascript
// solution 1 : 排列组合
// 使用数学上的排列组合计算公式
var uniquePaths1 = function (m, n) {
  // 起点到终点一共需要走 m-1+n-1 = m+n-2 步
  // 其中从总步数中选出向右的 m-1 步
  return jc(m + n - 2) / (jc(m - 1) * jc(n - 1))
};

var jc = (num) => {
  let res = 1;
  for (let i = 1; i <= num; i++) {
    res = res * i;
  }
  return res;
}
```



##### Solution 2: 动态规划

关键在于写出状态转移方程.很多解法用的是数组,我这里用的是对象存储.

数组的空间O(mn)可优化为O(n).

```javascript
// solution 2 : 动态规划
// 假设到点(i,j)的所有路径数表示为 dp(i,j)
// 状态转移方程
// dp(i,j) = dp(i-1,j) + dp(i,j-1)
var uniquePaths2 = function (m, n) {
  let dp = {};
  // 只能向右或向下
  // 所以到达边界的点的方法数=1
  dp[key(0, 0)] = 1;
  for (let i = 1; i < m; i++) {
    dp[key(i, 0)] = 1;
  }
  for (let j = 1; j < n; j++) {
    dp[key(0, j)] = 1;
  }
  // 时间复杂度O(mn)
  for (let i = 1; i < m; i++) {
    for (let j = 1; j < n; j++) {
      dp[key(i, j)] = dp[key(i - 1, j)] + dp[key(i, j - 1)];
    }
  }
  return dp[key(m - 1, n - 1)]
};

var key = (i, j) => {
  return i + '/' + j
}
```



#### [63] Unique Paths II

> A robot is located at the top-left corner of a *m* x *n* grid (marked 'Start' in the diagram below).
>
> The robot can only move either down or right at any point in time. The robot is trying to reach the bottom-right corner of the grid (marked 'Finish' in the diagram below).
>
> Now consider if some obstacles are added to the grids. How many unique paths would there be?

这里需要修改状态转移方程, 就是, 在计算某一点可到达的路径数时, 如果这一点有障碍, 则为0, 否则按照原来的等式计算.

在初始化到达边缘点的路径数时, 也要考虑障碍的情况.

感觉动态规划主要有两个过程, 重点还是**分析清楚过程**:

1. **初始化数据**
2. **正确的动态规划方程**

```javascript
var uniquePathsWithObstacles = function (obstacleGrid) {
  let rowNum = obstacleGrid.length;
  let colNum = obstacleGrid[0].length;
  let dp = {};

  let flag1 = true;
  for (let i = 0; i < rowNum; i++) {
    // 考虑障碍的情况
    // 边缘一有障碍, 则后面所有点的可到达路径数均为0
    if (obstacleGrid[i][0] == 1) {
      for (let t = i; t < rowNum; t++) {
        dp[key(t, 0)] = 0;
      }
      flag1 = false;
      break;
    }
    dp[key(i, 0)] = (flag1 == true) ? 1 : 0;
  }

  let flag2 = true;
  for (let j = 0; j < colNum; j++) {
    if (obstacleGrid[0][j] == 1) {
      for (let t = j; t < colNum; t++) {
        dp[key(0, t)] = 0;
      }
      flag2 = false;
      break;
    }
    dp[key(0, j)] = (flag2 == true) ? 1 : 0;
  }

  for (let i = 1; i < rowNum; i++) {
    for (let j = 1; j < colNum; j++) {
      // 考虑障碍的动态规划方程
      if (obstacleGrid[i][j] == 1)
        dp[key(i, j)] = 0;
      else
        dp[key(i, j)] = dp[key(i - 1, j)] + dp[key(i, j - 1)];
    }
  }
  // console.log(dp[key(rowNum - 1, colNum - 1)]);
  return dp[key(rowNum - 1, colNum - 1)];
};

var key = (i, j) => {
  return i + '/' + j;
}
```



#### [300] Longest Increasing Subsequence

> Given an unsorted array of integers, find the length of longest increasing subsequence.

**动态规划**

时间复杂度:

遍历数组, 遍历到每个元素时, 再次遍历该元素之前的所有元素. $O(N^2)$.

空间复杂度:

使用额外数组保存每个元素的最长子串的长度. $O(N)$ .

```javascript
// solution 1: 使用动态规划 时间复杂度 O(N^2)
// 后一个元素的递增子串长度最大值依赖于前面每个元素的递增子串长度最大值
var lengthOfLIS = function (nums) {
  let len = nums.length;
  // 不存在递增的子串
  if (len == 0) return [];
  // 初始化数组 保存到达每个位置的所有递增子串长度的最大值(以每个位置为终点)
  let dp = new Array(len);
  dp.fill(1);
  let maxLen = 1, val = 0;
  // 计算每一个位置的最长子串长度
  for (let i = 1; i < len; i++) {
    // 计算过程中, 每次查找i之前的元素, 看有无比nums[i]还小的
    // 小的话就是dp[j]+1
    // 但是还要将 dp[i] 与 dp[j]+1 进行比较, 保留较大值
    // 则最后dp[i]是所有递增子串中最长子串的长度
    for (let j = 0; j < i; j++) {
      if (nums[j] < nums[i]) {
        val = dp[j] + 1;
        dp[i] = val > dp[i] ? val : dp[i];
      }
    }
    // 保留dp[i]中的最大值
    maxLen = maxLen > dp[i] ? maxLen : dp[i];
  }
  // 返回的是 最长的递增子串的长度
  return maxLen;
};
```



#### [70] Climbing Stairs

> You are climbing a stair case. It takes *n* steps to reach to the top.
>
> Each time you can either climb 1 or 2 steps. In how many distinct ways can you climb to the top?

**时间复杂度**:

计算每一层的方法数, $O(N)$

**空间复杂度**:

使用数组保存到达每一层的方法数, $O(N)$

```javascript
// 楼梯一共有n层
// 需要返回达到n层总共的路径数
// 到达每一层都有两种方式, 要不跨一步, 要不跨两步
// 假设到达i层的方法数为dp[i], 则dp[i] = dp[i-1] + dp[i-2]
var climbStairs = function (n) {
  let way = new Array(n);
  way.fill(0);
  way[0] = 1;
  way[1] = 2;
  for (let i = 2; i < n; i++) {
    way[i] = way[i - 1] + way[i - 2];
  }
  return way[n - 1];
};
```





