---
title: Array
abbrlink: 7791
date: 2020-09-21 21:47:45
tags:
description: 数组相关题解
mathjax: true
---

#### [56] Merge Intervals

> Given a collection of intervals, merge all overlapping intervals.

**时间复杂度**:

排序时间($O(NlogN)$) + 遍历数组($O(N)$)

**空间复杂度**: 直接借用js数组特性splice对原数组进行修改, 没有使用额外的空间. 因此为$O(1)$.

**数组的sort方法**:

当数组长度<=10的时候，采用插入排序($O(N^2)$)，>10的时候，采用快排($O(NlogN)$)。

对于长度>1000的数组，采用的是快排与插入排序混合的方式进行排序.

```javascript
// 合并有交集的若干个区间
var merge = function (intervals) {
  let len = intervals.length
  if (len == 0 || len == 1) return intervals;
  // 首先对区间进行排序 根据数组的第一列进行排序
  // 如果不排序 后面在合并区间时也会两两交换 等于排序
  intervals.sort((a, b) => a[0] - b[0]);
  // 将数组中的区间进行两两合并
  // 如果某两个邻近区间a1 a2没有合并完成
  // 那么由于排序的原因 a1更不会和a3合并
  let preIndex = 0, currIndex = 1, mergeRes;
  while (currIndex < len) {
    // 判断两个区间是否能合并
    // 不能返回false 能则返回合并后的结果
    mergeRes = isMerge(intervals[preIndex], intervals[currIndex]);
    // 不能合并
    if (mergeRes == false) {
      preIndex = currIndex;
      currIndex++;
    } else { // 进行合并
      intervals[preIndex] = mergeRes;
      intervals.splice(currIndex, 1);
      // currIndex 此时不变
      len = intervals.length;
    }
  }
  return intervals;
};

var isMerge = (inter1, inter2) => {
  // 两个区间能合并的前提条件是 有交集
  let num0 = inter1[0], num1 = inter1[1], num2 = inter2[0], num3 = inter2[1];
  if (num1 < num2) {
    return false;
  } else if (num1 >= num2 && num1 <= num3) { // 不是包含关系的交集
    return [num0, num3];
  } else { // 前一个区间包含后一个区间
    return inter1;
  }
}
```



#### [57] Insert Interval

> Given a set of *non-overlapping* intervals, insert a new interval into the intervals (merge if necessary).
>
> You may assume that the intervals were initially sorted according to their start times.

**时间复杂度**:

遍历了一遍数组, 后面的判断部分时间是常数级, 因此  $O(N)$.

不得不说, 思路很好耶, 很巧妙利用了题目所给的条件. 比如有序, 比如区间合并, 这里的区间合并显得尤其简单.

```javascript
// intervals 本身是已经排好序的
var insert = function (intervals, newInterval) {
  let left = [], right = [];
  // 逐步比较 将完全不能与当前区间合并的区间分成左右两半部分
  for (let item of intervals) {
    if (item[1] < newInterval[0]) {
      left.push(item);
    }
    if (item[0] > newInterval[1]) {
      right.push(item);
    }
  }
  // 说明剩余有可以合并的区间
  // 剩余的每个区间都是可以和 newInterval 合并的
  // 剩余的所有区间和 newInterval 最后会合并成一个区间
  let ll = left.length, rl = right.length, len = intervals.length;
  let s = newInterval[0], b = newInterval[1];
  if (ll + rl != len) {
    // 可以合并的区间的序号范围是 [ll, len-rl-1]
    // 由于中间的每一个元素均和 newInterval 有交集
    // 则最后会合并成一个区间 找到最后这个合并区间的最大值与最小值(即其范围)
    s = Math.min(intervals[ll][0], s);
    b = Math.max(intervals[len - rl - 1][1], b);
  }
  // 将合并部分的区间置于左右中间
  // 拼接形成结果
  let res = left.concat([[s, b]]).concat(right);
  return res;
}
```



#### [169] Majority Element

> Given an array of size *n*, find the majority element. The majority element is the element that appears **more than** `⌊ n/2 ⌋` times.
>
> You may assume that the array is non-empty and the majority element always exist in the array.



```javascript
// solution 1
var majorityElement = function (nums) {
  // 确定次数
  let times = Math.floor(nums.length / 2);
  let map = {};
  for (let i of nums) {
    if (i in map) {
      map[i] += 1;
    } else {
      map[i] = 1;
    }
    // 每个值的出现次数更新之后, 都会判断一下是否是主要元素
    // 由于题目说主要元素一定存在, 因此一定会存在元素的出现次数超过规定值
    if (map[i] > times) return i;
  }
};
```



#### [54] Spiral Matrix

> Given a matrix of *m* x *n* elements (*m* rows, *n* columns), return all elements of the matrix in spiral order.

**时间复杂度**:

遍历矩阵 $O(M*N)$.

**空间复杂度**:

除了返回的结果数组, 使用的额外空间为常数级, $O(1)$.

