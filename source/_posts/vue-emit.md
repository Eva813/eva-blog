---
title: Vue.js 3 -  資料傳遞 emit
date: 2021-09-21 12:42:48
tags: ["Vue"] 
---
圖示：
![](https://i.imgur.com/PxIIobu.png)


## emit 實作練習

![](https://i.imgur.com/7guYdVb.png)


* 透過點擊add按鈕，觸發外層元件數值的改變
    * 先定義外層接收資料方式
    * 定義內層的 $emit 觸發方法
    * 使用 v-on 的方式觸發外層方法（口訣：前內、後外）

### 外層元件
* 定義接收方法：當內層傳給你的時候，要做什麼事
    * `addNumber()`使num 增加1
```javascript
const app = Vue.createApp({
            data() {
              return {
                num: 0,
                text: ''
              };
            },
            methods: {
              addNumber() {
                console.log('addnumber');
                this.num++;
              },

            }
          });
```

### 內層元件

* 在方法內建立函式，來觸發資料傳遞
* 並將`click`綁到內部元件的按鈕上。使點擊時候，會觸發$emit
    * emit 名稱`emit-num`
```javascript
app.component('button-counter', {
            methods: {
              click() {
                console.log('inner,click');
                this.$emit('emit-num');
              }
            },
            template: `<button type="button" @click="click">add</button>`
          });
```

![](https://i.imgur.com/W1s9mhp.png)

## 建立內外層元件的溝通橋樑
* 使用 v-on 的方式觸發外層方法（口訣：前內、後外）
    * 在 div內放入子元件`button-counter`
    * `:emit-num="addNumber"`

```javascript
<div id="app">
          {{ num }}
    <button-counter v-on:emit-num="addNumber"></button-counter>
     </div>
```

## 練習二，資料接收

### 外層元件
* 接收方法為 `getData` 接收 text
```javascript
const app = Vue.createApp({
            data() {
              return {
                text: ''
              };
            },
            methods: {
           
              getData(value) {
                console.log('getData', text);
                this.text = value;
              }
            }
          });
```

### 內層元件
* 建立觸發傳遞的方式
    * 將內層元件`text: '內部資料'` 傳遞到外層
```javascript
app.component('button-text', {
    data() {
        return {
        text: '內部資料',
         }
     },
    methods: {
      emitText() {
      this.emit('emit-text', this.text)
         }
    },
     template: `<button type="button" @click="emitText">emit data</button>`
 });
```
### 建立內外橋樑
```javascript
<h3>傳遞資料狀態</h3>
 內部傳來的文字：{{ text }}<br>
 <button-text @emit-text="getData"></button-text>
```
![](https://i.imgur.com/avhMirS.png)

## emit 命名
與 props 命名一樣需要注意：
* 在內層若以駝峰命名 `emitText`
    * 也可以一開始內層就是用`-` 來命名即可，如`emit-text`
```javascript
emitText() {
       this.emit('emitText', this.text)
    }
```
* 到了要綁定的 template 上，`emit-text`
```javascript
<button-text @emit-text="getData"></button-text>
```
