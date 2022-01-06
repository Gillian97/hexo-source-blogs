---
title: Two Pointers
abbrlink: b6a54014
date: 2020-07-30 10:03:54
tags: [LeetCode, array, two-pointers]
categories: Algorithm
description: 双指针相关题解
---



#### [11] Container With Most Water

> Given *n* non-negative integers *a1*, *a2*, ..., *an* , where each represents a point at coordinate (*i*, *ai*). *n* vertical lines are drawn such that the two endpoints of line *i* is at (*i*, *ai*) and (*i*, 0). Find two lines, which together with x-axis forms a container, such that the container contains the most water.
>
> **Note:** You may not slant the container and *n* is at least 2.



###### my solution : Brute Force

穷举所有面积的可能性,最后对面积进行排序,找到最大值.

中间一度尝试过将 `partArea` 组成数组先排序,也还是超时.

```javascript
var maxArea = function (height) {
  let partMaxSet = new Set();
  for (let i = 0; i < height.length - 1; i++) {
    for (let j = i + 1; j < height.length; j++) {
      let yVal = height[j] < height[i] ? height[j] : height[i];
      let partArea = (j - i) * yVal;
      partMaxSet.add(partArea);
    }
  }
  let partMaxList = Array.from(partMaxSet);
  // 数字降序排列
  partMaxList.sort(function (a, b) { return b - a });
  return partMaxList[0];
  // return Math.max.apply(null, partMaxList);
};
```



###### Two pointers

采用双指针做法, 对于 S(i, j) 来说, 都是每次向里移动一步.

移动短板, 短板有可能变长, 面积有可能变大.

但是移动长板,  短板只会不变或者变小, 因为盛水的体积取决于短板, 所以面积只会不变或变小.

```js
// 此算法需要证明
var maxArea = function (height) {
  let i = 0, j = height.length - 1;
  let areaList = new Array();
  while (j - i > 0) {
    if (height[i] < height[j]) {
      // 计算面积以短边为准
      areaList.push((j - i) * height[i]);
      // 移动短边有可能获得更大面积
      i++;
    } else {
      areaList.push((j - i) * height[j]);
      j--;
    }
  }
  // 将可能的面积列表倒序排列,返回第一个
  return areaList.sort((a, b) => b - a)[0]
}
```

**Complexity Analysis**

- Time complexity : O(n)*O*(*n*). Single pass.
- Space complexity : O(1)*O*(1). Constant space is used.



#### [26] Remove Duplicates from Sorted Array

