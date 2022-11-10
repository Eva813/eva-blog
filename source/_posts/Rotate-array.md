---
title: 刷題練習 - Rotate Array
tags: ["Leetcode"]
categories: Leetcode
---


Given an array, rotate the array to the right by k steps, where k is non-negative.

Example 1:

```
Input: nums = [1,2,3,4,5,6,7], k = 3
Output: [5,6,7,1,2,3,4]
Explanation:
rotate 1 steps to the right: [7,1,2,3,4,5,6]
rotate 2 steps to the right: [6,7,1,2,3,4,5]
rotate 3 steps to the right: [5,6,7,1,2,3,4]
```
```
Example 2:

Input: nums = [-1,-100,3,99], k = 2
Output: [3,99,-1,-100]
Explanation: 
rotate 1 steps to the right: [99,-1,-100,3]
rotate 2 steps to the right: [3,99,-1,-100]
```

1.由上述範例可以觀察到，根據 k 值進行抓取最後的元素至最前面，以此類推下去。
當 k 為某值的時後，會由陣列最後方取的該數目元素。
以下為直覺解法:

```javaScript
//會有 問題：Time Limit Exceeded
const rotateArray1 = function (nums, k) {
  //unshift 將給入的元素值放到最前面
  for (let i = 0; i < k; i++) {
    console.log('nums.pop()', nums.pop())
    nums.unshift(nums.pop());
  }
  console.log('num', nums)
  return nums;
}

```


2.也可以先將陣列進行反轉 reverse ,再依據 k 值 來進行前後段區分，來進行反轉


```javaScript 
const revNum = (nums, start, end) => {
  while (start < end) {

    [nums[start], nums[end]] = [nums[end], nums[start]]
    console.log('[nums[start], nums[end]]', [nums[start], nums[end]])
    start++;
    end--;
  }
  console.log(nums)
}
//end -> 陣列長度-1
// revNum([1, 2, 3, 4], 0, 3)

const rotateArray2 = function (nums, k) {
  k = k % nums.length
  //先整個陣列 reverse
  nums.reverse()
  revNum(nums, 0, k - 1)
  revNum(nums, k, nums.length - 1)
}

```
