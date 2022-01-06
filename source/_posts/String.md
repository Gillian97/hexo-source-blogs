---
title: String
tags:
  - LeetCode
categories: Algorithm
abbrlink: 9912b79f
description: 字符串相关题解
---

#### [43] Multiply Strings

> Given two non-negative integers `num1` and `num2` represented as strings, return the product of `num1` and `num2`, also represented as a string.

算法答题思路就是模拟乘法累加的过程. 

需要注意的是, js大数相加会丢失精度, 所以谨慎使用.

```javascript
/**
 * @param {string} num1
 * @param {string} num2
 * @return {string}
 */
// js大数相加容易丢失精度, 有安全范围, 不行
/**
 * 
 * 整个算法的过程模拟乘法的计算过程
 * 用一个数组来存储每一步计算的结果
 * 时间复杂度: n^2
 */
var multiply = function (num1, num2) {
  // 有一个参数为0,则结果为0
  if (num1 == '0' || num2 == '0')
    return '0';
  let len1 = num1.length,
    len2 = num2.length,
    len = len1 + len2;
  // 数组存储计算结果 长度暂时为两个字符串长度之和
  let resArr = new Array(len);
  // 初始化结果数组
  for (let i = 0; i < len; i++) {
    resArr[i] = 0;
  }
  // 循环反过来的原因是: 每次都是先拿因数1的每一位与因数2的同一位相乘
  for (let j = len2 - 1; j >= 0; j--) {
    let n2 = parseInt(num2[j]);
    // 计算结果放置的位置
    let pos = len - len2 + j;
    for (let i = len1 - 1; i >= 0; i--) {
      let n1 = parseInt(num1[i]);
      let res = n1 * n2;
      addNext(res, resArr, pos, 1);
      // 数位升高一位, 结果放置也要对应往左一位
      pos--;
    }
  }
  // 去除结果数组最左边的 0
  while (resArr[0] == '0') {
    resArr.shift()
  }
  return resArr.join('');
};

// flag 为 1 表示要与当前位置数值相加
// 为 0 则表示不用相加
var addNext = (num, resArr, pos, flag) => {
  let posNum = num % 10, // 取余数 置于当前位置
    addNum = Math.floor(num / 10); // floor 向下取整

  resArr[pos] = (flag == 1) ? resArr[pos] + posNum : posNum;
  resArr[pos - 1] += addNum;

  if (resArr[pos] >= 10) {
    // 与当前数值相加之后, 数值大于10, 则当前仍然需要进位
    // 但是不需要再与当前位置数值相加了
    addNext(resArr[pos], resArr, pos, 0);
  }
  // 进位最多只会等于 10
  if (resArr[pos - 1] == 10) {
    addNext(resArr[pos - 1], resArr, pos - 1, 0);
  }
}
```



#### [13] Roman to Integer

> Roman numerals are represented by seven different symbols: `I`, `V`, `X`, `L`, `C`, `D` and `M`.
>
> For example, two is written as `II` in Roman numeral, just two one's added together. Twelve is written as, `XII`, which is simply `X` + `II`. The number twenty seven is written as `XXVII`, which is `XX` + `V` + `II`.
>
> Given a roman numeral, convert it to an integer. Input is guaranteed to be within the range from 1 to 3999.

时间复杂度: 遍历整个字符串, $O(N)$

空间复杂度: 常数级别.

```javascript
// 罗马数字一共三种情况
// 百位数字>十位数字>个位数字
// 出现左侧数字比右侧数字小, 只会在某一个数位内部
var romanToInt = function (s) {
  // 从左往右遍历
  let pre = getVal(s[0]), sum = 0, num;
  let len = s.length;
  for (let i = 1; i < len; i++) {
    num = getVal(s[i]);
    // 前一个数字不小于后一个数字, 加上pre
    // 反之, 说明pre是某一数位内部的需要减去的数字, 则减去pre
    // 左侧的被减去的数字只出现一次或者不出现, 不会出现两次
    if (pre >= num) {
      sum += pre;
    } else {
      sum -= pre;
    }
    // 更新pre的值
    pre = num;
  }
  // 最后一位不会再比右边哪个元素小了
  // 所以需要将最后一位加上
  sum += getVal(s[len - 1]);
  return sum;
};

var getVal = (ch) => {
  switch (ch) {
    case 'I':
      return 1;
    case 'V':
      return 5;
    case 'X':
      return 10;
    case 'L':
      return 50;
    case 'V':
      return 5;
    case 'C':
      return 100;
    case 'D':
      return 500;
    case 'M':
      return 1000;
    default:
      return 0;
  }
}
```



#### [12] Integer to Roman

> Roman numerals are represented by seven different symbols: `I`, `V`, `X`, `L`, `C`, `D` and `M`.
>
> For example, two is written as `II` in Roman numeral, just two one's added together. Twelve is written as, `XII`, which is simply `X` + `II`. The number twenty seven is written as `XXVII`, which is `XX` + `V` + `II`.

主要是不同数位的数字转换, 需要对数位进行分情况讨论. 不同数位的相同数字, 对应使用的罗马字符也不同.