> Given a sorted array *nums*, remove the duplicates [**in-place**](https://en.wikipedia.org/wiki/In-place_algorithm) such that each element appear only *once* and return the new length.
>
> Do not allocate extra space for another array, you must do this by **modifying the input array [in-place](https://en.wikipedia.org/wiki/In-place_algorithm)** with O(1) extra memory.



###### my solution

借用 js 数组splice方法

```javascript
var removeDuplicates = function (nums) {
  for (let i = 0; i < nums.length; i++) {
    let j = i+1;
    while (nums[j] == nums[i]) {
      nums.splice(j, 1);
    }
  }
};
```



###### Two pointers

```javascript
// 参考双指针的方法, 优化了解法
// js的数组越界不会报错,只会得到 undefined 值
var removeDuplicates = function (nums) {
  let i = 0;
  for (let j = 0; j < nums.length; j++) {
    if (nums[j] != nums[j + 1]) {
      nums[i] = nums[j];
      i++;
    }
  }
  return i;
};
```



#### [27] Remove Element

> Given an array *nums* and a value *val*, remove all instances of that value [**in-place**](https://en.wikipedia.org/wiki/In-place_algorithm) and return the new length.
>
> Do not allocate extra space for another array, you must do this by **modifying the input array [in-place](https://en.wikipedia.org/wiki/In-place_algorithm)** with O(1) extra memory.
>
> The order of elements can be changed. It doesn't matter what you leave beyond the new length.



###### my solution

我自己的解法是利用了js数组操作的特性, 可以直接删除数组元素然后剩余元素位置前移的那种,比较方便,但是运行效果不咋地.

```javascript
// solution 1
var removeElement = function (nums, val) {
  for (let i = 0; i < nums.length; i++) {
    // 使用 while 保证对于同一个i, 去除该位置所有与val相等的值
    // 不会遗漏由于删除数组元素而位置前移的新元素
    while (nums[i] == val) {
      nums.splice(i, 1)
    }
  }
};
```



###### Two pointers

下面的解法是参考了示例解法, 双指针解法. 我觉得很精巧.

主要思路是, 将需要保留的元素都赋值给数组的前部分, 使用 i 标记赋值的位置.

```javascript
// solution two pointers
// 只保留与val不同的元素
// 赋值操作比起splice的删除操作 肯定速度更快 至于额外的空间 需要看splice的实现有没有占用了
var removeElement = function (nums, val) {
  let i = 0;
  for (let j = 0; j < nums.length; j++) {
    if (nums[j] != val) {
      nums[i] = nums[j];
      // console.log("i=", i, " ", nums[i]);
      i++;
    }
  }
  return i;
};
```

最差的情况应该是, 没有一个一样的, 但是遍历数组两遍而不是嵌套, 所以是 O(n).

**Complexity analysis**

- Time complexity : O(n). Assume the array has a total of n*n* elements, both *i* and *j* traverse at most 2*n* steps.
- Space complexity : O(1).



#### [15] 3Sum

> Given an array `nums` of *n* integers, are there elements *a*, *b*, *c* in `nums` such that *a* + *b* + *c* = 0? Find all unique triplets in the array which gives the sum of zero.
>
> **Note:**
>
> The solution set must not contain duplicate triplets.

数组排序后, 方便去除重复的元素 + 双指针移动不用嵌套且有方向可循.

```javascript
// solution 2 ：将数组排序后的双指针解法
var threeSum = function (nums) {
  nums = nums.sort((a, b) => a - b); // 将数组正序排列
  let len = nums.length;
  let res = [];
  for (let i = 0; i < len - 2; i++) {
    // 第一个数大于 0，肯定加起来和不为0了
    if (nums[i] > 0) {
      break;
    }
    // 去掉重复元素
    if (i > 0 && nums[i] == nums[i - 1])
      continue;
    let target = -nums[i];
    let left = i + 1, right = len - 1;
    while (left < right) {
      if (nums[left] + nums[right] == target) {
        res.push([nums[i], nums[left], nums[right]]);
        left++;
        right--;
        // 这里是否判断 left < right 都没有那么重要, 因为最外面还会再判断一次
        // 但是加上判断可能会少做一次计算
        // 去掉重复元素
        while (left < right && nums[left] == nums[left - 1]) {
          left++;
        }
        while (left < right && nums[right] == nums[right + 1]) {
          right--;
        }
      } else if (nums[left] + nums[right] < target) {
        left++;
      } else {
        right--;
      }
    }
  }
  return res;
};
```



#### [16] 3Sum Closest

> Given an array `nums` of *n* integers and an integer `target`, find three integers in `nums` such that the sum is closest to `target`. Return the sum of the three integers. You may assume that each input would have exactly one solution.

注意这里不是找相等, 而是保留最接近target的值, 实现方法类似.

与target比较, 由于一直在找最接近的, 比target小就left++, 比target大就right--, 总之就是不断靠近target.

```javascript
var threeSumClosest = function (nums, target) {
  nums = nums.sort((a, b) => a - b);
  let closest = nums[0] + nums[1] + nums[2];
  let diff = Math.abs(closest - target);
  let len = nums.length;
  for (let i = 0; i < len - 2; i++) {
    // 由于数组是排好序的
    // 如果nums[i] * 3 > target, 则 nums[i]+nums[i+1]+nums[i+2] 是接下来遍历的最小值
    // 后面差距只会越来越大
    // 将接下来最小值与当前最小值closest分别与target比较, 返回与target差距较小的那个值
    // 优化部分
    if (nums[i] * 3 > target) {
      let cDiff = Math.abs(closest - target);
      let tempMin = nums[i] + nums[i + 1] + nums[i + 2];
      let tDiff = Math.abs(tempMin - target);
      return cDiff < tDiff ? closest : tempMin;
    }
    // 双指针 遍历数组剩余元素
    let left = i + 1, right = len - 1;
    while (left < right) {
      let sum = nums[i] + nums[left] + nums[right];
      let newDiff = Math.abs(sum - target);
      if (newDiff == 0) {
        return target
      }
      if (newDiff < diff) {
        diff = newDiff;
        closest = sum;
      }
      if (sum < target)
        left++;
      else
        right--;
    }
  }
  // console.log('closest:', closest);
  return closest;
};
```



#### [18] 4Sum

> Given an array `nums` of *n* integers and an integer `target`, are there elements *a*, *b*, *c*, and *d*in `nums` such that *a* + *b* + *c* + *d* = `target`? Find all unique quadruplets in the array which gives the sum of `target`.
>
> **Note:**
>
> The solution set must not contain duplicate quadruplets.

有了双指针, nSum都可解, 不过这个嵌套有点多,估计有更巧妙的解法.

```javascript
var fourSum = function (nums, target) {
  nums = nums.sort((a, b) => a - b);
  let len = nums.length, res = [];
  for (let i = 0; i < len - 3; i++) {
    // 去重
    if (nums[i] == nums[i - 1] && i > 0)
      continue;

    for (let j = i + 1; j < len - 2; j++) {

      if (nums[j] == nums[j - 1] && j > i + 1)
        continue;
        
      let t = target - nums[i] - nums[j];
      let left = j + 1, right = len - 1;

      while (left < right) {
        let twoSum = nums[left] + nums[right];
        if (twoSum == t) {
          res.push([nums[i], nums[j], nums[left], nums[right]]);
          left++;
          right--;
          // 去重
          while (left < right && nums[left] == nums[left - 1])
            left++;
          while (left < right && nums[right] == nums[right + 1])
            right--;
        } else if (twoSum > t) {
          right--;
        } else {
          left++;
        }
      }
    }
  }
  // console.log(res);
  return res;
};
```



#### [283] Move Zeroes

> Given an array `nums`, write a function to move all `0`'s to the end of it while maintaining the relative order of the non-zero elements.
>
> **Note**:
>
> 1. You must do this **in-place** without making a copy of the array.
> 2. Minimize the total number of operations.



双指针解法, 一次成功.

```javascript
// solution: two pointers
// 借鉴之前的做题经验, 这题算是完成的比较快
var moveZeroes = function (nums) {
  let len = nums.length, i = 0;
  for (let j = 0; j < len; j++) {
    if (nums[j] != 0) {
      nums[i] = nums[j];
      i++;
    }
  }
  for (let t = i; t < len; t++) {
    nums[t] = 0;
  }
  // console.log(nums);
  return nums;
};
```



#### [66] Plus One

> Given a **non-empty** array of digits representing a non-negative integer, increment one to the integer.
>
> The digits are stored such that the most significant digit is at the head of the list, and each element in the array contains a single digit.
>
> You may assume the integer does not contain any leading zero, except the number 0 itself.



主要是运用数组特性.不是很难, 理解题意即可.

还挺多人不喜欢这道题的, 可能觉得太弱智了?...

```javascript
var plusOne = function (digits) {
  let len = digits.length;
  // c 表示进位
  let i = len - 1, c = 1;
  while (i >= 0) {
    // 没有向前进位, 就 +1 结束
    if (digits[i] + c < 10) {
      digits[i]++;
      break;
    } else if (i == 0) { 
      // 首位元素 +1 后有进位, 向数组头部插入 1 结束
      digits[i] = 0;
      digits.unshift(1);
      break;
    } else {
      // 不是首位元素 +1 后有进位
      // 当前元素设为 0 , 继续看更高位元素
      digits[i] = 0;
      i--;
    }
  }
  // console.log(digits);
  return digits;
};
```



#### [88] Merge Sorted Array

> Given two sorted integer arrays *nums1* and *nums2*, merge *nums2* into *nums1* as one sorted array.
>
> **Note:**
>
> - The number of elements initialized in *nums1* and *nums2* are *m*and *n* respectively.
> - You may assume that *nums1* has enough space (size that is **equal** to *m* + *n*) to hold additional elements from *nums2*.

我用的方法比较死板, 感觉没有什么难度. 就是分情况讨论. 

不过用到了 js 里 Array 的特性.

```javascript
var merge = function (nums1, m, nums2, n) {
    // 说明没有自己的元素
    if (m == 0) {
        for (let i = 0; i < n; i++) {
            nums1[i] = nums2[i];
        }
    } else {
        let len = nums1.length;
        let i = 0;
        let j = m;
        let k = 0;
        while (i < len && k < n) {

            if (nums2[k] >= nums1[i] && nums2[k] < nums1[i + 1]) {
                nums1.splice(i + 1, 0, nums2[k]);
                nums1.pop();
                j++;
                k++;
            } else if (nums2[k] >= nums1[j - 1]) {
                nums1[j] = nums2[k];
                j++;
                k++;
            } else if (nums2[k] <= nums1[0]) {
                nums1.unshift(nums2[k]);
                nums1.pop();
                j++;
                k++;
                i = 0;
                continue;
            }
            i++;
        }
    }
};
```



#### [80] Remove Duplicates from Sorted Array II

> Given a sorted array *nums*, remove the duplicates [**in-place**](https://en.wikipedia.org/wiki/In-place_algorithm) such that duplicates appeared at most *twice* and return the new length.
>
> Do not allocate extra space for another array, you must do this by **modifying the input array [in-place](https://en.wikipedia.org/wiki/In-place_algorithm)** with O(1) extra memory.

**时间复杂度**: 遍历整个数组 $O(N)$

**空间复杂度**: 仅使用两个index: left和right标记数组元素位置, $O(1)$ 

使用splice方法直接修改数组. (这个方法的实现原理需要了解一下 TODO)

```javascript
// 数组是排好序的
// 不能使用额外空间,直接修改数组
// 使所有元素出现次数最多不超过2次
var removeDuplicates = function (nums) {
  let len = nums.length;
  let left = 0, right = 1;
  while (right < len) {
    if (nums[right] == nums[left] && right - left == 1) {
      right++;
      continue;
    }
    // 说明至少是第三个相同的元素
    // 需要将该元素删除
    if (nums[right] == nums[left] && right - left > 1) {
      nums.splice(right, 1);
      // 删除元素时会影响原数组的长度
      // 但是right仍然需要控制不能超过数组的边界
      len = nums.length;
      continue;
    }
    // right和left指向的元素不同
    left = right;
    right = right + 1;
  }
  // 当right==len的时候,说明数组遍历结束
  // 返回当前删除重复元素后的数组长度
  return len;
};
```



#### [202] Happy Number

> Write an algorithm to determine if a number `n` is "happy".
>
> A happy number is a number defined by the following process: Starting with any positive integer, replace the number by the sum of the squares of its digits, and repeat the process until the number equals 1 (where it will stay), or it **loops endlessly in a cycle** which does not include 1. Those numbers for which this process **ends in 1** are happy numbers.
>
> Return True if `n` is a happy number, and False if not.

复杂度分析有点困难, 以及为什么只有循环或者归1两种结果, 而不是无穷大.

```javascript
// solution 1: 快慢指针法
// 快慢指针找循环
// 在平方累加的过程中, 要不最后等于1, 要不最后陷入循环, 至于为什么不是无穷大
// 需要再琢磨一下
var isHappy1 = function (n) {
  // 慢指针一次计算一步, 快指针一次计算两步
  // 如果两个指针的值一样, 说明进入循环
  // 否则, 快指针一定比慢指针率先到达1
  // 初始化快慢指针
  let slow = getPow(n), fast = getPow(getPow(n));
  while (fast != 1 && fast != slow) {
    slow = getPow(slow);
    fast = getPow(fast);
    fast = getPow(fast);
  }
  return fast == 1 ? true : false;
};

// 计算某个数的各个数位上的平方
var getPow = (n) => {
  let sum = 0;
  while (n > 0) {
    let num = n % 10;
    sum += num * num;
    n = Math.floor(n / 10);
  }
  return sum;
}

// solution 2: 使用hashmap来判断值有无重复 聪儿判断有无循环
var isHappy = function (n) {
  let map = {};
  let res = getPow(n);
  // map中没有该计算结果
  // 则添加进map中
  // 不是undefined 说明该计算结果之前已经被塞进map中
  // 得到循环
  while (map[res] == undefined && res != 1) {
    map[res] = '1';
    res = getPow(res);
  }
  return res == 1 ? true : false;
}
```

