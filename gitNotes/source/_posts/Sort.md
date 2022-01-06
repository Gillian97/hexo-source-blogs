---
title: Sort
abbrlink: 56837
date: 2020-09-17 09:36:33
tags:
description: 排序算法总结
---



### 1. 冒泡排序

时间复杂度: $O(N^2)$

平均空间复杂度: 常数

最快: 正序

最慢: 倒序

```javascript
// 冒泡排序
// 不断比较移动给定范围的最大值 直至最大值在数组的末尾
var bubble = (arr) => {
  let n = arr.length;
  let bigger;
  for (let i = 0; i < n - 1; i++) { // 为了限制内部循环的长度
    for (let j = 0; j < n - 1 - i; j++) {
      // 不断将更大的值 放在后面
      if (arr[j + 1] < arr[j]) {
        bigger = arr[j];
        arr[j] = arr[j + 1];
        arr[j + 1] = bigger;
      }
    }
  }
}
```



### 2. 选择排序

不断找到最小的元素与对应的位置进行交换.

平均时间复杂度: $O(N^2)$

空间复杂度: 常数

最快: 正序

最慢: 倒序

```javascript
// 选择排序
var selection = (arr) => {
  let n = arr.length, minIndex;
  for (let i = 0; i < n; i++) {
    minIndex = i;
    for (let j = i + 1; j < n; j++) {
      if (arr[j] < arr[minIndex]) {
        minIndex = j;
      }
    }
    // exchange minIndex and i
    // 最小值有更新
    if (minIndex != i) {
      let temp = arr[i];
      arr[i] = arr[minIndex];
      arr[minIndex] = temp;
    }
  }
}
```



### 3. 插入排序

类似于插扑克牌, 假设前n-1个是排好序的, 将第n个元素插入前面的有序数列中, 使得这n个数也是排好顺序的.

平均时间复杂度: $O(N^2)$

空间复杂度: 常数

最快: 正序 $O(N)$

最慢: 倒序

```javascript
// 插入排序
var insertion = (arr) => {
  let n = arr.length, temp;
  // 标记前多少元素是有序的
  for (let i = 0; i < n - 1; i++) {
    // 将有序数列的后一个元素加入进来
    for (let j = i + 1; j >= 0; j--) {
      if (arr[j] < arr[j - 1]) {
        temp = arr[j];
        arr[j] = arr[j - 1];
        arr[j - 1] = temp;
      } else {
        break;
      }
    }
  }
}
```



### 4. 希尔排序

对于基本有序的数据序列, 使用插入排序会更高效.



### 5. 归并排序

分治思想, 将多个已有序的子序列合并, 得到完全有序的序列(多路归并).

自上而下的递归/自下而上的迭代.(递归均可用迭代重写)

平均时间复杂度: $Ο(NlogN)$

```javascript
// 归并排序
// 直接对原数组进行修改排序
var mergeSort = (arr) => {
  let n = arr.length;
  // 递归结束条件
  // 数组只有一个元素 直接返回
  if (n <= 1) {
    return arr;
  }
  let mid = Math.floor(n / 2),
    leftArr = arr.slice(0, mid),
    rightArr = arr.slice(mid);
  return merge(mergeSort(leftArr), mergeSort(rightArr));
}

// 合并两个有序数组
var merge = (leftArr, rightArr) => {
  let result = [];
  while (leftArr.length && rightArr.length) {
    if (leftArr[0] <= rightArr[0]) {
      result.push(leftArr.shift());
    } else {
      result.push(rightArr.shift());
    }
  }
  while (leftArr.length) {
    result.push(leftArr.shift());
  }
  while (rightArr.length) {
    result.push(rightArr.shift());
  }
  return result;
}
```





### 6. 快速排序

分治思想, 是处理大数据最快的排序算法之一.

步骤:

1. 从数组中选中一个基准
2. 比基准小的放在基准左边, 比基准大的放在基准右边. 所有元素结束之后, 该基准处于数列中间位置, called partition (分区操作).
3. 递归地将基准左边与基准右边的子数列再进行排序.

平均时间复杂度:  $Ο(nlogn)$

空间复杂度: $Ο(logn)$ 

最慢: $O(N^2)$

```javascript
// 快速排序
var quickSort = (arr, left, right) => {
  if (left < right) {
    let partitionIndex = partition(arr, left, right);
    quickSort(arr, left, partitionIndex - 1);
    quickSort(arr, partitionIndex + 1, right);
  }
  // left==right 则数组不需要做排序操作 直接返回即可
}
// 分区
var partition = (arr, left, right) => {
  let pivot = left, // 随机选择基准值
    index = pivot + 1; // 标记第一个可以交换的位置
  for (let i = index; i <= right; i++) {
    // 比基准值小的元素 与index位置的元素进行交换
    if (arr[i] < arr[pivot]) {
      if (i != index) swap(arr, i, index);
      index++; // 下一个可以交换的位置往后移
    }
  }
  // 所有小于基准值的在左边 大于基准值的在右边
  // index 指向第一个大于基准值的元素
  // 将基准值交换到中间
  swap(arr, pivot, index - 1);
  // 返回基准值的最新位置
  // 为下一轮划分分区做准备
  return index - 1;
}
// 交换元素
var swap = (arr, i, j) => {
  let temp = arr[i];
  arr[i] = arr[j];
  arr[j] = temp;
}
```





