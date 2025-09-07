---
title: 刷題練習 - Majority Element
date: 2022-11-10 10:36:50
tags: ["LeetCode"]
categories: LeetCode
---

Given an array nums of size n, return the majority element.

The majority element is the element that appears more than ⌊n / 2⌋ times. You may assume that the majority element always exists in the array.

Example 1:

```
Input: nums = [3,2,3]
Output: 3
```
Example 2:

```
Input: nums = [2,2,1,1,1,2,2]
Output: 2
```

在一開始觀察題目，會直覺想說要找出陣列中出現最多次的元素，此外也注意到題目說明有提及運用 n/2 與陣列長度比較出現次數。
在一開始會傾向使用 雜湊表 來進行解題，如同找出陣列為一值的方式找出答案。

```javaScript
var majorityElement = function (nums) {
  let newObj = {}
  for (let num of nums) {
    // console.log(num)
    (!newObj[num]) ? newObj[num] = 1 : newObj[num]++
  }
  // console.log(newObj)
  let nNum = nums.length / 2
  // console.log('nNum', nNum)
  for (let key in newObj) {
    if (newObj[key] > nNum) return key
  }

};
```

另外也發現可以使用，Boyer–Moore majority vote algorithm(摩爾投票算法)來解題。
宣告變數 result , 以及計算變數。
將陣列loop ，判斷陣列元素有無出現進行 count 的加減
以 `majorityElement2([4, 5, 5, 4, 4])` 為範例：
第一個元素 4 ，因為 count === 0 , result === 4。
進入第二個元素 5，因為 result === 4 進入判斷式 else => count-- 。 所以 count === 0 。
進入第三個元素 5，因為 result === 5 進入判斷式 result === elem  => count++。 所以 count === 1 。
進入第四個元素 4，因為 result === 5 進入判斷式 else => count-- 。 所以 count === 0 。
進入第五個元素 4，因為 result === 4 進入判斷式 result === elem  => count++。 所以 count === 1 。

在迴圈的最後元素 4 ，為目前 count 為 1
 
```javaScript

var majorityElement2 = function (nums) {
  let result;
  let count = 0;

  for (let elem of nums) {
    if (count === 0) {
      result = elem;
    }
    if (result === elem) {
      count++;
    } else {
      count--;
    }
  }

  return result;

};

```


---

229. Majority Element II
此題也是使用摩爾投票法進行解題

Given an integer array of size n, find all elements that appear more than ⌊ n/3 ⌋ times.


Example 1:

```
Input: nums = [3,2,3]
Output: [3]
```

Example 2:

```
Input: nums = [1]
Output: [1]
```

Example 3:

```
Input: nums = [1,2]
Output: [1,2]
```

```javaScript
var majorityElement = function (nums) {
  // 只要是 > n/3 就進入陣列
  let candidate1 = null;
  let candidate2 = null;
  let count1 = 0;
  let count2 = 0;

  for (let num of nums) {

    if (candidate1 === num) {
      count1++
    } else if (candidate2 === num) {
      count2++
    } else if (count1 === 0) {
      candidate1 = num;
      count1++
    } else if (count2 === 0) {
      candidate2 = num;
      count2++
    } else {
      count1--;
      count2--;
    }
  }

  console.log('data:', candidate1, candidate2)
  count1 = 0;
  count2 = 0;
  const res = [];

  limit = Math.floor(nums.length / 3)

  for (let num of nums) {
    if (candidate1 === num) count1++;
    else if (candidate2 === num) {
      count2++;
    }
  }
  if (count1 > limit) res.push(candidate1)
  if (count2 > limit) res.push(candidate2)
  console.log(res, count1, count2)
  return res

};
majorityElement([1, 2])
```



![](https://i.imgur.com/T2y5qLa.jpg)
