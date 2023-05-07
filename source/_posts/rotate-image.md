---
title: Leetcode rotate-image
date: 2022-12-21 09:46:00
tags: ["Leetcode"]
categories: Leetcode
---

一開始看到題目，看到2維陣列就會有些緊張，想著是否要拆開，或是要如何從圖示去轉陣列，寫得很亂又雜還是解不出來。
看完其他教學才知道可以從斜對角開始調換，最後再將左右做調換。


You are given an n x n 2D matrix representing an image, rotate the image by 90 degrees (clockwise).

You have to rotate the image in-place, which means you have to modify the input 2D matrix directly. DO NOT allocate another 2D matrix and do the rotation.

 ![](https://i.imgur.com/0khxyek.png)


![](https://i.imgur.com/LjKoWWh.jpg)


```javascript
var rotate = function (matrix) {
  //斜對角
  for (let i = 0; i < matrix.length; i++) {
    for (let j = 0; j < matrix.length; j++) {
      let temp = matrix[i][j];
      matrix[i][j] = matrix[j][i];
      matrix[j][i] = temp;
    }
  }
  //reverse
  // Secondly Make Reflected Image Of Matrix
  for (let i = 0; i < matrix.length; i++) {
      //最左和最右調換，為兩兩調換，所以j的長度為matrix長度除以2
    for (let j = 0; j < matrix.length / 2; j++) {
      var Temp = matrix[i][j]
      //右側最後的值為長度-1
      matrix[i][j] = matrix[i][matrix.length - 1 - j]
      matrix[i][matrix.length - 1 - j] = Temp
    }
  }
  return matrix;
};

```

參考資料：
[Rotate Image - LeetCode 48 - JavaScript](https://www.youtube.com/watch?v=swlO6KKh8yk)
