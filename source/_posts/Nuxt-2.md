---
title: 講講幾個 Nuxt.js 重要觀念
date: 2021-11-10 09:58:39
tags: ["Nuxt"]
catagories: Nuxt
---

![](https://i.imgur.com/4LE0BUW.png)

在使用 Nuxt.js 開啟專案後，會自動生成不少文件檔案，今天就來介紹幾樣基礎設定以及檔案的運用吧！

## 了解 Nuxt 路徑引用規則
* ``~ ``以及`` @`` ：根目錄 
  * (注意：nuxt.conf.js、非 nuxt 結構的 js 檔 除外)
* 波浪或小老鼠 :從根目錄 搜尋assets 資料夾
![](https://i.imgur.com/CNUlrGz.png)
以下也一樣
`import test from "@/assets/js/test.js"`
2. ``./ ``：自己的目錄
* nuxt.conf.js、以及非 nuxt 結構要這樣撰寫
![](https://i.imgur.com/2qLJ5te.png)
3. 放在 static 資料夾裡的引用方式(直接斜線)`` <img src="/demo.png" alt="">``
4. 補充：
[2.4 nuxt 圖片處理 - webpack 相關設定](https://docs.google.com/presentation/d/15q4Vsl3I6Bf5F1f2LdNj_OvF5fcrl7aPv5aA1E39Kjw/edit#slide=id.g813fdce503_0_0)
* 在asset 加入 img 資料夾(將圖片放在這裡)
`<img src="@/assets/img/demo.png" alt="">
`
![](https://i.imgur.com/iSsni0c.png)

* 放在css作背景圖
    *``` background: url(~assets/img/demo.png);  //nuxt css 引入圖片不能加斜線```
    * 也不能將波浪改為@
:::info
原本波浪斜線方式:
``background: url(~/assets/img/demo.png); ``
:::
![](https://i.imgur.com/ekDF3vK.png)


## nuxt.config.js 設定
*  vue cli3 設定vue 專案是透過是 main.js/ vue.config.js 這兩隻檔案。
    *  main.js 為程式的進入點
* nuxt 則統一用 nuxt.conf.js 做為設定。
  * 要編輯這份文件達到專案需求
  * 如 wedpack 是放在 build

![](https://i.imgur.com/aLpoUcR.png)

* 此外在此檔案可以做標題、引入 CDN 等基礎設置
  * head,meta 可以做表頭的設定
  * script 可以引入 JS 的檔案或 CDN 資源
* css 全域設定，也同樣放在這裡
  * [The css property](https://nuxtjs.org/docs/configuration-glossary/configuration-css/)
```javascript
  css: [
   '~/assets/scss/demo.scss'
  ],

```

```javascript
export default {
  // Global page headers: https://go.nuxtjs.dev/config-head
  head: {
    title: 'myNuxt',
    htmlAttrs: {
      lang: 'en'
    },
    meta: [
      { charset: 'utf-8' },
      { name: 'viewport', content: 'width=device-width, initial-scale=1' },
      { hid: 'description', name: 'description', content: '' },
      { name: 'format-detection', content: '網站前後端、網頁設計、程式語言網站' }
    ],
    link: [
      { rel: 'icon', type: 'image/x-icon', href: '/favicon.ico' }
    ],
    script:
    [
      { src: "/jquery.min.js"},
      { src: "https://cdnjs.cloudflare.com/ajax/libs/font-awesome/5.11.2/js/all.js" }
    ]

  },
```
![](https://i.imgur.com/krqrhq8.png)

### 引用 static 靜態資源的檔案
* static資料夾:靜態資源資料夾
    * 其中要連接該檔案路徑要用斜線
    * 這裡面的檔案不會被打包和優化
![](https://i.imgur.com/46ebZ9F.png)


## 套件安裝!
1. 最簡單方式是使用打包後的 CDN 或 js 檔案 
    * 在head 的 script 放入 CDN
2. 使用 [nuxt plugins](https://nuxtjs.org/docs/directory-structure/plugins/) 自包 js 套件 或 vue 套件 → 較麻煩
3. 使用 [nuxt modules](https://zh.nuxtjs.org/guide/modules/) 來引入套件
  * 套件較少
* nuxt module 主要是將 plugin 在封裝一層出來。(就是 nuxt 專有的套件)，如果對安裝 plugins 沒把握，就找有 nuxt modules 的套件來安裝。

### 練習1包裝一般的 vue 套件
 [vuejs-datepicker](https://www.npmjs.com/package/vuejs-datepicker)

1、npm install vuejs-datepicker
* 若直接依照官網來安裝，並在檔案引入會發生錯誤
* 所以 install 完之後要再執行下面動作
2、plugins 新增一個檔案：datepicker.js → 撰寫套件
* 在 plugins 資料夾，新增檔案
```javascript
import Vue from 'vue'
import Datepicker from 'vuejs-datepicker';

Vue.component('Datepicker', Datepicker)

```

![](https://i.imgur.com/l0kYSwT.png)

3、nuxt.config.js 安裝 plugin → 可以設定該套件只在 客戶端 運作
* 可以將插件安裝在客戶端或是 server 端
```javascript
{ src: '~/plugins/datepicker.js', mode: 'client' },
```
* 要使用時，再放入頁面
![](https://i.imgur.com/gNNLJ2O.png)
### 練習2 安裝一般的 js 套件
[GSAP](https://greensock.com/docs/v3/Installation)
1. npm install gsap
2. plugins 新增一個檔案：gsap.js → 撰寫套件
```javascript
import Vue from 'vue'
import gsap from "gsap";

Vue.prototype.$gsap = gsap

```
![](https://i.imgur.com/t3G7UGN.png)
3. nuxt.config.js 安裝 plugin
```javascript
 plugins: [
    { src: '~/plugins/gsap.js', mode: 'client' },
  ],

```
* `create()` 會在前端執行\後端執行

### 練習3 nuxt 安裝 
[axios modules](https://axios.nuxtjs.org/)
1. npm install @nuxtjs/axios
2. plugins 新增一個檔案：axios.js 
* 如同前面的練習，到  nuxt.config.js
 ```javascript
  modules: [
    '@nuxtjs/axios',
  ],

 ```
