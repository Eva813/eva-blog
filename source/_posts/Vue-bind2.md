---
title: Vue.js 3 - 綁定方式多變化
date: 2021-05-16 16:17:30
tags: ["Vue"]
---

## 動態屬性綁定(注意大小寫)
* 宣告變數dynamic，將 dynamic 綁定於點擊事件上 `:click="dynamic = dynamic === 'disabled' ? 'readonly':'disabled'"`
  * 如果變數結果是 disable 的話就會切換成 readonly，反之，就會切換為 disabled
* 接著可以將值動態地加入 HTML 屬性上 `:[dynamic]`
  * 綁定後，就會發現輸入框隨著切換，有不同效果
```html
<h3>動態屬性綁定(注意大小寫)</h3>
  <button type="button"
  v-on:click="dynamic = dynamic === 'disabled' ? 'readonly':'disabled'">切換為 {{ dynamic }}</button>
  <br>
  <input type="text" :[dynamic] :value="name">
```
```javascript
//JS

Vue.createApp({

  data(){
     return {
      dynamic: 'disabled',
      breakfastShop: {
      name: '奇蹟早餐', 
        imgUrl: 'https://images.unsplash.com/photo-1600182610361-4b4d664e07b9?ixid=MXwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHw%3D&ixlib=rb-1.2.1&auto=format&fit=crop&w=200&q=80',
        resizeImg: 'https://images.unsplash.com/photo-1600182610361-4b4d664e07b9?ixid=MXwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHw%3D&ixlib=rb-1.2.1&auto=format&fit=crop&q=80'
      },
    }
  }
}).mount('#app')
```

