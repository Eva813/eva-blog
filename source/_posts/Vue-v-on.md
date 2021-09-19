---
title: Vue.js 3 - 事件綁定
date: 2021-05-16 16:16:30
tags: ["Vue.js"]
---

* 建立add、minus兩個按鈕
    * 點選add增加數字、點選minus減少數字
* 讓add按鈕綁定`addFn`函式：`v-on:click="addFn"` `on` `click`事件，執行`addFn`函式
* 綁定後，在` methods`撰寫`addFn`函式內容，要取得 data 中的 num 才能進行運算 =>`this.num++`
* 同理，在minus的按鈕，函式內運算`this.num++`，並將點擊事件放入`button`
    * v-on:Event 事件綁定，有兩種撰寫方式
![](https://i.imgur.com/IUUcznh.png)

```html
 <div id="app">
   <h1>{{num}}</h1>

    <button v-on:click="addFn">Add</button>
    <!-- v-on 改為@ -->
    <button @click="MinusFun">Minus</button>

  </div>
```

```javascript
Vue.createApp({

  data(){
     return {
       num: 0
    }
  },
  methods: {
    addFn() {
       //num.value = num.value + 1;
          this.num ++;
    },
     MinusFun(){
        this.num--;
     },
  }
}).mount('#app')
```

[codepen](https://codepen.io/Eva-go/pen/bGRLXNW)
