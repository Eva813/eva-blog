---
title: Amos金魚都能懂的切版教學影片-csscoke NO011
date: 2020-11-27 15:36:56
tags: css 切版 icon animation
---
[成品](https://eva813.github.io/my-projects/coke-NO011(v1).html)
![](https://i.imgur.com/4oCdMgG.png)

* 基本網頁的架構
```html
<body>
  <!-- 大區塊 wrap -->
  <div class="wrap">
    <!-- 區塊一 -->
    <div class="item">
      <div class="icon">

      </div>
      <div class="txt">
        <h3></h3>
        <p></p>
      </div>
    </div>
    <!-- 區塊二 -->
    <div class="item">
      <div class="icon">

      </div>


      <div class="txt">
        <h3></h3>
        <p></p>
      </div>
    </div>
    <!-- 區塊三 -->
    <div class="item">
      <div class="icon">

      </div>
      <div class="txt">
        <h3></h3>
        <p></p>
      </div>
    </div>

  </div>



</body>
```

### `wrap` 的處理

```css
.wrap {
      display: flex;
      max-width: 1200px;
      margin: auto;


    }
```

![](https://i.imgur.com/5PBCM6c.png)



###  三個item的處理
* 方框的角是圓弧`border-radius: 5px;`，[參考](https://developer.mozilla.org/zh-CN/docs/Web/CSS/border-radius)
* 方框背後有陰影`box-shadow`，[參考](https://developer.mozilla.org/zh-TW/docs/Web/CSS/box-shadow)

```css
.item {

      width: 340px;
      align-items: center;
      text-align: center;
      margin: 10px 15px;
      padding: 10px;
      border: 5px solid #F8D0CD;
      border-radius: 5px;
      box-shadow: 2px 2px 10px 1px #F8D0CD;
    }
```


### 破格
* 設置margin凸出去，就設置負的

```css
.item .icon {
      width: 150px;
      height: 150px;
      /* 將整個div放中間，不是用text-align */
      margin: -75px auto 0;
      background-color: blue;
      font-size: 85px;
      color: #f5afac;
      line-height: 150px;
      /* line-height可以用來做單航文字的垂直居中 */
      line-height: 150px;
      /* **** */
      position: relative;
      border-radius: 50%;

    }
```
* 先是方形的，後來加了radious

![](https://i.imgur.com/vXjT2NR.png)

![](https://i.imgur.com/joOrRwU.png)


#### 在icon前加一個物件，
```css
.item .icon::before {
      content: '';
      position: absolute;
      border: 10px solid #f5afac;
      width: 100%;
      height: 100%;
      /* left: 0; right: 0;  要注意有border*/
      left: -10px;
      right: -10px;
      border-radius: 50%;
      margin: -15px auto 0;
      
      /*下一張圖，加上框線的處理*/
      border-top: 10px solid #f9cec2;
      border-right: 10px solid #f9cec2;
      border-bottom: 10px solid transparent;
      border-left: 10px solid transparent;
      transform: rotate(-45deg);
      margin: -10px auto 0;
     
    }
```
* 先讓外框變原形，給予線條
* 設計線的上下左右，顏色與透明度（是以原本正方形的上下左右去看）
* 接著選轉，讓它到正上方

![](https://i.imgur.com/Vg4Rhg5.png)

![](https://i.imgur.com/9NuBZmd.png)

![](https://i.imgur.com/9UdygSn.png)

* #### 要注意突出去的部分已經超過wrap的範圍，會影響專案後續的呈現
![](https://i.imgur.com/31dbEZZ.png)


* #### wrap要做調整

![](https://i.imgur.com/mwELszb.png)

![](https://i.imgur.com/FeeKHp9.png)




## icon的動畫

* `@keyframes ＋ 動畫名稱` 動畫內容（劇本） 關鍵影格的設定(0-100%(結束) or from-to )一段動畫在一個連續時間軸上進行著
* ` animation` ＋ 動畫名稱 播放時間  延遲執行的時間（要等多久才播放） 速度 次數 方向 填充模式(起點狀態)  播放狀態（播放\暫停）

#### `animation:name duration | timing-function | delay | iteration-count | direction | fill-mode | play-state;`




[完整解析 CSS 動畫 ( CSS Animation )](https://www.oxxostudio.tw/articles/201803/css-animation.html)
[金魚都能懂網頁設計入門 : Animation 網頁動畫 - 鐵人賽第十九天](https://www.youtube.com/watch?v=H9xZ9mExrZk&feature=youtu.be)
[動畫效果](https://dotblogs.com.tw/gra/2018/03/20/222028)

***
- animation-duration:5s;指的是「播放一次」動畫需要的時間
- animation-iteration-count:infinite;表示動畫播放的次數，預設值為 1 次，如果設定為 infinite 動畫就會無止盡的播放下去。
- animation-timing-function 動畫加速度函式:
    - linear:線性，沒有任何加速減速
    - ease、ease-in、ease-out、ease-in-out:具有加速減速的動畫   
- animation-direction 動畫播放方向
    - normal：正常播放，從 0% 到 100% ( 預設值 )。
    - reverse：反轉播放，從 100% 到 0%。
    - alternate：正反轉輪流播放，奇數次為 0% 到 100%，偶數次為 100% 到 0%，若動畫播放次數只有一次就只會正常播放。
    - alternate-reverse：alternate 的相反，奇數次為 100% 到 0%，偶數次為 0% 到 100%，若動畫播放次數只有一次就只會反轉播放。
![](https://i.imgur.com/X8LBQPn.png)
***

```css=
/* item被摸到，然後搖裡面的icon */
    .item:hover .fas {
      animation: shake .2s linear infinite alternate;
    }

    @keyframes shake {
      0% {
        transform: rotate(-10deg);
      }

      100% {
        transform: rotate(10deg);
      }
    }
```

## Font Awesome 
* 使用前可以在官網創造帳戶
* 點選右上角的 ” Copy Kit Code ”
* 將剛剛複製的這串程式碼放入我們網站 HTML 程式碼中的 ``<head>`` 之中。
* 接下來就可以找自己享用的icon

![](https://i.imgur.com/ZZZ5mYn.png)


## @import "URI" 插入到 CSS 檔中
[@import 開心的結構化 CSS](https://ithelp.ithome.com.tw/articles/10195612)
