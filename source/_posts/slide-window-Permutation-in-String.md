---
title: 刷題練習 - Permutation in String
date: 2025-04-15 20:34:43
tags: [LeetCode, slide-window]
categories: LeetCode
---

練習來自 [labuladong 的演算法筆記](https://labuladong.online/algo/essential-technique/sliding-window-framework/) 的滑動視窗演算法核心程式碼模板。

## 基本設置:
用兩個指針來界定一個區間，也就是一個「窗口」：
- 左指針 left：表示窗口的起始位置。
- 右指針 right：表示窗口的尾部（通常採用左閉右開區間 [left, right)）。

窗口內的資料會隨著 left 和 right 的移動而動態改變。解題的關鍵在於：

- 擴大窗口：不斷將新的元素加入窗口，並更新窗口內的狀態或統計（例如字元出現次數）。
- 檢查窗口是否滿足條件：根據題目要求檢查當前窗口是否為合法解。
- 收縮窗口：如果當前窗口已滿足條件，嘗試從左側移除元素以找到更優（例如更短）的解，同時更新狀態。

## 模板

```javascript
var slidingWindow = function(s) {
    // 使用合適的資料結構記錄視窗中的資料，根據具體題目變通。
    // 例如：記錄字元出現的次數，或累加窗口內元素的和。
    let window = {};  

    let left = 0, right = 0;
    while (right < s.length) {
        // 取出將要加入視窗的字元
        let c = s[right];
        right++;  // 擴大窗口

        // 更新窗口內資料
        // 例如：window[c] = (window[c] || 0) + 1;
        // 根據具體題目邏輯進行更新

        // *** Debug 輸出位置 *** 
        // console.log("窗口: [%d, %d)", left, right);

        // 當窗口不再滿足題目要求時，收縮窗口
        while (/* 判斷窗口需要收縮的條件 */) {
            // 取得將要移除的字元（為使變數名稱更具描述性可用 removedChar）
            let removedChar = s[left];
            left++;  // 縮小窗口

            // 更新移除字元對窗口資料的影響
            // 例如：if (window[removedChar] ...) { ... }
        }
    }
};

```

## [題目](https://leetcode.com/problems/permutation-in-string/description/)

Given two strings s1 and s2, return true if s2 contains a permutation of s1, or false otherwise.

In other words, return true if one of s1's permutations is the substring of s2.

給定兩個字串 s1 和 s2，若 s2 中包含 s1 的某個排列，則返回 true；否則返回 false。

換句話說，若 s1 的某個排列是 s2 的子字串，則返回 true。

Example 1:

Input: s1 = "ab", s2 = "eidbaooo"
Output: true
Explanation: s2 contains one permutation of s1 ("ba").

<!-- more -->

### 解題思路：
1. 建立需求資料結構：
- 先建立一個哈希表 need，記錄 s1 中每個字元需出現的次數。這代表著當視窗中相應字元的出現次數達到 need 內的數量，該字元即滿足要求。

2. 初始化視窗相關變數：

- 建立一個哈希表 window 用來記錄當前視窗內各字元出現次數。
- 初始化左右指標 left 與 right 為 0。
- 使用變數 valid 來計算目前在視窗中滿足 need 要求的字元種類數。
- 由於目標是找出是否存在 s1 中排列（即字元組合相同）出現在 s2 中，所以要求的視窗大小固定為 s1 長度。

3. 擴大視窗：
利用 right 指標不斷擴大窗口，將 s2 中的字元依序加入窗口中，並更新 window 的計數。如果某個字元剛好滿足 need 的數量要求，則 valid 加 1。

4. 收縮視窗並檢查條件：
當視窗的大小等於 s1 的長度時，此時視窗中的所有字元構成一個候選解。

- 檢查 valid 是否等於 need 中的字元種類數。如果相等，說明當前窗口中的字元頻率與 s1 完全一致，符合排列條件，直接返回 true。
- 如果不符合，則將左側字元移除（收縮窗口），並更新 window 中該字元的計數以及 valid（若該字元數量從滿足變成不足）。
- 窗口收縮後繼續擴大右側以遍歷 s2。

5. 結束：
遍歷完 s2 後，若未能找到符合條件的窗口，則返回 false。

### 程式碼

```javascript
/**
 * @param {string} s1
 * @param {string} s2
 * @return {boolean}
 */
var checkInclusion = function(s1, s2) {
    let window = {}
    let need = {}
    let right = 0;
    let left = 0;
    let valid = 0;
    let required = Object.keys(need).length;  // 需要滿足的字元種類數
    for(let item of s1){
        need[item] = (need[item] || 0) + 1;
    }
    while(right < s2.length){
        let char = s2[right]
        right++
        if(need[char]){
            window[char] = (window[char] || 0) + 1;
            if(window[char] === need[char]){
                valid++
            }
        }

        // 當窗口長度達到 s1 的長度時，開始收縮窗口
        while(right - left >= s1.length){
            if(valid === Object.keys(need).length) return true    

            let removedChar = s2[left]
            left++
            // 更新移出字元對窗口資料的影響
            if(need[removedChar]){ 
                if(window[removedChar] === need[removedChar]){
                    valid--
                }
            }
            window[removedChar]--
        }

    }
    return false
};
```

## 時間與空間複雜度分析
1. 時間複雜度： O(n + m)，其中 n 為 s2 的長度，m 為 s1 的長度。
2. 空間複雜度： 最壞情況下為 O(m)。

- 綜合空間複雜度：
如果不假設字元集是固定大小（例如僅限小寫英文字母），那麼最壞情況下整體空間複雜度為 O(m)。
注意： 在許多實際情況下（例如字元集固定的狀況），可能會將此空間複雜度視為 O(1)；但從最壞情況分析角度，仍然為 O(m)

