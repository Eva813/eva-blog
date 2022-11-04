---
title: Vue.js 3 - v-on 修飾符
date: 2021-09-20 15:06:46
tags: ["Vue"]
categories: Vue
---
![](https://i.imgur.com/NBBY8vo.png)

修飾符有分為：
1.按件修飾符
2.滑鼠修飾符
3.事件修飾符

[codepen](https://codepen.io/Eva-go/pen/RwgMXGN)
## 按鍵修飾符

### 1-1. 別名修飾
* 在特定按鍵，按下去的時候觸發
  * `.enter`, `.tab`, `.delete`, `.esc`, `.space`, `.up`, `.down`, `.left`, `.righ`
* 使用`.enter`只有再按下 enter 鍵才會觸發

```html
  <div id="app">
   <h6 class="mt-3">別名修飾</h6>
  <input type="text" class="form-control" v-model="text" @keyup.enter="trigger('enter')">

  </div>
```

```javascript
Vue.createApp({

  data(){
     return {
     
    }
  },
  methods: {
   trigger: function(name) {
      console.log(name, '此事件被觸發了')
    },
  }
}).mount('#app')
```

### 1-2. 相應按鍵時才觸發的監聽器
* 僅在按下相應按鍵時才觸發鼠標或鍵盤事件的監聽器
* `.ctrl`, `.alt`, `.shift`, `.meta`
* 此範例為 `@keyup.shift.enter`
```html
 <h6 class="mt-3">相應按鍵時才觸發的監聽器</h6>
 <input type="text" class="form-control" v-model="text" @keyup.shift.enter="trigger('shift + Enter')">
```
![](https://i.imgur.com/gQjWYVy.png)

2. 滑鼠修飾符
`.left` 只當點擊鼠標左鍵時觸發。
`.right` 只當點擊鼠標右鍵時觸發。
`.middle` 只當點擊鼠標中鍵時觸發。
* 按下滑鼠右鍵
`<span class="box" @click.right="trigger('right button')">`

```html
          <h4>滑鼠修飾符</h4>
          <h6 class="mt-3">滑鼠修飾符</h6>
          <div class="p-3 bg-primary">
            <span class="box" @click.right="trigger('right button')">
            </span>
          </div>
```

![](https://i.imgur.com/lddG6zT.png)


3. 事件修飾符
* 不會限定，是使用滑鼠還是鍵盤，是針對事件本身來進行修飾
``.stop`` - 調用 `event.stopPropagation()`。
``.prevent`` - 調用 `event.preventDefault()`。
``.capture`` - 添加事件偵聽器時使用 capture 模式。
``.self`` - 只當事件是從偵聽器綁定的元素本身觸發時才觸發回調。
``.once`` - 只觸發一次回調。
* 常用的是 `<a>`外部連結：移除預設事件
```html
       <h4>事件修飾符</h4>
          <ul>
            <li>.stop - 調用 event.stopPropagation()。</li>
            <li><strong>.prevent - 調用 event.preventDefault()。</strong></li>
            <li>.capture - 添加事件偵聽器時使用 capture 模式。</li>
            <li>.self - 只當事件是從偵聽器綁定的元素本身觸發時才觸發回調。</li>
            <li>.once - 只觸發一次回調。</li>
          </ul>
          <a href="https://www.google.com/" @click.prevent="trigger('prevent')">加入 Prevent</a>
```

資料來源：
六角學院 - Vue 3.js