```javascript
// 主要思路就是, 逐步整除, 然后对商进行对应数位的转换
var intToRoman = function (num) {
  let items = [1000, 100, 10, 1];
  let n, res = ''; // res需要初始化 否则res+=操作时, 由于res的值为udefined, 会在结果头部留一个udefined
  // 从千位除到个位
  // 注意 用let i in items时, typeof i = string
  for (let i = 0; i < items.length; i++) {
    // 得到商
    n = Math.floor(num / items[i]);
    // 根据数位以及商的大小确定对应的罗马数字
    // 并对每一步得到的罗马数字进行拼接
    // 使用索引顺便标记数位
    res += toRoman(i, n);
    // 后一轮需要被整除的数是当前一轮整除后得到的余数
    num = num % items[i];
  }
  return res;
};

var toRoman = (type, n) => {
  let sma = '', mid = '', top = '';
  // 数位不同 相同数字的表达方法不同
  switch (type) {
    case 0: // 千位
      sma = 'M';
      break;
    case 1: // 百位
      sma = 'C'; mid = 'D'; top = 'M';
      break;
    case 2: // 十位
      sma = 'X'; mid = 'L'; top = 'C';
      break;
    case 3: // 个位
      sma = 'I'; mid = 'V'; top = 'X';
      break;
    default:
      break;
  }
  let roman = '';
  // 根据 n 的大小, 转换成对应的罗马数字
  // 千位不会超过3
  if (n >= 0 && n <= 3) { // [0,3]
    let i = 0;
    while (i < n) {
      roman += sma;
      i++;
    }
  } else if (n == 4 || n == 9) { // 4/9
    roman = n == 4 ? sma + mid : sma + top;
  } else { // [5, 8]
    let k = 0;
    roman = mid;
    while (k < n - 5) {
      roman += sma;
      k++;
    }
  }
  return roman;
}
```



#### [49] Group Anagrams

> Given an array of strings `strs`, group **the anagrams** together. You can return the answer in **any order**.
>
> An **Anagram** is a word or phrase formed by rearranging the letters of a different word or phrase, typically using all the original letters exactly once.



```javascript
// 将字符串按照构成分组
// 我的思路是: 将每个字符串按照字符拆分,然后排序,然后作为字典的key
// 相同key的就将元素index加入key对应的数组[]
// 将字符串按照构成分组
// 我的思路是: 将每个字符串按照字符拆分,然后排序,然后作为map的key
// 相同key的就将元素index加入key对应的数组[]
var groupAnagrams = function (strs) {
  let len = strs.length;
  if (len == 1) return [strs];
  let map = {}, key;
  for (let i = 0; i < len; i++) {
    // 对每个字符串的字符拆分-排序-重组为string
    // 以至于相同字母组成的str的key会一致
    key = strs[i].split('').sort().toString();
    // 看map是否存在
    // 不存在生成[]
    // 存在往[]里push元素
    if (map[key] == undefined) {
      map[key] = [i];
    } else {
      map[key].push(i);
    }
  }
  // map中value是数组, 包含字母组成相同的一组字符串的index
  // 遍历每一个数组, 将index替换为strs里对应的str
  for (let k in map) {
    for (let i = 0; i < map[k].length; i++) {
      map[k][i] = strs[map[k][i]];
    }
  }
  // 直接返回对象的values, 所有value被添加进[]并返回
  // 本来是又新创建了个数组来存储结果, 这样直接返回values更节省空间
  return Object.values(map);
};
```



#### [67] Add Binary

> Given two binary strings, return their sum (also a binary string).
>
> The input strings are both **non-empty** and contains only characters `1` or `0`.

时间复杂度:

假设两个串的长度分别为M/N, 则时间复杂度为 $O(max(M, N))$.

```javascript
var addBinary = function (a, b) {
  let i = a.length - 1, j = b.length - 1;
  let num, carry = false, sum = '', charA, charB;
  while (a[i] || b[j]) {
    // 考虑到两个串长度不一致时的处理
    charA = (a[i] == undefined) ? '0' : a[i];
    charB = (b[j] == undefined) ? '0' : b[j];
    // 由于二进制不是十进制加法, 所以需要对相加结果进行分类讨论
    if (charA == charB) {
      num = carry ? '1' : '0';
      carry = charA == '1';
    } else {
      num = carry ? '0' : '1';
    }
    sum = num + sum;
    i--;
    j--;
  }
  // 如果两数的每一位均已对应相加完成, 但是还有进位, 需要把进位也加入结果中
  sum = carry ? '1' + sum : sum;
  return sum;
};
```



#### [28] Implement strStr()

> Implement [strStr()](http://www.cplusplus.com/reference/cstring/strstr/).
>
> Return the index of the first occurrence of needle in haystack, or **-1** if needle is not part of haystack.

时间复杂度: 

设source长度为N, target长度为M, 最差的情况是: 一直遍历到最后的部分才知道是否有和target匹配的部分. O((N-M)*M). 不用遍历到source的最后一个元素, 只要剩余部分的长度不足M, 即可退出遍历.

```javascript
// 直观想法 使用双重循环 
// 最外层循环是对源字符串的遍历
// 需要考虑源字符串剩余长度不够匹配目标字符串的情况
// 建议使用source target这类有意义的变量命名
var strStr = function (haystack, needle) {
  // 目标串为'' 默认出现位置是0
  if (needle == '') return 0;
  // 源串为''(这里needle不为'') 则找不到
  if (haystack == '') return -1;
  for (let i = 0; i < haystack.length; i++) {
    if (haystack[i] == needle[0]) {
      if (isEqual(haystack, needle, i))
        return i;
      else
        continue;
    }
  }
  return -1
};

var isEqual = (haystack, needle, i) => {
  // 处理源字符串需要比较的串长度小于needle的长度的情况
  if (haystack.length - i < needle.length) return false;
  for (let j = 0; j < needle.length; j++) {
    if (needle[j] != haystack[i])
      return false
    else {
      i++;
    }
  }
  return true;
}
```

