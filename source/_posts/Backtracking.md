---
title: Backtracking
tags:
  - LeetCode
  - Backtracking
categories: Algorithm
abbrlink: 1f6fbb87
date: 2020-08-18 10:18:11
description: 回溯相关题解
mathjax: true
---

### 回溯算法

##### 解题过程=决策树的遍历过程:

1. 路径: 已经做出的选择
2. 选择列表: 当前可以做的选择
3. 结束条件: 到达决策树底层, 无法再做选择的条件(可选列表为空等)

##### 回溯算法代码框架:

```javascript
result = []
var backtrack = (路径, 选择列表) => {
  if 满足结束条件{
    result.push(路径);
  	return;
  }
  
  for 选择 in 选择列表{
  	// 做选择
  	将该选择从选择列表中移除
    路径.push(选择)
    backtrack(路径, 选择列表);
  	// 撤销选择
    路径.pop()
    将该选择再加入选择列表
  }
}
```

##### 时间复杂度分析:

递归相关的算法, 时间复杂度计算为: [递归次数]*[递归本身的时间复杂度].



### 经典例题

#### N皇后问题

##### [51] N-Queens

套用回溯框架即可, 每确定一个皇后位置, 将其加入已经选择的路径中, 接着进行选择.

学了框架之后, 思路都清楚了很多.

```javascript
// 皇后彼此不能相互攻击也就是说：任何两个皇后都不能处于同一条横行、纵行或斜线上
var solveNQueens = function (n) {
  // 初始化棋盘
  let board = [];
  for (let i = 0; i < n; i++) {
    board.push('.');
  }
  let path = [], res = []; // 存储皇后的纵坐标
  backtrack(board, path, res, n, 0);
  return res;
};

var backtrack = (board, path, res, n, depth) => {
  // end condition
  if (path.length == n) {
    let solution = [];
    for (let col of path) {
      let ele = board.slice();
      ele[col] = 'Q';
      solution.push(ele.join(''));
    }
    res.push(solution);
    return;
  }
  // make a choice
  for (let i = 0; i < n; i++) {
    if (path.length == 0 || isValid(depth, i, path)) {
      // 进行选择
      path.push(i);
      backtrack(board, path, res, n, depth + 1);
      // 撤销选择
      path.pop();
    }
  }
}

// 给两个皇后的坐标 判断这两个皇后是否会互相攻击
// 同一横线 同一竖线 同一斜线
var isValid = (m, n, path) => {
  for (let i = 0; i < path.length; i++) {
    // 横坐标相等 纵坐标相等 横坐标坐标差值相等
    if (m == i || n == path[i] || Math.abs(m - i) == Math.abs(path[i] - n)) {
      return false;
    }
  }
  return true;
}
```



#### 子集问题

##### [78] Subsets

> Given a set of **distinct** integers, *nums*, return all possible subsets (the power set).
>
> **Note:** The solution set must not contain duplicate subsets.

观察全排列/组合/子集问题，它们比较相似，且可以使用一些通用策略解决。

首先，它们的解空间非常大：

- 全排列：N!。


- 组合：N!。


- 子集：2^N, 每个元素都可能存在或不存在。


在它们的指数级解法中，要确保生成的结果 **完整** 且 **无冗余**，有三种常用的方法：

1. 递归
2. 回溯
3. 基于二进制位掩码和对应位掩码之间的映射字典生成排列/组合/子集

相比前两种方法，第三种方法将每种情况都简化为二进制数，易于实现和验证。

此外，第三种方法具有**最优的时间复杂度**，可以生成按照字典顺序的输出结果。



###### solution 1 : 字典排序（二进制排序） 子集

这种解法很巧妙, 由于是全排列问题, 子集的数量与数组长度有关.

数组中的元素, 每个只有在或者不在子集中这两种选择. 对于每一种可能, 都能用二进制来标记.

因此该方法的思路如下:

假设数组为[1, 2, 4], 则子集数量为 2^len= 2^3 = 8

则从 0 - 7 的二进制对应分别为 000-111

每一种可能都对应一种子集详情, 比如 101 对应 [1,4],  001 对应 [4].

![bitmask4](Backtracking/bitmask4.png)

需要注意的点是, 在将十进制转换为二进制时, 需要将二进制的位数扩充至与nums的长度相等.

