---
title: JS實作- 新年倒數
date: 2021-01-16 09:17:11
tags: ["JS","jQuery","setInterval"]
---
[成品](https://eva813.github.io/Eva_portfolio/countDown/countDown.html)

![](https://i.imgur.com/et0bQ96.png)

## 觀察功能需求
* 計算天、小時、分鐘、秒數的倒數
* 最下面顯示倒數年份
* page load的圖示(會旋轉1秒鐘)之後顯示倒數

## CSS部分

```css
/*增加讓螢幕寬度變化時，字的大小隨之改變
如果螢幕寬度為 500px 以下*/
@media (max-width: 500px) {
  h1 {
    font-size: 45px;
  }

  .time {
    margin: 5px;
  }

  .time h2 {
    font-size: 12px;
    margin: 0;
  }

  .time small {
    font-size: 10px;
  }
}

```

[Day22：小事之 Media Query
](https://ithelp.ithome.com.tw/articles/10196578)[CSS設定響應式(RWD)有幾種方法](https://malagege.github.io/blog/2019/05/05/CSS%E8%A8%AD%E5%AE%9A%E9%9F%BF%E6%87%89%E5%BC%8F-RWD-%E6%9C%89%E5%B9%BE%E7%A8%AE%E6%96%B9%E6%B3%95/)

## JS部分

### 1. 宣告最終日期(新的一年1/1的日期)

* 要先宣告取得目前的年份
* 再利用此變數帶入，要倒數的最終日期

```javascript=
//getFullYear(),用來取得日期物件當中本地時間的年份
const currentYear = new Date().getFullYear();
const newYearTime = new Date(`January 01 ${currentYear + 1} 00:00:00`);
```

時間日期
[JavaScript Date 時間和日期](https://www.fooish.com/javascript/date)
[getFullYear() Method](https://www.w3schools.com/jsref/jsref_getfullyear.asp)

### 2. 倒數計時的日、時、分、秒

* `currentTime` 取得目前時間
  ![](https://i.imgur.com/b3sgw3L.png)
* `diff = newYearTime - currentTime;`:計算出目前與未來最終日期的時間差異(此相減得出的是毫秒)
  ![](https://i.imgur.com/ly5elua.png)

* 計算天數、小時、秒數
    * `const d`1天等於24小時，1小時等於60分鐘，1分鐘等於60秒，1秒等於1000毫秒。所以，msec / 1000 / 60 / 60 / 24 保留整數就是天數。
    * `h = Math.floor(diff / 1000 / 60 / 60) % 24`:計算小時，並以除以24之後的餘數，來得到剩餘的小時。
    * 分鐘、以及秒數也是用同樣方式計算取得。
* 將取得的日期帶入畫面
    * 使用`.html()`：`$('#days').html(d);`
    * 在小時、分鐘、秒，是以兩位數呈現，需要當數字少於10，要呈現01,02...，所以使用三元運算子`$('#hours').html(h < 10 ? '0' + h : h);`，當數字小於10，要0+h;反之就只要呈現h。

* 最後要設定`setInterval(updateCountdown, 1000);` ，使其每秒倒數。

```javascript
function updateCountdown() {
  const currentTime = new Date();
  //console.log(currentTime);
  const diff = newYearTime - currentTime;
  //diff計算出來是毫秒
  //days
  const d = Math.floor(diff / 1000 / 60 / 60 / 24);
  //取得除以24之後的餘數
  const h = Math.floor(diff / 1000 / 60 / 60) % 24;
  const m = Math.floor(diff / 1000 / 60) % 60;
  const s = Math.floor(diff / 1000) % 60;
  $('#days').html(d);
  //當數字少於10，要呈現01,02...
  $('#hours').html(h < 10 ? '0' + h : h);
  $('#minutes').html(m < 10 ? '0' + m : m);
  $('#seconds').html(s < 10 ? '0' + s : s);

}

//設定每一秒都會循環倒數
setInterval(updateCountdown, 1000);

```
參考資料：
[【JS】javascript時間Date()介紹與補零應用的技巧!!!](http://zhi-yuan-chenge.blogspot.com/2015/10/jsjavascript_16.html)
[js和vue實現時分秒倒計時的方法](https://kknews.cc/zh-tw/code/8g4vo3e.html)
三元運算子：
[Day19【ES6 小筆記】三元運算子-以哥哥的擇偶條件為例
](https://ithelp.ithome.com.tw/articles/10218274)

### 3.載入圖示

* 設定在載入之後，要隔多久執行功能
* 在此設定載入1秒之後，要移除loading畫面，呈現出倒數的時間

```javascript
setTimeout(function () {
  $('#loading').remove();
  //要在css檔案的countdown，先設置display：none
  $('#countdown').css('display', 'flex');
}, 1000);
```

### 4.放入要倒數的年份
```javascript
$('#year').html(currentYear + 1)
```








---
[網頁練習 -倒數計時器實作(上)](https://ithelp.ithome.com.tw/articles/10238189)
[5 Best Countdown Timer JavaScript Libraries (2020 Update)](https://www.cssscript.com/best-countdown-timer/)
[網頁練習 -倒數計時器實作(下)](https://ithelp.ithome.com.tw/articles/10238547)
