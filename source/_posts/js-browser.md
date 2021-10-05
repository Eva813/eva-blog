---
title: JS-browser
date: 2020-12-28 18:47:58
tags: ["JS",DOM"]
---

![](https://i.imgur.com/NdO4OR5.png)

## 1. DOM 與樹狀結構
![](https://i.imgur.com/uZDTql8.png)
* DOM: document:網頁內部
> KURO 老師:
> DOM 是一個將 HTML 文件以樹狀的結構來表示的模型，而組合起來的樹狀圖，我們稱之為「DOM Tree」。

> Huli 老師:瀏覽器提供該橋樑，讓我們用js去改面畫面的東西
 ![](https://i.imgur.com/tPHGSL4.png)





### API (Application Programming Interface)
* 應用程式 編程 介面
## document 物件 API 文件
* #### `getElementsByTagName` : 抓取html中某tag的元素
```html 
<!DOCTYPE html>
<html lang="en">
<body>
<div>
    hello~

  </div>
  <div>
    yo!
  </div>
  <script>

    const elements = document.getElementsByTagName('div');
    console.log(elements);
  </script>
  </body>
  </html>
```
![](https://i.imgur.com/hSmfZ9u.png)

![](https://i.imgur.com/yNOOK2m.png)

* #### `getElementsByClassName`: 抓取html中某class元素(有好幾個相同名稱的class，會一並抓取)

```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title></title>
</head>

<body>
  <h1>DOM
  </h1>
  <div class="block1">
    hello~

  </div>
  <div>
    yo!
  </div>
  <script>

    const elements = document.getElementsByClassName('block1');
    console.log(elements);
  </script>
</body>

</html>
```

![](https://i.imgur.com/d7ZlZnr.png)


* #### `getElementById` : 注意：getElement 沒有加s！這是用來抓取指定ID
```html
<body>
  <h1>DOM
  </h1>
  <div class="block1">
    hello~

  </div>
  <div id="myyo">
    yo!
  </div>
  <script>
    const elements = document.getElementById('myyo');
    console.log(elements);
  </script>
</body>

```
![](https://i.imgur.com/1ouq4WA.png)

* #### `querySelector`: 後面接的是css選擇器
* 只會針對元素的第一筆資料，其他並不會被選入這時候可以使用

    * 選擇tag標籤：('div')：注意：使用此方式當html有多個div，他只會選擇到第一個符合的
    * 選擇cass : ('.calssName')
    * 選擇id : ('#idName')
    
![](https://i.imgur.com/QzoOo6r.png)

![](https://i.imgur.com/pSMPFCJ.png)
    
* 也可以選區某一tag下的tag
    * `querySelector('div > a')`
```html
<body>
  <h1>DOM
  </h1>
  <div class="block1">
    hello~

  </div>
  <div id="myyo">
    yo!
    <a href="#">hello</a>
  </div>
  <script>

    const elements = document.querySelector('div > a');
    console.log(elements);
  </script>
</body>
```
![](https://i.imgur.com/I9qr8LD.png)

* #### `querySelectorAll`: 可以選取多個元素

```html
<body>
  <h1>DOM
  </h1>
  <div class="block1">
    hello~

  </div>
  <div id="myyo">
    yo!
    <a href="#">hello</a>
  </div>
  <script>

    const elements = document.querySelectorAll('div');
    console.log(elements);
  </script>
```
![Eloquent JavaScript, 3rd Edition](https://i.imgur.com/jFPYIO0.png)

### 順利選則元素之後，嘗試做些改變

可以看看，js如何與瀏覽器互動 ->[JavaScript 與瀏覽器的溝通](https://hackmd.io/yGiTI8qVRLWmDCpD1w7HcA?view)



###  BOM 與 window 物件
* BOM (Browser Object Model，瀏覽器物件模型)，是瀏覽器所有功能的核心，與網頁的內容無關。
    * window是瀏覽器的根物件，也是BOM的瀏覽器模型
* window.location.hrrf => 可以知道現在網址
![](https://i.imgur.com/8EeLLGQ.png)
* window.history.back(); => 回到網頁瀏覽的上一頁
![](https://i.imgur.com/kBtDslP.png)

![](https://i.imgur.com/jyPOjvH.png)


### 參考資料
[Day03-深入理解網頁架構：DOM](https://ithelp.ithome.com.tw/articles/10202689)
[JS 筆記 - 認識 DOM 文件物件模型](https://hsuchihting.github.io/javascript/20200615/1316819935/)
[MDN-Document](https://developer.mozilla.org/zh-TW/docs/Web/API/Document)
[The HTML DOM Document Object](https://www.w3schools.com/jsref/dom_obj_document.asp)
[JavaScript 基礎知識-querySelectorAll](https://ithelp.ithome.com.tw/articles/10211614)
[The Document Object Model](https://eloquentjavascript.net/14_dom.html)
[JavaScript入門系列：BOM和DOM筆記](https://www.happycoding.today/posts/43)

