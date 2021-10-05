---
title: Vue.js 3 -  多筆資料渲染 v-for
date: 2021-05-17 16:20:22
tags: ["Vue","v-for"] 
---
![](https://i.imgur.com/NBBY8vo.png)


## ` v-for` 呈現多筆陣列
* `v-for= item in peopleArr` ， item 為 自定義名稱，後面(peopleArr)填入要迴圈的資料
  * item是表示 peopleArr 陣列中每個單一物件
* key ：為帶入陣列的 key 值

```html
  <div id="app">
   <h3>v-for 與 key</h3>
  <p> 人員介紹</p>
  <ul>
    <li v-for="(item, key) in peopleArr">
      {{ key }} - {{ item.name}} / {{ item.age }} 歲
    </li>
  </ul>
    
  </div>

```
```javascript
Vue.createApp({

  data(){
     return {
     peopleArr: [
        {
          name: 'Ken',
          age: 30,
          vegan: false
        },
        {
          name: 'Wong',
          page: 35,
          vegan: false
        },
        {
          name: 'Merry',
          age: 60,
          vegan: false
        },
        {
          name: 'Tom',
          age: 30,
          vegan: true
        },
      ],
    }
  },
  
}).mount('#app')
```
[codepen](https://codepen.io/Eva-go/pen/powVYmN)
![](https://i.imgur.com/TebHrmC.png)

## 物件迴圈
* 每個item 就是每個物件的資料
* key值為物件的屬性名稱

```html
  <div id="app">
   <h3>v-for 與 key</h3>
  <p> 人員介紹</p>
  <ul>
    <li v-for="(item, key) in peopleObj">
      {{ key }} - {{ item.name}} / {{ item.age }} 歲
    
    </li>
  </ul>
    
  </div>

```
```javascript
Vue.createApp({

  data(){
     return {
     peopleObj:{
        Salse: {
           name: 'Ken',
          age: 30,
          vegan: false
        },
        Manager: {
          name: 'Wong',
          page: 35,
          vegan: false
        },
        HumanResource: {
          name: 'Merry',
          age: 60,
          vegan: false
        },
        Crew: {
           name: 'Tom',
          age: 30,
          vegan: true
        }
      },
    }
  },
  
}).mount('#app')
```
![](https://i.imgur.com/tzjh6Ah.png)

## v-for 與 key


### v-for 可以使用 index 當作 key 嗎？
以六角課程為範例說明


```html
<h3>v-for 與 key </h3>
  <p>菜單</p>
  <ul>
    <li v-for="(item, key) in products">
      {{ key }} - {{ item.name}} / {{ item.price }} 元
      <input type="text">
    </li>
  </ul>
```
```javascript
Vue.createApp({

  data(){
    return {
     products: [
        {
          name: '蛋餅',
          price: 30,
          vegan: false
        },
        {
          name: '飯糰',
          price: 35,
          vegan: false
        },
        {
          name: '小籠包',
          price: 60,
          vegan: false
        },
        {
          name: '蘿蔔糕',
          price: 30,
          vegan: true
        },
      ],
    },
  method:{
    reverseArray: function () {
      this.products.reverse();
    },
  }
  }
}).mount('#app')
```
![](https://i.imgur.com/GKjdBGd.png)

* 反轉之後，並沒有跟著
![](https://i.imgur.com/ALe8vst.png)

* 綁定key之後
``` html
<li v-for="(item, key) in products" v-bind:key='item.name'>
```

![](https://i.imgur.com/kbtUwy1.png)

### `v-for` 可以使用 index 當作 key 嗎？
索引是依照位置來判定，所以當資料的位置修改，索引值也會跟著變動，導致後續的渲染錯誤。
如果key是會變動的話，可能會導致資料的渲染錯誤

### 如果後端提供的資料，沒有唯一索引要如何處理？
  1.與後端溝通，為何前端要用到key的值
  2.拿到資料後，自己產生key，並放入該物件資料中

```
使用v-for時，一定要用key 
使用v-for時，不要拿索引當作key
請使用唯一的值當作key的值
```
