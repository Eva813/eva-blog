---
title: 為何要學習 Nuxt.js ？
date: 2021-11-10 09:30:01
tags: ["Nuxt"]
---

![](https://i.imgur.com/4LE0BUW.png)

製作網站，都會希望自己的網站可以被搜尋引擎找得到，並且可以在使用者輸入關鍵字後，可以在頭幾個項目中就能關注到自己的網站！
而在使用 Vue.js 框架，因為是使用 SPA 架構無法做到 SEO (search engine optimization) 的效果，別人無法輕易的找到我們所製作的網站。
今天要介紹的 Nuxt.js 是一個基於 Vue.js、用來處理伺服器渲染（Server-side rendering(SSR)）的框架。透過 SSR 我們可以做到更好的 SEO 效果。

本文首要任務： 認識 SPA 與 SSR 的差異


## SPA (Single Page Application) 架構
* 讓一些後端的工作，分擔到前端 (如： router 設定)
* 流程為：使用者開了瀏覽器 -> 向後端server要資料，因為改為SPA 架構，所以提供的會是SPA 首頁（會載入JS\編譯好的CSS\一頁空殼的HTML網頁），此空殼經過 Vue 編譯產出 DOM 物件，讓瀏覽器印出。
* 讓回應速度更快，使用者在轉換頁面時得到更好的體驗。
  * SPA（Single Page Application）意思是僅有一個頁面的應用程式，也就是說網頁不需跳轉頁面就可以達到基本的建立、讀取、修改、刪除資料功能。
* 後端負責調整API 邏輯\架構
### SPA 缺點
* SEO差(SPA 一開始吐的是空殼)，GOOGLE 爬不到頁面資訊
* SMO差，FB爬不到頁面資訊
* 首屏速度慢，一開始畫面會空白或閃爍
    * 發出請求資訊到實際上呈現完整頁面時間

## SSR (Server Side Rendering)
流程為：使用者開了瀏覽器 -> 向後端server要資料，因為改為SSR，所以會提供一整個頁面(不會是畫面空白閃爍)
* 希望頁面可以將網頁整個東西呈現，並讓搜尋引擎查得到
* 處理 SEO(針對搜尋引擎)
- title
- meta tag
  - 全域 ``<head>`` 設定
  - 單頁 ``<head>`` 設定 
- description
* 處理 SMO (針對 Social media)
  * twitter\youtube\Line...
### 有更多的彈性設定
#### Nuxt 有三種模式，可以幫你解決問題
1. Universal: SSR + CSR 
* 全域設定 mode: 'Universal'
2. SPA: 不跑SSR 
* 全域設定 mode: 'spa' 或執行 nuxt-spa
3. Static Generated (Pre Rendering): 產生靜態頁面
* nuxt run generate
    * 預先將頁面轉成靜態頁面
    * 如要拉10樣商品，就先拉10個頁面

希望本篇的扼要介紹能夠讓讀者快速知道其中的差異。

參考資料：
[SSR — Nuxt.js 超入門](https://medium.com/@jackercleaninglab/ssr-nuxt-js-%E8%B6%85%E5%85%A5%E9%96%80-84a0823b45ed)
[Vue Nuxt 介紹與實作範例](https://www.youtube.com/watch?v=szrRDJBFmvQ)