`readony`:可以選擇輸入框，但無法輸入值
`disabled`:無法點選輸入框
![](https://i.imgur.com/GfAhQJk.png)

![](https://i.imgur.com/YUf3XZk.png)
```
再次補充說明：
    1. dynamic = 【這邊是最前面的變數，用來存放後面回傳回來的值】
    2. dynamic === 'disabled' ? 【這邊就是中間判斷式的部分，判斷 dynamic 變數是否為 'disabled'】
    3. 'readonly':'disabled' 【這邊就是最後面的值，會依照前方的判斷式來決定回傳 'readonly' 還是 'disabled'，如果判斷式結果為 True 則回傳 'readonly' ，反之回傳 'disabled'】
```


## HTML 樣式綁定
樣式綁定是時常使用到的效果，能透過此方式，使顏色改變、增加一點簡易動畫的效果。

## 範例解說
* 替 box 增加旋轉效果
* 綁定class可以用陣列方式、或物件方式來添加，以下範例為物件方式
  * 物件key值對應 className，物件的值是對應 true\false(判斷式)
  * 若className 有 `-` ， 注意要 "bg-danger"
* 事件的綁定 `change()` 透過函式來切換 true\false` this[key] = !this[key];`
* 切換是否旋轉、切換背景色 
```html
<style>
.box {
  background-color: var(--bs-light);
  border: 1px solid var(--bs-gray);
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

 <h2>切換 Class</h2>
          <h3>物件寫法</h3>
          <!-- 物件key值對應 className，物件的值是對應 true\false -->
          <div class="box" :class="{ rotate: isTransform ,'bg-danger':boxColor}"></div>
          <hr>
          <button class="btn btn-outline-primary" v-on:click="change('isTransform')">選轉物件</button>
          <button class="btn btn-outline-primary ms-1" v-on:click="change('boxColor')">切換色彩</button>

```
```javascript
const App = {
  data() {
    return {
      isTransform: true,
      boxColor: false,

     
    };
  },
  methods: {
    change: function (key) {
      this[key] = !this[key];
    },
    addClass(arr) {
      this.arrayClass.push(...arr);
    }
  },
};

Vue.createApp(App).mount('#app');
```
![](https://i.imgur.com/KFqN9hz.png)

![](https://i.imgur.com/b9OCub3.gif)

### 整合為一個物件

將上個範例的兩個class樣式，整合成一個物件

```html
 <hr class="mt-4">
   <h3>物件寫法 2</h5>     
    <div class="box" :class='classObj'></div>

```
```javascript
const App = {
  data() {
    return {
      isTransform: true,
      boxColor: false,

      classObj:{
        rotata: true,
        "bg-danger": true,
      }
     
    };
  },
  methods: {
    change: function (key) {
      this[key] = !this[key];
    },
    addClass(arr) {
      this.arrayClass.push(...arr);
    }
  },
};

Vue.createApp(App).mount('#app');
```


![](https://i.imgur.com/41AKFc8.png)

![](https://i.imgur.com/StALtsi.png)

## 綁定樣式，陣列寫法
* 針對單一元素，加入多個class
  * 此方式就不用 true\false 的判斷動作，只要綁定在陣列之中的樣式，就會套入效果
  * 像是範例，就是使按鈕背景色彩為紅色('btn-danger')、無法點擊('disabled')
```html
 <h4>陣列寫法</h4>
            <button class="btn" :class="['disabled','btn-danger']">請操作本元件</button>
            <button type="button" class="btn btn-outline-primary" v-on:click="addClass(['btn-primary', 'active'])">為陣列加入
              Class</button>
```
![](https://i.imgur.com/RZFOrdK.png)


* 另一種，製作陣列，並將陣列放入
    * 將arrayClass，綁定在標籤的class上
    * 在點擊按鈕，綁定點擊事件，`addClass(arr)`，在該函式傳入參數，將陣列內容加入arrayClass，並套入綁定的標籤上
    * 相對應的方法：
    ![](https://i.imgur.com/VFfDXPY.png)

```html
<h4>陣列寫法</h4>
  <button class="btn" :class="arrayClass"  >請操作本元件</button>
  <button type="button"
    class="btn btn-outline-primary"
    v-on:click="addClass(['btn-primary', 'active'])">為陣列加入 Class</button>
```
```javascript
const App = {
  data() {
    return {
       // Array 操作
      arrayClass: [''],
    };
  },
  methods: {
   
    addClass(arr) {
      this.arrayClass.push(...arr);
    }
  },
};

Vue.createApp(App).mount('#app');
```


![](https://i.imgur.com/PZd0sKO.png)
* `btn-primary`:背景色為綠色
![](https://i.imgur.com/S0zLJKb.png)


#### 補充：混合寫法
* 將bootstrap的class放入，且加入arrayClass
  * `:class="[arrayClass,'text-danger']`，也就是除了放入 arrayClass 這個變數之外，我們還可以放入新的class給予不同樣式
```html
<h4>陣列寫法</h4>
    <button class="btn" :class="[arrayClass,'text-danger']">請操作本元件</button>
      <button type="button" class="btn btn-outline-primary" v-on:click="addClass(['btn-primary', 'active'])">為陣列加入
              Class</button>
```

![](https://i.imgur.com/d3dqSTj.png)




## 行內樣式style

#### 要綁定style的時候
* key會帶入style的屬性(注意要以駝峰式)，如background-color，要改為backgroundColor
* 值則是帶入style相對應的值

```html
<h2>行內樣式</h2>
            <h4>綁定行內樣式</h4>
            <div class="box" :style="{backgroundColor:'red'}"></div>
            <div class="box"></div>
            <div class="box"></div>
```

### 加入準備好的資料格式

* 將設定好的物件，裡面有包含數個樣式，直接綁定至style
![](https://i.imgur.com/z93Jmam.png)

* 背景色彩，紅色，邊框5px
![](https://i.imgur.com/lWYdxIi.png)


### 同時多個樣式

* 以陣列裡面包多個物件
```html
<div class="box" :style="[styleObject,styleObject2]"></div>
```
![](https://i.imgur.com/11h8F7J.png)

![](https://i.imgur.com/62sDl0J.png)