### 7. 堆排序



## 使用桶



### 8. 计数排序

***非比较排序***, 一个桶放一个数字.

速度快于任何比较算法. 核心在于将数字放在对应的桶中, 需要知道元素的大小范围.

每个桶里放一个元素.

时间复杂度:  $O(N+K)$. 对于一个大小范围是$0-K$的长度为$N$的数组, 需要遍历两个数组.

空间复杂度: $O(K)$. 需要长度为$K+1$的数组放置这$N$个元素

限制: 只能对整数进行排序, 在最大值与最小值之间差距较大时, 比较浪费空间.

```javascript
var countingSort = (arr, maxValue) => {
  let n = maxValue + 1, index = 0;
  let bucket = new Array(n);
  // 初始化数组的值为 0
  for (let i = 0; i < n; i++) {
    bucket[i] = 0;
  }
  // 统计待排数组
  for (let i of arr) {
    bucket[i]++;
  }
  console.log('bucket', bucket);
  // 形成排序之后的数组
  for (let i = 0; i < n; i++) {
    while (bucket[i] > 0) {
      arr[index++] = i; // 先取值, 再++
      bucket[i]--;
    }
  }
  console.log(arr);
  return arr;
}
```



### 9. 桶排序

将数组中的元素分到数量有限的桶中, 每个桶中再单独进行排序(可能使用别的排序算法), 即每个桶是一个桶区间.

计算桶区间时, 使用 `(max - min)/(桶数 - 1)`, 除数减一的原因是为了使得最大值也能有桶装, 否则只有最大值刚好被排除在外.

假设 N 为待排序的元素个数, M 为桶个数.

时间复杂度: 将所有元素放入桶中, 数组遍历一遍, 桶遍历一遍, $O(M+N)$

空间复杂度: $O(M)$

优点: 简单/速度快

限制: 空间利用率不高

适用场景: 数据分布比较均匀/数据跨度不是很大, 实际使用桶排序会使用散列表, 提高空间利用率和时间复杂度.

```javascript
var bucketSort = (arr) => {
  // 桶的默认数量
  const bucketNum = 5;
  // 确定每个桶的容量/平均每个桶装几个数
  let minVal = arr[0], maxVal = arr[0];
  for (let i of arr) {
    minVal = Math.min(minVal, i);
    maxVal = Math.max(maxVal, i);
  }
  // 桶区间
  let scope = Math.floor((maxVal - minVal) / (bucketNum - 1));
  // 初始化桶
  let buckets = new Array(bucketNum);
  for (let i = 0; i < bucketNum; i++)  buckets[i] = [];
  // 数组中元素根据大小放入桶中 注意减去最小值
  for (let i of arr) buckets[Math.floor((i - minVal) / scope)].push(i);
  // 对每个桶中的元素进行排序 这里使用插入排序
  for (let bucket of buckets) {
    for (let i = 0; i < bucket.length - 1; i++) {
      // 找到插入的位置
      for (let j = i + 1; j > 0; j--) {
        if (bucket[j] >= bucket[j - 1]) break;
        let temp = bucket[j];
        bucket[j] = bucket[j - 1];
        bucket[j - 1] = temp;
      }
    }
  }

  // 将桶中排好序的元素拿出
  let newIndex = 0;
  for (let bucket of buckets) {
    for (let i of bucket) arr[newIndex++] = i;
  }
}
```



### 10. 基数排序

***非比较排序***, 一个桶放对应数位相同的所有数字.

每个桶里放对应位数相同的元素, 比如第一轮放置时, 64, 24, 34一个桶, 第二轮放置时, 63, 65, 567一个桶.

时间复杂度:

假设最大值的位数是 K, 则一共需要遍历数组 K 遍. 每次遍历后将元素放入桶中, 随后还要将元素从桶中拿出来, 需要N的时间.

时间复杂度为: $O(K * (N + N)) = O(K*N)$.

空间复杂度: 需要桶来存储元素, 每对元素进行除法, 都需要桶来记录当前的结果.

```javascript
// 基数排序
var radixSort = (arr) => {
  let bucket = new Array(10);
  let index = 0, mod = 10, dev = 1;
  for (let i = 0; i < 10; i++) bucket[i] = [];

  // 找到最大值 获取最长位数
  let maxVal = 0;
  for (let i of arr) maxVal = Math.max(maxVal, i);
  let maxLen = maxVal.toString().length;

  // 进行除法的次数
  for (let i = 0; i < maxLen; i++, mod *= 10, dev *= 10) {
    index = 0;
    for (let num of arr) bucket[Math.floor(num % mod / dev)].push(num);
    // 依次读取bucket中的值
    for (let item of bucket) {
      while (item.length > 0) {
        arr[index++] = item.shift(); // 先进的值更小 比如 54 52
      }
    }
  }
  return arr;
}
```