```javascript
// 旋转矩阵
// 按层遍历 输出结果
var spiralOrder = function (matrix) {
  let m = matrix.length; // 矩阵的行数
  if (m == 0) return [];
  let n = matrix[0].length; // 矩阵的列数
  let res = []; // 存储结果序列
  let top = 0, bottom = m - 1;
  let left = 0, right = n - 1;
  // 按照顺时针方向
  while (top <= bottom && left <= right) {
    // 添加top从左到右的一行
    for (let i = left; i <= right; i++) {
      res.push(matrix[top][i]);
    }
    if (top == bottom) break; // 判断结束情况

    // 添加right从上到下的一列
    for (let j = top + 1; j <= bottom; j++) {
      res.push(matrix[j][right]);
    }
    if (left == right) break; // 判断结束情况

    // 添加bottom从右到左的一行 
    for (let i = right - 1; i >= left; i--) {
      res.push(matrix[bottom][i]);
    }

    // 添加left从下到上的一列
    for (let j = bottom - 1; j >= top + 1; j--) {
      res.push(matrix[j][left]);
    }
    // 修改边界值 进入下一层
    top++; left++; bottom--; right--;
  }
  // 直接在循环中判断两个边界是否相等 就不用再另外判断奇数偶数
  // 然后分情况讨论了
  /*
  // m n 均是偶数 则退出循环后可以直接返回res
  // m n 至少有一个为奇数
  退出循环时则会出现 top==bottom 或者 left==right
  if (m % 2 != 0 || n % 2 != 0) {
    if (top == bottom) {
      for (let i = left; i <= right; i++) {
        res.push(matrix[top][i])
      }
    } else {
      for (let i = top; i <= bottom; i++) {
        res.push(matrix[i][left])
      }
    }
  }
  */
  return res;
}
```



#### [59] Spiral Matrix II

> Given a positive integer *n*, generate a square matrix filled with elements from 1 to *n*2 in spiral order.

借鉴上一题的思路.

**时间复杂度**: O(N*N), 遍历了整个二维数组.

**空间复杂度**: O(1), 除了返回的二维结果数组, 其余为常量级.

```javascript
var generateMatrix = function (n) {
  let top = 0, bottom = n - 1;
  let left = 0, right = n - 1;
  // 注意不要使用 let arr = new Array(n).fill(new Array(n))
  // 这样fill填充的是同一个数组的引用, 不是多个,(坑)
  // 建议使用for循环初始化结果数组
  let arr = new Array(n);
  for (let i = 0; i < n; i++) {
    arr[i] = new Array(n);
  }
  let val = 0;
  // 按照顺时针的方向逐行逐列填充递增值
  // 填充完外圈再填充内圈
  while (top <= bottom && left <= right) {
    // n为奇数 最后 top==bottom left==right
    // 只会走下面这一个for循环 其余for循环均不满足条件
    // n 为偶数 最后 top+1==bottom left+1==right
    // 最后一个for循环不满足条件
    for (let i = left; i <= right; i++) {
      arr[top][i] = ++val;
    }
    for (let i = top + 1; i <= bottom; i++) {
      arr[i][right] = ++val;
    }
    for (let i = right - 1; i >= left; i--) {
      arr[bottom][i] = ++val;
    }
    for (let i = bottom - 1; i >= top + 1; i--) {
      arr[i][left] = ++val;
    }
    top++; bottom--; left++; right--;
  }
  return arr;
};
```



#### [48] Rotate Image

> You are given an *n* x *n* 2D `matrix` representing an image, rotate the image by 90 degrees (clockwise).
>
> You have to rotate the image [**in-place**](https://en.wikipedia.org/wiki/In-place_algorithm), which means you have to modify the input 2D matrix directly. **DO NOT** allocate another 2D matrix and do the rotation.

**时间复杂度**:

双重循环 $O(N)$.

**空间复杂度**:

O(1). 原地修改数组内容.

```javascript
// 思路就是: 旋转完外圈, 再旋转内圈
// 直至内圈没有可以旋转的圈
// 没有啥可说的, 就是不断画图, 标注坐标
// 找到需要交换值的四个位置坐标间的联系与规律
var rotate = function (matrix) {
  let n = matrix.length;
  // 计算层数
  let levels = Math.floor(n / 2);
  let end, a, b, c, d;
  // 遍历层数
  for (let t = 0; t < levels; t++) {
    // 每一层右上角节点的纵坐标值
    end = n - 1 - t;
    // 每一层的矩阵大小为 m*m
    // 则只有前m-1个元素需要移动
    for (let i = t; i <= end - 1; i++) {
      // 需要进行交换的四个点的值
      a = matrix[t][i];
      b = matrix[i][end];
      c = matrix[end][end - (i - t)];
      d = matrix[end - (i - t)][t];
      // 进行交换
      matrix[t][i] = d;
      matrix[i][end] = a;
      matrix[end][end - (i - t)] = b;
      matrix[end - (i - t)][t] = c;
    }
  }
};
```

