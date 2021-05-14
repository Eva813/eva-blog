---
title: js基本功- 變數與型別
date: 2020-12-26 18:15:07
tags: ["js","切版"]
---

## 使用var來宣告變數

#### 如果var沒有給予任何值，會呈現undefine
![](https://i.imgur.com/ZV4O7gJ.png)

#### 給予值之後
![](https://i.imgur.com/b916Z2B.png)

{%note info%}
程式語言中 等於符號`=` 不是比較的意思，是->指派值的意思
上圖：1是一個值，它指派給x這個變數
{%endnote%}

### 要比較值的大小
- true,false（布林值）
- 宣告x為1,y=2;利用 `===`來判斷
![](https://i.imgur.com/0jhw1fi.png)

### 型別
* 剛剛把1給x這個變數
* 當查看型別時，如下圖
![](https://i.imgur.com/y0o91bX.png)

---

## 在VS code 來撰寫 JS

* 與剛剛在chrom一樣的方式來輸入x,y
* 利用console.log來印出資料

```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>使用 VS code 來撰寫 JS，講解變數，值與型別</title>
</head>

<body>
  <h1>EX 01</h1>
  <script>
    console.log('hello world')
    var x;
    console.log(x);  // 出現undefined
    var y = 10;
    console.log(y);

  </script>
</body>

</html>

```
![](https://i.imgur.com/pFvGU90.png)

## 型別介紹：

### Number 數值
* 通常程式語言會分成，如：python'java'c...
    * 整數 integer：只需紀錄完整數字
    * 浮點數 float：小數點
* 而javascript 不區分 -> 只有 數值number（具有浮點數能力）
* 以下宣告 `b=1.1`，查看型別發現是`typeof`

![](https://i.imgur.com/AdcRhnM.png)

* 另外以python為例：可以發現有區分顯示整數、浮點數

> 程式柴課程：
> ![](https://i.imgur.com/MWfjXxS.png)

### 浮點數的陷阱
* 因為js運算是依循IEEE754的規範，在運算時會轉換成`二進制`，而浮點數在轉成`二進制`時會造成無窮迴圈，進而產生運算誤差
* 由於js採用64位雙精度浮點數編碼，實際儲存時為了節省空間，採用科學計數法表示

參考資料：
[[JavaScript]浮點數運算出現一堆小數位數
](https://dotblogs.com.tw/WillianHsiaoDotNetBLog/2020/01/15/JavascriptFloatCaculateBug) [JavaScript 浮點數陷阱及解法](https://www.itread01.com/content/1545644704.html)
[js浮點數計算精度問題](https://www.mdeditor.tw/pl/pqGp/zh-tw)
[JavaScript-Number的各種地雷--浮點數運算](https://medium.com/@ad57475747/javascript%E6%B5%AE%E9%BB%9E%E6%95%B8%E9%81%8B%E7%AE%97-1691eefe3ea7)

## String 字串 
> 程式柴課程：
![](https://i.imgur.com/xO6SmsP.png)

* ### 以chrome開發者工具示範
![](https://i.imgur.com/81RfQ39.png)
* #### 單雙混用 
![](https://i.imgur.com/0yIN8rY.png)
* #### ES6使用  (此符號也能用來處理斷行問題)
![](https://i.imgur.com/NSAe3A0.png)
* #### 字串連接 
![](https://i.imgur.com/bqpGAjI.png)
{%note info%}
`clear()`  清除頁面
{%endnote%}

## Boolean 布林值
![](https://i.imgur.com/fcMnCjC.png)
二進位：
0的時候->false
1的時候->true
* ### 搭配if、else使用，以控制或判斷流程
![](https://i.imgur.com/uQ00Bgh.png)

## 「null 空值」與 「undefined 未定義」
* 宣吿了x變數，卻沒有給它值（還沒有指派給它值） ->未定義的變數

![](https://i.imgur.com/wCX0pBt.png)

* 期待它是一個空值，是由使用者指派給b變數（指定給它為空值）

![](https://i.imgur.com/GkIkdYT.png)


---
