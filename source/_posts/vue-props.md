---
title: Vue.js 3 -  資料傳遞 props
date: 2021-09-21 11:42:48
tags: ["Vue"] 
categories: Vue
---
因為Vue每個元件都是各自獨立，所以我們無法在各自元件去調整資料，而直接修改另一個元件的資料。
所以，要使元件之間可以相互溝通，就需要使用資料傳遞方式。
在傳遞方式：
* 外層傳遞內層 : props
* 內層向外傳遞 : emit 
當外層元件，要將資料往內層元件丟時，此過程就是使用props。

![](https://i.imgur.com/FTbHDPk.png)
## 傳遞資料的方式：
### 靜態資料傳入
* 預期將外層傳入一個url的變數，傳進給內層使用
    * photo為內部元件，在template 我希望能夠取得外層傳入的圖片(urlimg)
    * 故在 props 以此命名，並將它綁到 template
```javascript
 app.component('photo', {
        props: ['url'],
        template: `<img :src="url" class="img-thumbnail" alt>`
    });
```
* 將上方的元件資料，加到外層元件：像是加入html屬性一樣
```javascript
<photo
    url="https://images.unsplash.com/photo-1605784401368-5af1d9d6c4dc?ixid=MXwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHw%3D&ixlib=rb-1.2.1&auto=format&fit=crop&w=600&q=80">
</photo>
```

![](https://i.imgur.com/f079ziY.png)

### 動態資源
* 使用 `v-bind`，綁定內部元件的props ，使資料可以跟外層元件連動
* 記得使用口訣： 前內、後外 =>前面就是props內的名稱，後面就是外層元件的名稱
![](https://i.imgur.com/BMVZtkn.png)
```htmlembedded
  <h3>動態資源</h3>
  <p>技巧：前內、後外</p>
  <photo v-bind:urlimg="imgUrl"></photo>
```

## 資料傳遞為 單向數據流
* 外部所定義的資料，當往內層傳遞，是單向性
* 不能試圖使用v-model或各種方式，來改變props傳入的內容
    * 以下範例：在子元件 `photo2` 放入 `input`綁定 `v-model`
    * 當嘗試在輸入框修改網址，會出現錯誤

```javascript
//外層元件綁定 url
<photo2 :url="imgUrl"></photo2>
```
```javascript
const app = Vue.createApp({
    data() {
    return {
      imgUrl: 'https://images.unsplash.com/photo-1605784401368-5af1d9d6c4dc?ixid=MXwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHw%3D&ixlib=rb-1.2.1&auto=format&fit=crop&w=600&q=80',
         };
     },
});

 app.component('photo2', {
    props: ['url'],
    template: `<img :src="url" class="img-thumbnail" alt><br>
    <input type="text" v-model="url"> {{ url }}`
     })
```
![](https://i.imgur.com/OmmIxpi.png)

![](https://i.imgur.com/2BqCNTp.png)

## 在內部元件，要為props來命名
在命名上有些要注意的地方
* 首字母大寫: PostData、SetItems
* 駝峰命名法: postData、setItems
要注意：在 HTML 中使用時必須使用 kebab-case (短橫線分隔)且應該為小寫。

```javascript
<div id="app">

 <photo3 :super-url="imgUrl" ></photo3>
</div>

<script>
Vue.component("photo3", {
  props: ["superUrl"],
  template: `<img :src="superUrl" class="img-thumbnail alt >"`
});
</script>
```

![](https://i.imgur.com/57P1JDW.png)



## 定義 Props 型別驗證
* 使用型別驗證，會改用大括號，裡面放入props名稱，並用物件形式加入設定的內容
    * ```props:{
        propC: {
        type: String,
        required: true,
        }
      }```
* 其中，可以針對該props 來設定：
    * `type`型別 ： 可以是 String、Number、Boolean、Array、Object、Date、Function、Symbol
    * `default` ：如果該 prop 沒有接收到傳入的值，就會使用 default 的值作為預設值。
    * `required` ： 是否為必填項，如果設為 true 則表示必須要有值傳入，若沒有，就會出現錯誤提示。
```javascript
app.component('props-validation', {
    props: {
      // 單一型別檢查，可接受的型別 String, Number, Object, Boolean, Function(在 Vue 中可使用 Function 驗證型別)
      // null, undefined 會直接通過驗證
      propA: Function,

      // 多個型別檢查
      propB: [String, Number],

      // 必要值
      propC: {
        type: String,
        required: true,
      },

      // 預設值
      propD: {
        type: Number,
        default: 300
      },

      // 自訂函式
      propE: {
        type: Object,
        default() {
          return {
            money: 300
          }
        }
      },

      // 自訂驗證
      propF: {
        validator(value) {
          return value > 1000
        }
      }, 
    },
```
[六角學院](https://www.hexschool.com/courses/vue3.html)
[[DAY12]跟 Vue.js 認識的30天 - Vue 模組資料傳遞(`props`)](https://ithelp.ithome.com.tw/articles/10254050)
