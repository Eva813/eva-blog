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


## 事件與class的綁定
* 於 box 綁定旋轉的效果 `:class="{ rotate: isTransform }`
* 在button綁定觸發旋轉的事件
```html
<style>
  .box {
   margin:0 auto;
  background-color: #fff;
  border: 1px solid #ccc;
  width: 80px;
  height: 80px;
}
.box {
  transition: all .5s;
}
.box.rotate {
  transform: rotate(45deg)
}
</style>
 <div id="app">
   <h3>觸發事件 與 縮寫*</h3>
  <div class="box" :class="{ rotate: isTransform }"></div>
  <hr>
  <button class="btn btn-outline-primary" @click="changeClass" >選轉物件</button>
  </div>
```
```javascript
Vue.createApp({

 data() {
    return {
      
       isTransform: true,
    };
  },
  methods: {
     changeClass() {
      this.isTransform =!this.isTransform;
    },
  }
};
  
}).mount('#app')
```


## 帶入參數
* 建立 `change()` 函式以帶入參數方式，來變化 isTransform 的 true\false
```html
<h3>帶入參數*</h3>
  <div class="box" :class="{ rotate: isTransform }"></div>
  <button class="btn btn-outline-primary" @click="change('isTransform')">選轉物件</button>
```
```javascript
Vue.createApp({

 data() {
    return {
      
       isTransform: true,
    };
  },
  methods: {
     changeClass() {
      this.isTransform =!this.isTransform;
    },
    change(key) {
      this[key] = !this[key];
    },
  }
};
  
}).mount('#app')
```
## 動態物件方法 {}
* 在一個元素上，加入多個事件
* 注意：此方式無法傳入參數
```html
  <h3>動態物件方法 {}</h3>
          <!-- 此方法無法傳入參數 -->
  <button class="box" @="{
      mousedown:down,
      mouseup:up
   }">
  </button>
```
```javascript
Vue.createApp({

 data() {
    return {
      
       isTransform: true,
    };
  },
  methods: {
     changeClass() {
      this.isTransform =!this.isTransform;
    },
    change(key) {
      this[key] = !this[key];
    },
    down() {
      console.log("按下");
    },
    up() {
      console.log("放開");
    }
  }
};
  
}).mount('#app')
```

![](https://i.imgur.com/qO3AcxL.gif)


[codepen](https://codepen.io/Eva-go/pen/abwYwPa)
