---
title: 刷題練習 - Merge-Sorted-Array
date: 2025-10-04 21:32:53
tags: ["LeetCode"]
categories: LeetCode
---

You are given two integer arrays `nums1` and `nums2`, sorted in **non-decreasing order**, and two integers `m` and `n`, representing the number of elements in `nums1` and `nums2` respectively.

**Merge** `nums1` and `nums2` into a single array sorted in **non-decreasing order**.

The final sorted array should not be returned by the function, but instead be *stored inside the array* `nums1`. To accommodate this, `nums1` has a length of `m + n`, where the first `m` elements denote the elements that should be merged, and the last `n` elements are set to `0` and should be ignored. `nums2` has a length of `n`.

給定兩個整數陣列 `nums1` 和 `nums2`，都按照**非遞減順序**排列，以及兩個整數 `m` 和 `n`，分別代表 `nums1` 和 `nums2` 中元素的個數。
請將 `nums1` 和 `nums2` **合併**為一個按**非遞減順序**排列的陣列。
最終的排序陣列**不應該由函數返回**，而是要**儲存在陣列 `nums1` 內部**。為了實現這一點，`nums1` 的長度為 `m + n`，其中前 `m` 個元素表示應該合併的元素，後 `n` 個元素設為 `0` 並且應該被忽略。`nums2` 的長度為 `n`。

目標：將 nums2 合併於 nums1
- 必須在 `nums1` 內部完成合併，不能使用額外的陣列空間
- **不返回值**：函數不需要 return，直接修改 `nums1`

<!-- more -->

## 解題思路
直覺做法

但這樣做會創造新陣列，也忽略原本已經 sort 過得特性
```javascript
var merge = function(nums1, m, nums2, n) {
    // 1. 取出 nums1 的有效元素
    let combined = nums1.slice(0, m).concat(nums2);
    
    // 2. 排序
    combined.sort((a, b) => a - b);
    
    // 3. 放回 nums1
    for (let i = 0; i < combined.length; i++) {
        nums1[i] = combined[i];
    }
};
```
### 優化做法

既然題目已提供 m 和 n，我們可以利用它們作為指針來追蹤有效元素位置。
核心概念是：
nums1 的尾部有足夠空間放入合併結果。
從陣列尾端開始比較，每次把較大的數字放到最後。
指針逐步向前移動，直到其中一方陣列元素用完。


### 範例程式碼

```javascript
/**
 * @param {number[]} nums1
 * @param {number} m
 * @param {number[]} nums2
 * @param {number} n
 * @return {void} Do not return anything, modify nums1 in-place instead.
 */
var merge = function(nums1, m, nums2, n) {
    if(n === 0  && nums1.length === m) return nums1
    // nums 要被替換，並且要與 nums2 比較，讓 2 放入該位置 
    // 我可以根據 m , n 來得到有效的位置（index）
    let p1 = m - 1 
    let p2 = n - 1 
    let finalP= m + n -1

     // 從後往前，選大的
     while( p1 >= 0 && p2 >= 0){
        if( nums1[p1] > nums2[p2]){
            // 替換最後一
            nums1[finalP]  = nums1[p1]
            p1--
        }else{
            nums1[finalP]  = nums2[p2]
            p2--
        }
        // 因為他是他們的最後，所以已經將最大值放到陣列最後
        finalP--
     }

     while(p2 >= 0){
        nums1[finalP] = nums2[p2]
        p2--
        finalP--
     }

};
```

## 解析

### 為什麼用 while (p1 >= 0 && p2 >= 0)？
因為只要任一陣列已被取完，就不再需要比較，避免越界。

### 為什麼從後往前？

因為這樣可以避免覆蓋到尚未處理的元素，確保每次都將最大的元素放到正確的位置。

### 為什麼只需要處理 nums2 剩餘？

- **nums2 有剩餘**：表示這些元素比 nums1 中已處理的元素都小，需要複製到 nums1 前方對應位置
- **nums1 有剩餘**：這些元素已經在 nums1 的正確位置上，無需移動

### 時間與空間複雜度
時間：O(m + n)
空間：O(1)（原地修改）