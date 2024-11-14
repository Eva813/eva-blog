---
title: tribute-page 切版練習
date: 2020-11-27 15:00:33
tags: ["CSS"]
categories: CSS
---
在freeCodeCamp上，可以免費學習到程式語言的課程，除了提供基礎的介紹外，也有一些實作範本可以練習，此次，主要練習css切版的基礎，製作一個一頁的頁面

## 頁面分析
![](https://i.imgur.com/HfmFPC0.png)

![](https://i.imgur.com/hGHbOHb.png)



#### 先試著自己動手做
### 我區分的：
```htmlembedded
<div class="header">
    <h1>Dr. Norman Borlaug</h1>
    <p>The man who saved a billion lives</p>
  </div>
 ```
 ```htmlembedded
 <div class="container">
    <img src="https://picsum.photos/500/400?random=1" alt="">
    <p>Dr. Norman Borease wheat yields - part of his
      life-long war on hunger.</p>
      </div>
    <div class="list">
      <h2>Here's a time line of Dr. Borlaug's life:</h2>
      <ul>
       li*16--
      </ul>
      <p>"Borlaug's life and achievement are testimony to the far-reaching contribution that one man's towering
        intellect,
        persistence and scientific vision can make to human peace and progress."
        <br>
        -- Indian Prime Minister Manmohan Singh</p>
      <h3><strong>If you have time, you should read more about this incredible human being on his a
          <a href="https://en.wikipedia.org/wiki/Norman_Borlaug">Wikipedia entry</a> .</strong></h3>


    </div>
 ```
 ## 自己切的時候，遇到的問題
 * img 在網頁縮放時，沒有好好的跟著容器變化大小，會凸出去。
     * 一開始，是設定固定寬度、高度去調整
     * 解決：使用`max-width`
     * 另外img的display也要調整為block或inline-block，比較好調整置中
* 在中間很多列表的表頭，上下空間與置中一直沒有調整好
    * 發現區段沒有設置h3，讓他形成一個區塊
    * 解決：使用開發者工作，去看margin、padding產生空間


---

### 解法：

## 1.在最上面的區塊，使用main去包
```htmlembedded=
<main id="main">
 <h1>..</h1>
    <p>Ts</p>
  </main>
```
### style的部分要注意置中\字的大小
* margin,padding 做了些調整
```css
 h1 {
    font-size: 2.5em;
  }
   #main {
    margin: 30px 8px;
    padding: 15px;

  }
```

## 2.圖片區塊

使用` <figure>`用來引用任何內容像是文字段落、圖片、圖表或程式碼片段。`<figure>` 中還可以有一個 `<figcaption>` 標籤用來說明該 figure 區塊的標題。

```htmlembedded=
<figure id="img-div"><img src="/img/tribute-cover.jpg" alt="">

    <figcaption id="imd-cap">
      ar on hunger.
    </figcaption>
  </figure>
```

### style的部分
* img要記得設最大寬度，讓他置中
* #img-div，是包含照片的容器，注意原本版面左右是有留白,以及灰色，所以設定padding:10px
```css=
 img {
    max-width: 100%;
    display: inline-block;
    height: auto;
    margin: 0 auto;
  }
 #img-div {
    background: white;
    padding: 10px;
    margin: 0;
  }
```
## 3.u1資訊的部分
* 以section做區塊
* 使用blockquote，用來引用段落文字，如果引用的內容來自於網路，還可以用 cite 屬性指定引用來源 (URL)。


```htmlembedded
<section id="tribute-info">
<h3 id="headline">

</h3>
<ul>
  <li></li>
  <li></li>
  <li></li>
  <li></li>
  <li></li>
</ul>

<blockquote>
  <p></p>
  <cite></cite>
</blockquote>

```

```css
#headline {
    margin: 50px 0;
    text-align: center;
  }

  ul {
    max-width: 550px;
    margin: 0 auto 50px auto;
    text-align: left;
    line-height: 1.6;
  }

  li {
    margin: 16px 0;
  }
```




## 4.最下面一段

* 以h3作為最後一段文字的區塊

```htmlembedded
<h3>
<a></a>
</h3>
```
```css=
 a {
    color: #477ca7;
  }

  a:visited {
    color: #74638f;
  }
blockquote {
    font-style: italic;
    max-width: 545px;
    margin: 0 auto 50px auto;
    text-align: left;
  }
```


## 完成的網址
[我製作的網頁](https://eva813.github.io/my-projects/tribute-page.html)
[codpen的解法](https://eva813.github.io/my-projects/tribute-page(v2).html)

---

參考資料：

[假圖](https://chupainotebook.blogspot.com/2019/02/blog-post.html?utm_source=feedburner&utm_medium=feed&utm_campaign=Feed:+blogspot/SMcpvm+(+%E7%AB%B9%E7%99%BD%E8%A8%98%E4%BA%8B%E6%9C%AC)&m=1)
[金魚都能懂的網頁設計-雜記](https://medium.com/chloelo925/%E9%87%91%E9%AD%9A%E9%83%BD%E8%83%BD%E6%87%82%E7%9A%84%E7%B6%B2%E9%A0%81%E8%A8%AD%E8%A8%88-%E9%9B%9C%E8%A8%98-1e6068054e4a)
[CSS垂直置中技巧，我只會23個，你會幾個](http://csscoke.com/2018/08/21/css-vertical-align/)
