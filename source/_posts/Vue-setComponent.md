---
title: Vue.js 3 -  組件的拆分
date: 2021-10-19 16:36:10
tags: ["Vue"] 
---

試想今天有一個網頁，上方的導覽列有不同的分類，帶領你到該項目的分頁中
那我們該如何實現，將各個項目的內容一一呈現
![](https://i.imgur.com/7LUqd5I.png)


## 簡單分別製作 component

首先：component的命名要用大寫
![](https://i.imgur.com/fAOtmM8.png)
* 在 component 中
![](https://i.imgur.com/dgWnvii.png)

### Header
* 在 App.vue
讓vue知道有個component可以被使用，透過ES6的解構放入
![](https://i.imgur.com/4yAMVNw.png)
將引入的component放到template
![](https://i.imgur.com/PO4LhRv.png)
![](https://i.imgur.com/K0UmvdI.png)

## 將其他組件引入

![](https://i.imgur.com/EgWUGfG.png)

![](https://i.imgur.com/qIvkq2l.png)

## 補充說明：
```
修改路徑:
用~@來指定圖片路徑：@在vuecli是代表特殊意義，就會直接從src這個資料夾底下開始查找;而波浪符號，代表現在在css之中使用這個功能
![](https://i.imgur.com/NefWOd8.png)
* 也就是在專案中，即便路徑複雜，只要在路徑最前面加上`@`就會直接進去src
![](https://i.imgur.com/sPhH1rs.png)

```
