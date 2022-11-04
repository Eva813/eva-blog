---
title: Vue.js 3 - 屬性綁定
date: 2021-05-16 14:56:28
tags: ["Vue"]
categories: Vue
---
![](https://i.imgur.com/NBBY8vo.png)

  屬性綁定可以輕鬆的將data中的資料，與HTML的標籤屬性綁定，這樣一來可以動態的變更資料。

## 原本 HTML 的樣子

* img中原本有個圖片資料
* v-bind是指令，``：``後接的是HTML的屬性

```html
  <h3>綁定屬性 v-bind</h3>
  <p>{{ breakfastShop.name }}</p>
  <img src="https://images.unsplash.com/photo-1600182610361-4b4d664e07b9?ixid=MXwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHw%3D&ixlib=rb-1.2.1&auto=format&fit=crop&w=200&q=80" class="square-img" alt="">
```
```javascript
//JS

Vue.createApp({

  data(){
     return {
      breakfastShop: {
        name: '奇蹟早餐',
        imgUrl: 'https://images.unsplash.com/photo-1600182610361-4b4d664e07b9?ixid=MXwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHw%3D&ixlib=rb-1.2.1&auto=format&fit=crop&w=200&q=80',
        resizeImg: 'https://images.unsplash.com/photo-1600182610361-4b4d664e07b9?ixid=MXwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHw%3D&ixlib=rb-1.2.1&auto=format&fit=crop&q=80'
      },
    }
  }
}).mount('#app')
```
### 修改 => 綁定JS中 breakfastShop 物件資料內容

![](https://i.imgur.com/H3UzhrB.png)
* 將原本`src` => `v-bind:src="breakfastShop.imgUrl"`
  * 此外title 屬性，也能照樣綁定喔！  
```html
   <h3>綁定屬性 v-bind</h3>
          <p>{{ breakfastShop.name }}</p>
          <img v-bind:src="breakfastShop.imgUrl" class="square-img" v-bind:title="breakfastShop.name" alt="">
```
![](https://i.imgur.com/Xhtez9s.png)

### 縮寫形式
* 留下冒號
```html
 <h3>縮寫形式 <code>:</code></h3>
          <img :src="breakfastShop.imgUrl" class="square-img" :title="breakfastShop.name" alt="">

```

## 其他屬性的綁定

* 當他是飽的狀態，就無法按下送出
    * 預設是false 所以可以送出表單
    * 但當點擊狀態切換(isFull: true)，此時就會無法點擊送出按鈕
* 由範例可得知，可以透過 true\false 的狀態切換，製造出不同互動效果
```html
 <h3>更多屬性綁定</h3>
          小明還想點餐：
          <form action="">
            <input type="text" value="我要吃蘿蔔糕">
            <button type="submit" :disabled="isFull">送出</button>
          </form>

          <button type="button" v-on:click="change('isFull')">狀態切換</button>
```

![](https://i.imgur.com/GVQXr8f.png)


* 預設是false，所以可以點擊送出
![](https://i.imgur.com/4HmkD5f.png)

![](https://i.imgur.com/w91MEBD.png)


資料來源：
六角學院 - Vue.js 3 課程
