---
title: React- 元件 / props
date: 2022-09-04 15:42:36
tags: ["React"]
categories: React
---

![](https://i.imgur.com/InPScmW.png)

之前曾快速的學習了React 資料的建立與傳遞方式，並寫了篇文章，最近因為六角讀書會活動也再次更深入一些的學習 React ，並將一些 React Hook 反覆使用練習。
因為目前工作主要還是以 Vue 進行開發，所以有時候會提一下兩者框架使用的相同或不同之處。 

## component 元件化
在 Vue 的撰寫中，也是會嘗試將頁面中的不同區塊做拆分。與 Vue 一樣，元件的的命名會以 大寫 為開頭。
透過減少重複為用意來建立元件化。

![](https://i.imgur.com/T26krQk.png)
[codepen-Component](https://codepen.io/Eva-go/pen/zYjxvev)

### 多個資料，傳遞給單一元件
* 建立兩個元件 component1 , component2
* 建立共通元件 Board ，此元件可以各別放入兩個元件要傳入的資料(props)

[codepen-共用元件](https://codepen.io/Eva-go/pen/poVjVaB)

### 共同資料，傳遞給不同元件
[codepen-共用資料](https://codepen.io/Eva-go/pen/ExLVRXX?editors=1010)

### 條件 render
* 這部分在官方文件就有清楚介紹，可以依據使用條件來顯示元件資料
* 使用條件判斷時，若不複雜，可以使用三元運算方式
  * `isLoggedIn ? <UserGreeting /> : <GuestGreeting />`
* [conditional-rendering](https://zh-hant.reactjs.org/docs/conditional-rendering.html)

## 元件資料傳遞
### 子傳父
* 範例將以 input 為範例
* 此方式的資料傳遞，有點類似於 Vue 中的 `v-model` 資料雙向綁定
* [使用 v-model 實現父子元件傳遞資料](https://ithelp.ithome.com.tw/articles/10268187)
* 在 React 可以將 useState 中設訂的值綁在元件上作為傳遞
* 在下方的圖示
* 在父層有 value , setValue
-> 其中 setValue 方法提供給子元件，同時就會讓子元件也產生改變 value 的能力（也就是可以使用父元件的方法）
* 要實現子傳父，就在父層掛一個 hook
![](https://i.imgur.com/Qb3dUeX.png)
![](https://i.imgur.com/WbKvlT2.png)
