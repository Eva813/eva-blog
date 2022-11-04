---
title: 認識 Node.js
date: 2021-06-11 14:28:30
tags:
catagories: Node.js
---
## 前言
  之前在安裝套件時，就有先安裝過 Node.js 但從沒有特別去注意，它是應用在哪一塊、可以做什麼？就找了之前 udemy 全端課程，特意拉此部分來認識一下。

Node.js，讓我們可以將js檔案拿出瀏覽器，並直接和電腦硬件互動
* js: 進行網頁中的實作、函式、行為的建立。動畫的執行、下拉選單
* Node.js: 允許我們用JS 直接與電腦的硬體互動。例如建立桌面應用程式
* 此外，還能在別人的電腦或server用Node.js來用行JS。例如：使用者登入 google 並輸入詢問，而你的瀏覽器會發出請求給google servers，而在這些servers 可以真的執行JS程式碼來執行發出的請求

![](https://i.imgur.com/49D3LbW.png)

## 如何使用Node
1. 首先，建立一個資料夾 `intro-to-node`，在資料夾內建立檔案`index.js`
2. 在該資料夾位置，運行``node index.js``

![](https://i.imgur.com/fsgHKd8.png)
* 印出檔案內的console
![](https://i.imgur.com/bptXcfu.png)
![](https://i.imgur.com/QQ1BXXI.png)


## node REPL
* Read Evaluation Print Loop
* 要進入：就是在終端機下node
![](https://i.imgur.com/gomqsjQ.png)
* 和google 的console有點類似
![](https://i.imgur.com/hOMWtn5.png)
* 取得些提示 con+ tab 會列出相關的可能性
![](https://i.imgur.com/ijg4eJg.png)
![](https://i.imgur.com/36ZOTYu.png)
### 離開
* `.exit`
* control+c 兩次
* `clear` 可以清除終端機的資料
## native node module
* 當安裝node，他就會包含許多建立好的模組
### 使用node.js 進入電腦本地端的資料
* nodejs.org/api 可以查到所有native node module，以及使用方式說明的文件
* 當要使用 modual
![](https://i.imgur.com/eIDZjer.png)

![](https://i.imgur.com/DDWBxTn.png)
* 警告 jshint esversion:6
![](https://i.imgur.com/zdRpkQ4.png)

### 實際應用練習：複製檔案
* 在原本的資料夾內建立新的檔案
![](https://i.imgur.com/ndvRf3A.png)
![](https://i.imgur.com/24EYw4k.png)
* 在index.jd 輸入該語法
![](https://i.imgur.com/0oGOWsV.png)
* 輸入後要運行 `node index.js`
![](https://i.imgur.com/hCsZbOF.png)
    * 運行之後會看到 `file2` 建立完成
![](https://i.imgur.com/YR3MLWP.png)
* 若是已經建立好的檔案
* 在檔案內輸入資訊`I am file2`
![](https://i.imgur.com/9ODoKY5.png)
* 執行 `copyFileSync`
![](https://i.imgur.com/Jae1spC.png)

---
## NPM package manager
* 即為 Node Package Manager 的縮寫，他是套件管理工具
* 在安裝node 的同時，就已經裝有 NPM
[NPM是什麼？了解Node Package Manager套件管理機制](https://tw.alphacamp.co/blog/npm-node-package-manager)
* npm init
![](https://i.imgur.com/75YtfQR.png)
* 建立一個 package.json
![](https://i.imgur.com/HdtTW6p.png)
![](https://i.imgur.com/eHkTGKZ.png)

## 如何使用真實npm
![](https://i.imgur.com/OQ0764J.png)
* 在要進行安裝的資料夾內，`npm install superheroes`
![](https://i.imgur.com/05qxzDJ.png)
![](https://i.imgur.com/XOlgK2J.png)
* 參考文件的說明
![](https://i.imgur.com/6xrdtZG.png)
    * 在js檔案中輸入
![](https://i.imgur.com/87emqhz.png)
    * 運行 `node index.js`
![](https://i.imgur.com/F9f6Qzj.png)


[什麼是 Node.js？Node.js 完整介紹懶人包！](https://blog.hiskio.com/what-is-node-js/)