```javascript
var subsets = function (nums) {
  let len = nums.length;
  let subSets = [];
  // 根据数组长度计算器其子集数量
  let subSetsNum = Math.pow(2, len);
  for (let i = 0; i < subSetsNum; i++) {
    // 十进制转换为二进制
    // 每一个二进制都唯一对应一个子集
    let setNoStr = i.toString(2);
    while (setNoStr.length < len) {
      //如果长度不足 len，前面添加 0
      setNoStr = '0' + setNoStr;
    }
    let setNoList = setNoStr.split('');
    let subSet = [];
    for (let j = 0; j < len; j++) {
      if (setNoList[j] == '1') {
        subSet.push(nums[j]);
      }
    }
    subSets.push(subSet);
  }
  return subSets;
};
```

**复杂度分析**

- 时间复杂度：O(N×2^N)，生成所有的子集，并复制到输出列表中。
- 空间复杂度：O(N×2^N)，存储所有子集，共 n 个元素，每个元素都有可能存在或者不存在。



###### solution 2 : 数学归纳法

这个解法挺巧妙的, 每次都把新元素加进已有的所有子集, 生成新的子集, 因为每个元素只有在和不在两种情况.

```javascript
var subsets = function (nums) {
  let subSets = [[]];
  let len = nums.length;
  if (len == 0)
    return subSets

  for (let i = 0; i < len; i++) {
    let l = subSets.length;
    for (let j = 0; j < l; j++) {
      let ele = subSets[j].concat([nums[i]]);
      subSets.push(ele);
    }
  }
  return subSets;
}
```

时间复杂度:  总共添加2^N个子集, 每个子集是数组形式, 子集通过深拷贝塞进结果数组中, 拷贝耗时O(N). 因此, 总的时间复杂度是 O(N*2^N).

空间复杂度: 存储每个子集需要 O(N)的递归堆栈空间(不算结果集), 算上结果的话, 一共2^N个子集, 因此所需空间为 O(N*2^N).



###### Solution 3 :  回溯解法

使用回溯模板. 控制start.

```javascript
var subsets = (nums) => {
  let res = [], path = [];
  helper(nums, res, path);
  res.push([]); // 添加空集
  return res;
}

var helper = (choice, res, path) => {
  // end condition
  // 没有节点可以选择时 返回
  if (choice.length == 0) return;
  
  for (let i = 0; i < choice.length; i++) {
    // 做选择
    path.push(choice[i]);
    res.push(path.slice()); // 每一个节点都是子集
    // 对于一个没有重复元素的集合来说
    // 在添加元素时 直接向后作为选择列表即可
    // 因为前面的元素与当前元素的子集已经被先前元素添加过了
    // 排除已选择的数字
    helper(choice.slice(i + 1), res, path);
    // 撤销选择
    path.pop();
  }
}
```



##### [90] Subsets II

> Given a collection of integers that might contain duplicates, ***nums\***, return all possible subsets (the power set).
>
> **Note:** The solution set must not contain duplicate subsets.

思路与子集时类似, 只是需要剪枝操作, **将同一层其余相同的元素除去**.

```javascript
var subsetsWithDup = function (nums) {
  nums = nums.sort(); // 排序操作 使得相同元素相邻
  let len = nums.length;
  let res = [];
  for (let i = 0; i <= len; i++) {
    recur(i, 0, len, [], res, nums);
  }
  console.log(res);
  return res;
}

var recur = (depth, first, len, curr, res, nums) => {
  if (curr.length == depth) {
    res.push(curr.slice()); // 将当前子集的深拷贝加入结果数组
    return;
  }
  for (let i = first; i < len; i++) {
    if (i > first && nums[i] == nums[i - 1]) { // 一开始写成了 i>0 结果总是不对 后来知道每次加元素是从 first 开始 不是 0 开始
      // 每次当作为起始点往数组加入元素时,不能加入与上一个元素相同的元素
      continue;
    }
    curr.push(nums[i]);
    recur(depth, i + 1, len, curr, res, nums);
    curr.pop(); // 回溯 回到初始状态
  }
}
```



#### 括号问题

两个性质:

1. 合法的括号组合中, 左括号数目=右括号数目.
2. 一个合法的括号组合字符串 s, 对于任意 0<=i<len(s), 子串 s[0, i] 中, 左括号数目>=右括号数目.

##### [22] Generate Parentheses

> Given *n* pairs of parentheses, write a function to generate all combinations of well-formed parentheses.

对于该题题意的理解, 可以是, 现在有 `2n` 个位置, 每个位置可以放置`'('`(数目为n)/`')'`(数目为n), 请问一共有多少种合法的放置方式?

则解题思路是: *先得到 2^(2n) 种组合方式, 再去除不合法的部分即可.*

