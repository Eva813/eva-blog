---
title: 刷題練習 - Valid Parentheses
date: 2022-09-24 08:32:55
tags: ["Leetcode"]
categories: Leetcode
---

  對於刷題總時敬而遠之，記得剛開始學習 JavaScript 時，有挑幾題來練習，看到題目後完全不知道該如何著手，甚至有些題目還要反覆看幾次才比較理解。 畢竟也踏入前端工程師近一年的時間，使用 JavaScript 也有兩年了，該是好好鑽研練習 Leetcode。

題目：
  Given a string s containing just the characters '(', ')', '{', '}', '[' and ']', determine if the input string is valid.

  An input string is valid if:

  Open brackets must be closed by the same type of brackets.
  Open brackets must be closed in the correct order.
  Every close bracket has a corresponding open bracket of the same type.

  以字串帶入，並要確認傳入的字串中的大括號, 中括號等是否有兩倆配對

Example 1: 
```
Input: s = "()"
Output: true
```
```
Input: s = "()[]{}"
Output: true
```
```
Input: s = "(]"
Output: false
```

1. 製作一個物件，為括號的配對
2. 存放括號的空物件
3. 將字串進行迭代判斷

```
  i 會loop 此字串
  "([]){}"

  第一圈：遇到 "("
  stack = [ ")"]

  第二圈：遇到 "["
  stack = [ ")","]"]

  第三圈：遇到 "]" , 此時，stack = [ ")","]"]
  進入第二個判斷：stack[stack.length - 1] === ch
  陣列的最後一個是否與 i=2 ,ch = "]" 互相配對，若配對成功則從陣列中刪除

  第四圈：遇到 ")" , 此時，stack = [ ")"]
  進入第二個判斷：stack[stack.length - 1] === ch
  陣列的最後一個是否與 i=3 ,ch = ")" 互相配對，若配對成功則從陣列中刪除

  第五圈：遇到 "{" , 此時，stack = []
  stack = [ "}"]

  第六圈：遇到 "}" , 此時，stack = [ "}"]
  進入第二個判斷：stack[stack.length - 1] === ch
  陣列的最後一個是否與 i=5 ,ch = "}" 互相配對，若配對成功則從陣列中刪除

    最後跳出陣列確認 stack.length === 0 => 為 0 表示皆配對成功

  
```

```JavaScript
  var isValid = function (s) {
    const hashMap = { "(": ")", "{": "}", "[": "]" };
    const stack = [];
    for (let ch of s) {
      if (hashMap[ch]) {
        // ch is an opening bracket
        stack.push(hashMap[ch]);
      } else if (stack.length > 0 && stack[stack.length - 1] === ch) {
        //當空陣列裡面有值且陣列的最後一個值和 key 是一樣的就從陣列中刪除
        // ch is a closing bracket and top of stack matches
        stack.pop();
      } else {
        // ch is a closing bracket and top of the stack doesn't match
        return false;
      }
    }
    return stack.length === 0;
  };
```


