---
title: 刷題練習 - Maximum Depth of Binary Tree 解題策略
date: 2025-05-01 13:01:11
tags: ["Leetcode"]
categories: Leetcode
---

Leetcode 題目多樣，尤其是「二元樹（Binary Tree）」相關題型，容易讓人看了發愣。

本篇文章將教你如何釐清解法邏輯，並將二元樹題型拆解為兩大類解題策略，搭配範例與模板，幫助你用 DFS（深度優先搜尋）更系統地解題

###  類型一：遍歷型（Traversal Type）

這類題目需要「走過整棵樹的所有節點」，通常是為了計算某種總量，例如：

- 節點總數
- 最大深度
- 所有路徑總和
- 葉子節點的數量

解題思路

可根據需求使用：

- **後序遍歷 + 回傳值 return** → 適合需要組合子樹結果的情況  
- **前序遍歷 + 狀態變數追蹤** → 適合需要累加或記錄路徑狀態的情況  


#### 模板（後序 + return）

```javaScript
function dfs(node) {
  if (node === null) return baseValue;
  const left = dfs(node.left);
  const right = dfs(node.right);
  return combine(left, right, node);
}
```
#### 模板（前序 + 狀態變數）

```javaScript
let result = 0;
let pathDepth = 0;

function traverse(node) {
  if (node === null) return;

  pathDepth++; // 遞迴深入，深度 +1
  if (node.left === null && node.right === null) {
    result = Math.max(result, pathDepth); // 若為葉子，更新最大深度
  }

  traverse(node.left);
  traverse(node.right);

  pathDepth--; // 回歸
}
```

## Maximum Depth of Binary Tree 


題目連結：[104. Maximum Depth of Binary Tree](https://leetcode.com/problems/maximum-depth-of-binary-tree/)  

題目描述:
給定一棵二元樹，請求出它的「最大深度」——也就是從根節點走到最遠的葉子節點所需經過的節點數量。

解題思路：使用前序遍歷 + 狀態變數追蹤

1. 使用 depth 追蹤當前所在第幾層

2. 每遇到一個葉子節點，就與 `result` 比較並更新最大值

3. 所有節點遍歷完畢後，`result` 即為最大深度


```javaScript
/**
 * Definition for a binary tree node.
 * function TreeNode(val, left, right) {
 *     this.val = (val===undefined ? 0 : val)
 *     this.left = (left===undefined ? null : left)
 *     this.right = (right===undefined ? null : right)
 * }
 */
/**
 * @param {TreeNode} root
 * @return {number}
 */
var maxDepth = function(root) {
    let depth = 0 
    let result = 0
    const traverse = (node) =>{
		    
        if(node === null) return
        // 1. 進入節點：深度 +1
        depth++
        // 2. 如果是葉子（左右都沒有子節點），就更新 res
        if(node.left === null && node.right === null){
            result = Math.max(result, depth)
        }
        // 3. 繼續遞歸兩側
        traverse(node.left)
        traverse(node.right)
         // 4. 離開節點：回到父層，深度 -1
        depth--
    }
    traverse(root)
    return result

};
```

### 第二類: Comparison 型 - 比較兩棵/兩節點

這類題目不再只是單純遍歷，而是需要「比較兩個節點的結構與數值是否一致」，例如：

- 判斷兩棵樹是否相同（Same Tree）
- 判斷是否為對稱樹（Symmetric Tree）

### 解題思路

1. 同步比較兩個節點 `p` 與 `q`
2. 確認三件事：
   - 是否都為 `null`
   - 是否有一個是 `null`（另一個不是）
   - 兩節點的值是否相等
3. 最後遞迴比對左子樹與右子樹是否一致


```javaScript
function compare(p, q) {
  if (p === null && q === null) return true;
  if (p === null || q === null) return false;
  if (p.val !== q.val) return false;
  return compare(p.left, q.left) && compare(p.right, q.right);
}
```

### 呼應 Maximum Depth of Binary Tree 

```javaScript
var maxDepth = function(root) {
  if (!root) return 0;
  let leftMax  = maxDepth(root.left);
  let rightMax = maxDepth(root.right);
  return 1 + Math.max(leftMax, rightMax);
};

```