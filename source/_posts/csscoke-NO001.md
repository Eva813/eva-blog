---
title: Amos金魚都能懂的切版教學影片- csscoke NO001
date: 2020-11-27 15:26:57
tags: css 切版 多重背景
---
![](https://i.imgur.com/v5d6H4B.jpg)

## 一開始我的製作方式是
* 一個區塊放圖片
* 一個區塊放h1,下標
* 最後在一個區塊h2,p
* 想讓文字與圖的區塊疊住再去做調整，不過失敗...

## Amos老師製作方式
* 用一個取名為banner的區塊
* 裡面放一個container的容器，作為來設定裝下裡面文字的容器
* 此container中，再放一個banner-txt，用來放置文字區塊

```html
<body>
  <div class="banner">
    <div class="container">
      <!-- 設定固定寬度的容器 -->
      <div class="banner-txt">
        <h1>金魚都能懂的
          <small>這個網頁畫面怎麼切</small>
        </h1>
        <h2>圖文滿版區塊</h2>
        <p>這畫面實在常見，在各種樣版網站可說是設計常客
          金魚切不出來實在說不過去阿</p>

      </div>
    </div>
  </div>

</body>

```
### 1.css的簡單解析
* 在banner的地方會去設定填滿整個視窗
* container用來設定所放入文字的容器，他的寬與高，並讓他置中
* banner-txt的地方，將高度設定和container一樣，並設定flex讓內容文字能夠依照想要的方式來進行排列


---

## 將幾個大重點放於下方說明：

### 2.CSS Reset
[金魚都能懂網頁設計入門 : CSS Reset ](https://www.youtube.com/watch?v=WtjXBIyxhw8)
meyerweb.com
計網頁時重要的起手式，主要是為了讓各家瀏覽器的網頁外觀維持一致。
不同瀏覽器，在一開始，會有預設的部分，會導致在做css時和預想的設置會有差異，所以要記得reset。


---


### 3.flex 的使用
[玩轉CSS ](https://www.youtube.com/watch?v=_nCBQ6AIzDU)
```
display: flex;
flex-direction: column; 垂直
justify-content: center; 
align-items:flex-start
```
### 此網頁flex排列的過程

![](https://i.imgur.com/eMWlawn.png)

* display: flex;

![](https://i.imgur.com/0cDI8Nq.png)

* .banner-txt small {  display: block;}

![](https://i.imgur.com/lWwcqSe.png)

* .banner-txt {  flex-direction: column;}

![](https://i.imgur.com/xHmKCi0.png)

* .banner-txt {  flex-direction: column;justify-content: center;}

![](https://i.imgur.com/ALt0gm2.png)

### 在金魚...此h1下面 加上分隔線

* 以邊框來設，線變成滿版一條
* 因為我們前面使用了flex的特性，讓他們的排列變成直排（主軸變直的）
* 故'次軸'變成橫的，所以在預設情形下，次軸會填滿主軸的寬或高
```
.banner-txt h1 {
      font-size: 80px;
      border-bottom: 1px solid #333;
    }
```

![](https://i.imgur.com/VVDVFKG.png)

* 在剛剛設置flex的那邊設置

```
.banner-txt{ align-items: flex-start;}
```


![](https://i.imgur.com/eRE9dBP.png)


---

## 多重背景
* 整個版面是將此背景圖放在banner區塊
* 用線性方式

```css
/* 多重背景的使用 */
      background: linear-gradient(115deg, darksalmon 50%, transparent 50%)center center /100% 100%, url(https://picsum.photos/seed/picsum/1200/600)right center /auto 100%;
```

linear gradient(顏色漸變方向 <開始方向 結束方向>, 色碼1 位置1 ,色碼 2 位置2,....)    fixed (位置：固定)  center center(背景：位置，x軸 y軸) / 100% 100% (背景：尺寸，寬、高)



[Day26：小事之 多重背景與漸層背景 CSS3 Gradients](https://ithelp.ithome.com.tw/articles/10197136)
[你真的理解CSS的linear-gradient？](https://www.w3cplus.com/css3/do-you-really-understand-css-linear-gradients.html)

---

### margin 與 padding


![](https://i.imgur.com/HmMAKt5.png)



---
可用假圖產生圖片，尺寸大小或是隨機出圖都可以在網址上設定。
假圖產生網址 1：https://fakeimg.pl/
假圖產生網址 2：https://picsum.photos/
 
參考資料
[金魚都能懂的網頁設計-雜記](https://medium.com/chloelo925/%E9%87%91%E9%AD%9A%E9%83%BD%E8%83%BD%E6%87%82%E7%9A%84%E7%B6%B2%E9%A0%81%E8%A8%AD%E8%A8%88-%E9%9B%9C%E8%A8%98-1e6068054e4a)
