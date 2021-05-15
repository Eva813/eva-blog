---
title: 認識什麼是LocalStorage
date: 2021-01-10 10:36:26
tags: ["LocalStorage"]
---
## 認識 Web Storage：

HTML5 的 Web Storage 是一種可讓網頁將資料儲存於本地端的技術，其作用如同 cookie。

Cookie 儲存空間很小，最多僅能儲存4KB的資料。
HTML5 Web Storage 的儲存空間則大得多(儲存資料容量至少5MB以上)。

![](https://i.imgur.com/tq8wugy.png)


## localStorage 與  sessionStorage

Web Storage 提供兩個物件可以將資料存在 client 端，一種是 localStorage，另一種是 sessionStorage



* localStorage:可以跨瀏覽器分頁（tab）存取，且資料存取沒有期限，除非手動從本地端清除。
* sessionStorage:生命週期較短，每次分頁或瀏覽器關掉後就會清除。

基本操作：
- 設置資料：`localStorage.setItem(key, value)`
- 取得資料：``let storageValue = localStorage.getItem(key, value)``
- 清除資料：``localStorage.removeItem(key)``
- 清除全部資料：``localStorage.clear()``
> 只要將localStorage替換sessionStorage即可

{%note warning%}
儲存在本地端的資料，格式為 key : value，需注意的是「value 的型態只有 String」!
{%endnote%}

## 找到儲存的資料

* 在 DevTools console 

![](https://i.imgur.com/jZNIas5.png)

![](https://i.imgur.com/7SkvJ8q.png)

![](https://i.imgur.com/ZoLQ4yV.png)




## JSON 字串轉換

```javascript=
var arr = ['1','2','c'];

console.log( arr + " is " + typeof(arr));
// 1,2,c is object
```

#### 1.轉換成字串 JSON.stringify


```javascript=
var arrToStr = JSON.stringify(arr);
```

![](https://i.imgur.com/0lfiqLV.png)


#### 2.轉換回原本格式 JSON.parse

* 會把一個JSON字串轉換成 JavaScript的數值或是物件

```javascript
var parse = JSON.parse(arrToStr);
```
![](https://i.imgur.com/3chwLJF.png)



參考資料：
[[JavaScript] localStorage 的使用](https://medium.com/%E9%BA%A5%E5%85%8B%E7%9A%84%E5%8D%8A%E8%B7%AF%E5%87%BA%E5%AE%B6%E7%AD%86%E8%A8%98/javascript-localstorage-%E7%9A%84%E4%BD%BF%E7%94%A8-e0da6f402453)
[[第七週] 瀏覽器資料儲存 - Cookie、LocalStorage、SessionStorage](https://yakimhsu.com/project/project_w7_storage.html)
[[Day16] JavaScript - 前端資料存](https://ithelp.ithome.com.tw/articles/10223253)
[HTML5 的 Web Storage- local storage 與 session storage](https://tools.wingzero.tw/article/sn/28)
[Day20 localStorage、sessionStorage](https://ithelp.ithome.com.tw/articles/10203525)
[[JavaScript] JSON.stringify() and JSON.parse()：變 JSON 和變物件](https://medium.com/itsems-frontend/javascript-json-stringify-and-json-parse-7a1251d3824c)
