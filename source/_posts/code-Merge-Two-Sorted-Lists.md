---
title: 刷題練習 - Merge Two Sorted Lists
tags: ["Leetcode"]
categories: Leetcode
---

   首先要先了解 linked lists 和陣列是不一樣的！！
   linked lists是一種常見的資料結構，其使用node(節點)來記錄、表示、儲存資料(data)，並利用每個node中的pointer指向下一個node 

## 題目：
You are given the heads of two sorted linked lists list1 and list2.

Merge the two lists in a one sorted list. The list should be made by splicing together the nodes of the first two lists.

Return the head of the merged linked list.

![](https://i.imgur.com/I645KZq.png)



注意類似題目要設立一個 dummy 節點
* 如何訪問list 中某個節點的 值，要從第一個節點開始，透過 next 依序訪問下一個節點重複操作直到抵達目標節點並讀取 value
* 訪問第一個節點為 list.val  ; 訪問第二個節點 list.next.val ; 訪問第三個節點list.next.next.val 
* 要將兩個 list 合併的方法，每次比較兩個 而最小的數挑出，放入新的 list

```JavaScript
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

  var mergeTwoLists = function (l1, l2) {
    //current
    const dummy = new ListNode(-Infinity)
    //point dummy node
    let prev = dummy;
    while (l1 && l2) {
      if (l1.val <= l2.val) {
        prev.next = l1;
        //移動 prev list
        prev = l1
        //鏈錶指針 l1 就往後移動
        l1 = l1.next
      } else {
        prev.next = l2;
        prev = l2
        l2 = l2.next
      }
    }
    //如果 l1 是 null ,就是 l2
    if (!l1) prev.next = l2;
    if (!l2) prev.next = l1;
    return dummy.next
  };

  mergeTwoLists([1, 100], [3, 4, 6, 8])
```
