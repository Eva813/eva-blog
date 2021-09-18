---
title: Vue.js 3 - 起手式
date: 2021-05-16 13:56:28
tags: ["Vue.js"]
---


今天要介紹的是 Vue.js3 的起手式，主要參考[官方文件](https://vuejs.org/v2/guide/#Getting-Started)來學習

### 載入方式
```javascript
<script src="https://unpkg.com/vue@next"></script>
```

### 引入之後，在`index.html`檔案

```html
 <div id="app">
    <h1>{{message}}</h1>
  </div>
```
```javascript
//JS

Vue.createApp({

  data(){
     return {
      message: 'I am Eva!'
    }
  }
}).mount('#app')
```
* 畫面就會出現相應的文字
![](https://i.imgur.com/l7MiTCV.png)
