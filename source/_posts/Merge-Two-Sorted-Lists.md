---
title: 刷題練習 - Merge-Two-Sorted-Lists
date: 2025-10-04 19:59:06
tags: ["LeetCode"]
categories: LeetCode
---

## 題目描述
You are given the heads of two sorted linked lists list1 and list2.

Merge the two lists into one sorted list. The list should be made by splicing together the nodes of the first two lists.

Return the head of the merged linked list.

給定兩個已排序鏈結串列的頭節點 list1 和 list2。
將兩個串列合併為一個已排序的串列。該串列應透過拼接前兩個串列的節點來建立。
回傳合併後鏈結串列的頭節點。


Example 1:
Input: list1 = [1,2,4], list2 = [1,3,4]
Output: [1,1,2,3,4,4]

Example 2:
Input: list1 = [], list2 = []
Output: []

Example 3:
Input: list1 = [], list2 = [0]
Output: [0]

## 解題思路
我一開始想法，會需要 2 迴圈去比較，然後組出一個新的 list node，最後再將新的 list node 回傳。
但是這樣的話，時間複雜度會是 O(n^2)，因為每次都要從頭開始比較。
後來想說，因為兩個 list 都是已排序好的，所以可以用雙指標的方式，一次比較兩個 list 的頭節點，將較小的節點加入新的 list node，然後將該指標往後移動一位，這樣時間複雜度就會是 O(n)，因為每個節點只會被比較一次。

## 範例程式碼
```javascript
/**
 * Definition for singly-linked list.
 * function ListNode(val, next) {
 *     this.val = (val===undefined ? 0 : val)
 *     this.next = (next===undefined ? null : next)
 * }
 */
/**
 * @param {ListNode} list1
 * @param {ListNode} list2
 * @return {ListNode}
 */
var mergeTwoLists = function(list1, list2) {
        let head = null
        let nowNode = null 
        let list1pointer = list1;
        let list2pointer = list2;
		// 兩兩比較
        if(list1 === null && list2 === null) return head
        if(list1 === null && list2 !== null) return list2
        if(list1 !== null && list2 === null) return list1
        
  
        // 決定 head
        // 因為當你選擇了 list1pointer 作為頭節點後，你應該：
            // 移動 list1pointer 指針：list1pointer = list1pointer.next
            // 設定 nowNode 為當前的尾節點：nowNode = head
        if(list1pointer.val <= list2pointer.val ){
            head = list1pointer
            list1pointer = list1pointer.next
        }else{
             head = list2pointer
             list2pointer = list2pointer.next
        }
        nowNode = head

        //進入迴圈檢查
        while(list1pointer !== null && list2pointer !== null){
            if(list1pointer.val <= list2pointer.val){
                nowNode.next =  list1pointer
                list1pointer = list1pointer.next
            }else {
                nowNode.next =  list2pointer
                // 移動指針
                list2pointer = list2pointer.next
            }
            nowNode = nowNode.next;
        }
        // 最後要再檢查。其中一不為 null 
        if(list1pointer !== null){
            nowNode.next = list1pointer
        }else{
            nowNode.next = list2pointer
        }

        return head
    
};
```

<!-- more -->

## 說明
1. 在「**先決定 head**」這段:
    1. 透過判斷大小 選擇 `list1` 的當前節點作為合併鏈表的起始點
    2. list1pointer = list1pointer.next 
        - 將 `list1pointer` 移動到下一個節點
        - **原因**：因為當前節點已經被「消費」了，加入到合併鏈表中
    
    決定好 head ，要記得  nowNode = head
    
2. 進入 while 迴圈：因為我們 會讓 pointer 移動，可以用 list1pointer !== null  && list2pointer !== null 來判斷

    **`nowNode` 的角色**：它是合併鏈表的「尾指針」，始終指向當前合併鏈表的最後一個節點。

    **`nowNode.next` 的含義**：它是尾節點的 `next` 指針，也就是「下一個節點要插入的位置」。
    1. 在迴圈內部，我們需要比較 `list1pointer.val` 和 `list2pointer.val`
    2. 將比較結果的節點賦值給 `nowNode.next`**，並且要同步移動 list 的 pointer**

    **為什麼使用 `nowNode.next = list1pointer` 而不是 `nowNode = list1pointer`？**

    - `nowNode.next = list1pointer`：**修改節點內容**，將 `nowNode` 所指向節點的 `next` 屬性指向 `list1pointer`，把剩餘的鏈結串接到已建構的鏈結串列中，**保持鏈結串列的連接性**。
    - `nowNode = list1pointer`：只是**改變變數指向**，不會修改任何節點的 `next` 屬性，不會將節點串接到結果鏈結串列中。

    範例說明：
    ```
    目前狀態：
    結果鏈結串列: dummyHead -> 1 -> 2 -> (nowNode 在這裡)
    list1pointer: 3 -> 5 -> null
    list2pointer: null

    使用 nowNode.next = list1pointer：
    dummyHead -> 1 -> 2 -> 3 -> 5 -> null  ✓ 正確串接

    使用 nowNode = list1pointer：
    dummyHead -> 1 -> 2 -> null  ✗ 鏈結斷掉了
    nowNode 變數: 3 -> 5 -> null (獨立的，沒有連到結果鏈結串列)
    ```
3. 最後，因為剛剛迴圈是判斷 2 個 list pointer 不要有 null， 這樣就會有當其中一個走到 null 但另一 list 還有值，就要接上去。