```javascript
// 2020/10/21 学习回溯模板之后 很简洁
var generateParenthesis = (n) => {
  let path = [], res = [];
  helper(path, res, n, n, n);
  return res;
}

var helper = (path, res, n, left, right) => {
  // 左括号剩的比右括号多 不合法
  if (left > right) return;
  // 使用次数小于0 不合法
  if (left < 0 || right < 0) return;
  // 左右括号都恰好用完, 得到一个合法的括号
  if (left == 0 && right == 0) {
    res.push(path.join(''));
    return;
  }
  // 尝试放一个左括号
  path.push('('); // 选择
  helper(path, res, n, left - 1, right);
  path.pop(); // 撤销选择
  // 尝试放一个右括号
  path.push(')'); // 选择
  helper(path, res, n, left, right - 1);
  path.pop(); // 撤销选择
}
```



#### 排列问题

##### [46] Permutations

> Given a collection of **distinct** integers, return all possible permutations.

时间复杂度: O(N*N!)

全排列一共有 N! 种可能, 实现每一种可能需要遍历整个数组即O(N)时间, 所以可得.

使用回溯模板. 删除已经选择的数字.

```javascript
var permute = (nums) => {
  let res = [], path = [];
  helper(nums, res, path);
  return res;
}
var helper = (nums, res, path) => {
  // 选择列表为空
  if (nums.length == 0) {
    res.push(path.slice());
    return;
  }
  for (let i = 0; i < nums.length; i++) {
    // 做选择
    path.push(nums[i]);
    let nums_cp = nums.slice();
    // 从选择列表中删除这个选择
    // 排除已选择的数字
    nums.splice(i, 1); 
    helper(nums, res, path);
    // 撤销选择
    path.pop(); 
    nums = nums_cp; // 将移除的选择重新加入选择列表
  }
}
```

#### 组合问题

##### [77] Combinations

> Given two integers *n* and *k*, return all possible combinations of *k* numbers out of 1 ... *n*.
>
> You may return the answer in **any order**.

使用回溯模板, 决策树的深度为 k. 将深度为k的节点均加入节点列表.

其实就是自己问题的一部分, 使用start排除已经做出的选择.

```javascript
var combine = (n, k) => {
  let path = [], res = [];
  let choice = [];
  for (let i = 1; i <= n; i++) {
    choice.push(i);
  }
  helper(choice, 0, path, res, k);
  return res;
}

var helper = (choice, start, path, res, k) => {
  if (path.length == k) {
    res.push(path.slice());
    return;
  }
  for (let i = start; i < choice.length; i++) {
    path.push(choice[i]);
    helper(choice, i + 1, path, res, k);
    path.pop();
  }
}
```



#### 其他问题

##### [79] Word Search

> Given a 2D board and a word, find if the word exists in the grid.
>
> The word can be constructed from letters of sequentially adjacent cell, where "adjacent" cells are those horizontally or vertically neighboring. The same letter cell may not be used more than once.

回溯算法本质就是暴力穷举.

```javascript
var exist = function (board, word) {
  let m = board.length;
  let n = board[0].length;
  for (let i = 0; i < m; i++) {
    for (let j = 0; j < n; j++) {
      if (board[i][j] == word[0]) {
        const res = findNeighbor(board, word, 0, i, j, {});
        if (res) return true;
      }
    }
  }
  return false;
};

var findNeighbor = (board, word, w, row, col, visited) => {
  // 不能在这里进行判断 因为尽管w这里表示最后一个字母
  // 但是却并不一定能在board里找到 需要再经过判断
  // if (w + 1 == word.length) return true;

  // 选择不合法则回到上一层
  let key = row + '+' + col;
  if (row >= board.length || row < 0 || col >= board[0].length || col < 0
    || visited[key] || board[row][col] != word[w]
  ) {
    return false;
  }

  // 选择列表
  // 对一个可能的选择都进行尝试

  // 做选择
  visited[key] = true;
  // 递归结束条件
  // 一旦word的最后一个字母也找到 则说明成功找到word
  if (w + 1 == word.length) return true;
  // 一旦找到目标字符串(递归返回值为true) 就直接返回 不用再进行下面的递归了 减少递归次数
  let r1 = findNeighbor(board, word, w + 1, row - 1, col, visited);
  if (r1) return true;
  let r2 = findNeighbor(board, word, w + 1, row + 1, col, visited);
  if (r2) return true;
  let r3 = findNeighbor(board, word, w + 1, row, col - 1, visited);
  if (r3) return true;
  let r4 = findNeighbor(board, word, w + 1, row, col + 1, visited);
  if (r4) return true;

  // 找了一圈没有找到 说明从该节点开始不是正确的选择
  // 则在该节点应该 撤销选择 回到上一层 在上一层转换搜索方向
  visited[key] = false;
  return false;
}
```



