---
title: HTML基本介紹-2
date: 2020-11-18 14:12:02
tags: ["HTML"]
---
 此篇文章主要再介紹HTML的元素、屬性，以及簡介在前端開發中常使用的css和javascript是如何和HTML搭配使用。
## HTML 元素 (Element)
```html
 <p>This is paragraph.</p>
```

* 完整的標籤會有開始、結尾：`<p>`,`</p>`:分別為opening tag以及closing tag
* 標籤中間包圍的This is paragraph.是內容
* 起始標籤 + 內容 + 結束標籤:表示一個 HTML元素。

## HTML 標籤屬性 (Attribute)
* `<p class="myP">This is paragraph.</p>`
* 有些Attribute是HTML內建，如：`<a href="./first.html">`
* 有些是為了用於css或是javascript的部分所加上去。如：class與id的使用。
  *   補充： `class`與`id`
    是在於 ID 選擇器在一個 HTML 文件中只能被使用一次，而 Class 選擇器在一個 HTML 文件中可以被使用多次;ID 選擇器可以被 Javascript 中的 GetElementByID 函數所運用，而 Class 選擇器無法被 Javascript 運用到。

## css, javascript與HTML 搭配
### 1.css如何跟 HTML 搭配使用
#### (1).inline CSS
* 在html中加入style屬性，來撰寫CSS
```html
<h1 style="color:blue;">A Blue Heading</h1>
<p style="color:red;">A red paragraph.</p>
```
ps:此方式不建議使用，會造成維護的困難

#### (2).internal CSS
在html檔案中，建立`<style> </style>`區塊
```html
<!DOCTYPE html>
<html>
<head>
<style>
body {background-color: powderblue;}
h1   {color: blue;}
p    {color: red;}
</style>
</head>
<body>

<h1>This is a heading</h1>
<p>This is a paragraph.</p>

</body>
</html>
```
#### (3).external CSS
##### 用link的方式，連結到另一個css檔案

```html
<!DOCTYPE html>
<html>
<head>
  <link rel="stylesheet" href="styles.css">
  /*This example links to a
  style sheet located in the same folder as the current page*/
</head>
<body>

<h1>This is a heading</h1>
<p>This is a paragraph.</p>

</body>
</html>
```

**`<link rel="value">`,"stylesheet"文檔的外部樣式表**


### 2.HTML 中使用 javascript

#### 在html的body尾端
```htmlembedded=
<script>
document.getElementById("demo").innerHTML = "Hello JavaScript!";
</script>
```

#### 創立.js檔案，並引入

* **放在`<head>` 前面**


```javascript
//HTML4 與 （x）HTML
<script type="javascript.js"> </script>

//HTML5
<script src="javascript.js"> </script>

```
---

參考資料:
[W3school](https://www.w3schools.com/html/html_css.asp)
[MDN](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/link)
