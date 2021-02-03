---
title: HTML基本介紹-1
date: 2021-02-03 20:51:30
tags: HTML
---
* 標記式語言，也就是透過標籤來建立
* 目前的規範版本為 HTML 5
# 建立第一個網頁

`html:5`,`!` 建立HTML雛形
* 輸入標題

 ![](https://i.imgur.com/rNdXzJ4.png)

* 將建立的html放到瀏覽器

![](https://i.imgur.com/rrnJ0Kf.png)

# HTML的文件架構

<!DOCTYPE html>

> Luka 程式柴教學
![](https://i.imgur.com/eQmD5Qk.png)

* 字元集(charset)：UTF-8,Big5
>![](https://i.imgur.com/BP4V514.png)

>![](https://i.imgur.com/W9rljFi.png)

# HTML的元素
## 1.`h1`

![](https://i.imgur.com/0fymVOx.png)



`<head> </head>`網頁中的頭，它的任務是要容納文件的 metadata
 
`<h1>Heading 1 </h1>`網頁中的標題 headings


## 2.`<p>`

一個段落是一個block元素，段落與段落間有距離
例如：
html有block(`<p>`,`<div>`),inline(`<span>`)元素
`<br>`換行，可與文內進行段落的區分

如圖：
![](https://i.imgur.com/QNi6OuM.png)

## 3.圖片 `<img>`
`<img src="pic_trulli.jpg" alt="Italian Trulli">`

`src=`圖片的網址
`alt` 圖片代表的意思，特別對SEO優化有幫助，且在圖片無法顯示時，能呈  現基本資訊


![](https://i.imgur.com/NAkLq9P.png)


## 4.連結標籤 `<a>`
`<a href="https://www.w3schools.com/">Visit W3Schools.com!</a>`

* `href=` 連結要去的網址位置
* `<a href="/html/default.asp">HTML tutorial</a>`
  -> 也可以連結同一個檔案、目錄下的資料
* `<a href="https://www.w3schools.com/" target="_blank">Visit W3Schools!</a>`
* `Target="_blank"`開啟一個新的視窗，不會蓋掉前面已開啟的舊視窗，但要注意的是當開了新的視窗後，可能會影響原本視窗的運作
建議你要加上 rel="noreferrer noopener"


![](https://i.imgur.com/1sTR3Fv.png)

## 5.表格 `<table>`
使用的標籤有`table`,`th`,`td`，要使用時再去複製[語法](https://www.w3schools.com/html/html_tables.asp)修改比較快～

簡易示意圖，清楚標籤所對應的表格位置：
![](https://i.imgur.com/o8WwMSE.png)

## 6.`<form>` 如：登入,會員註冊


1) `action` 定義數據的發送位置
範例：沒有action 屬性，數據將發送到和表單所在的同一頁面`<form></form>`
範例：數據將發送到指定的URL位置(也就是web server位置)`<form action="http://foo.com"></form>`



2) `<input> </input>`表單輸入的設定
其中：
`name` 屬性用來指定送出去的該筆資料要用什麼名稱
`type` 建立表單元件，"text"文字,"password"密碼輸入,"checkbox"核取方塊,"submit" 表單的送出按鈕
`placeholder`  輸入的提示訊息
`value` 指定初始值 (default value)


![](https://i.imgur.com/Zr7IFsA.png)






---
參考資料
[W3SCHOOL](https://www.w3schools.com/html/default.asp)
[Target="_blank"的安全性風險](https://pjchender.blogspot.com/2020/05/relnoreferrer-targetblank.html)
[你不曾察覺的隱患：危險的 target="_blank" 與 “opener”](https://www.itread01.com/iieq.html)
[ JS - `<form>` 表單](https://ithelp.ithome.com.tw/articles/10206661)
[HTML 表單元件 - `<input>` 標籤 (tag)
](https://www.fooish.com/html/input-tag.html)