##### [93] Restore IP Addresses

> Given a string `s` containing only digits. Return all possible valid IP addresses that can be obtained from `s`. You can return them in **any** order.
>
> A **valid IP address** consists of exactly four integers, each integer is between `0` and `255`, separated by single points and cannot have leading zeros. For example, "0.1.2.201" and "192.168.1.1" are **valid** IP addresses and "0.011.255.245", "192.168.1.312" and "192.168@1.1" are **invalid** IP addresses. 

就是大概有想法但是却实现不出来, 说明思路还不是很清楚, 需要画树状图帮助自己理解.

多画树状图, 理解回溯过程. 被这道题折磨好久.

```javascript
/**
 * 
 * 合法的url的条件:
 * 将字符串分为四个部分, 每个部分的数字[0,255], 不超过3位
 * 中间的数字不能以0开头
 * 
 */
var restoreIpAddresses = function (s) {
  let len = s.length;
  // 字符串长度不够则直接返回空数组
  if (len > 12 || len < 4)
    return [];
  let res = new Array();
  let ip = new Array();
  let start = 0;
  dfs(res, ip, start, s);
  return res;
};

/**
 * 
 * @param {array} res 存储合法ip
 * @param {array} ip 存储ip的每一段
 * @param {number} start 剩余需要继续递归的子串
 * @param {string} s 
 */
var dfs = (res, ip, start, s) => {
  // 在开始切割子串之前, 判断ip是否符合题意
  // s 已经遍历完毕且ip是四段的话, 该ip可以进结果数组了
  if (ip.length == 4) {
    if (start == s.length) {
      res.push(ip.join('.'));
      return;
    }
    // 未遍历完s ip已经4段 该ip不合题意
    if (start < s.length) {
      return;
    }
  }
  // s 已经遍历完了,但是ip不到四段,返回
  if (ip.length < 4 && start == s.length) {
    return;
  }
  // 从每个起始位置开始切割都是三个长度
  for (let l = 1; l <= 3; l++) {
    if (start + l > s.length) return; // 索引超过边界
    if ((l == 2 || l == 3) && s[start] == '0') return; // 2/3子串以0开头
    let part = s.substring(start, start + l); // 切割子串
    if (Number(part) > 255) return; // 子串不符合条件 这里已经l==3了 continue和return是一样的效果
    // part符合条件
    ip.push(part.slice());
    // 下次递归的子串的起始位置是start+l
    dfs(res, ip, start + l, s);
    ip.pop();
  }
}
```



##### [473] Matchsticks to Square

> Remember the story of Little Match Girl? By now, you know exactly what matchsticks the little match girl has, please find out a way you can make one square by using up all those matchsticks. You should not break any stick, but you can link them up, and each matchstick must be used **exactly** one time.
>
> Your input will be several matchsticks the girl has, represented with their stick length. Your output will either be true or false, to represent whether you could make one square using all the matchsticks the little match girl has.

需要注意的是, 在采用递归方法的情况下,  这里不需要在选择位置的外面再套一层for循环去遍历火柴, 因为不存在第一根火柴找到合适位置之后, 把第一根火柴去掉, 以第二根火柴为第一根火柴, 再重新开始摆放. 只需要每根火柴摆放好之后, 去递归摆放下一根火柴即可.

```javascript
// 使用小女孩的所有火柴拼成一个正方形
// 回溯 使用所有的火柴 每根火柴只能用一次
var makesquare = function (nums) {
  let sum = 0;
  nums.forEach((val) => {
    sum += val;
  })
  if (sum % 4 != 0) return false;
  let res = [0, 0, 0, 0];
  helper(nums, 0, res, sum / 4);
  return res[0] != 0;
};

var helper = (nums, index, res, sum) => {
  // 递归结束条件 所有火柴均已放置
  if (index == nums.length) {
    // 找到可行解
    return res[0] == res[1] && res[1] == res[2] && res[2] == res[3];
  }
  
  // 每根火柴均有四个位置可以选择
  for (let j = 0; j < res.length; j++) {
    if (res[j] + nums[index] <= sum) {
      // 做选择
      res[j] += nums[index];
      if (helper(nums, index + 1, res, sum)) {
        return true; // 找到可行解就返回 不再继续尝试
      }
      // 撤销选择
      res[j] -= nums[index];
    }
  }
  // 走到这里说明 剩下的火柴怎么摆放都达不到要求
  // 说明上一层摆错了位置
  return false;
}
```

