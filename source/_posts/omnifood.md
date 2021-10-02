---
title: CSS練習- omnifood 餐廳網頁
date: 2020-12-13 13:05:53
tags: ["udemy","CSS","icon","grid","多重背景","opacity"]
---

## 介紹
[成品](https://eva813.github.io/Eva_portfolio/Omnifood/omnifood.html)
使用css來製作一個一頁式的網站，將網站分為表頭、特色區塊、食物照片區、訂餐方法介紹...等，從不同區塊，去操作字型、文字排版、圖片處理。

![](https://i.imgur.com/OwpZq8P.png)


## 首先，文字字型的選擇
先用Normalize.css，下載並連結
字型：使用google font > lato
![](https://i.imgur.com/4VSiIEZ.png)

text-rendering告訴渲染引擎工作時如何優化顯示文字

```css=
*{
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
html{
  background-color: #fff;
  color: #555;
  font-family: 'Lato', 'Arial',sans-serif;
  font-weight: 300;
  font-size: 20px;
  text-rendering: optimizeLegibility;
  /* 繪制文本時易讀性優先，會啟用字距調整和連字 */
}

/* content will be in rows */
.row{
  /* 只有這邊是用px，之後的margin等設定都會用% */
 max-width: 1140px; 
 margin:0 auto;
/* 讓左右的距離能夠平均分散，使其置中 */

}



```
# head區塊
![](https://i.imgur.com/pc0jiX3.png)
## 表頭的製作
```htmlembedded=
 <div class="hero-text-box">
      <h1>Goodbye junk food. Hello super healthy meals.
      </h1>
      <a href="#">I’m hungry</a>
      <a href="#">Show me more</a>

    </div>

```

![](https://i.imgur.com/ioW5W5p.png)

* 利用background-image插入圖片後，往上滑會出現上方的空白處
    * 是因為h1的margin（因設置normalize.css）
* 此時照片也還沒有填滿、置中

![](https://i.imgur.com/dZzd6V7.png)
![](https://i.imgur.com/pe6AUaN.png)

### 調整背景圖位置 

![](https://i.imgur.com/zoaaQoB.jpg)

### 讓h1文字置中
透過使用絕對定位，讓文字可以置中

```css=
.hero-text-box{
  position: absolute;
 
  width: 1140px;
  top: 50%;
  left:}
```
![](https://i.imgur.com/r4IryMQ.jpg)

* transform: translate(-50%,-50%);

![](https://i.imgur.com/QcgKtPg.jpg)


### 文字放在圖片上使得看不清楚
* 將背景圖調成深色
```css=
background:linear-gradient(0deg,rgba(0,0,0,.6),rgba(0,0,0,.5)) center center,
   url(./Omnifood\ Contents/hero.jpg) no-repeat center center/cover ;
```
![](https://i.imgur.com/NmUA3qy.jpg)


## h1文字區的細節調整

* 調整字的顏色、大小
* 字母、單字之間的間距
```css=
.hero-text-box h1{
margin: 0;
color: #fff;
/* 這裏的字體大小用百分比，是為了相對於一開始設定font-size: 20px;，以他的百分比來計算 */
font-size: 240%;
font-weight: 400;
letter-spacing: 1px;
 text-transform: uppercase; /*將字母改為大寫 */
 word-spacing: 3px;
}
```

## 文字區下面的按鈕
html結構是使用`<a>`來製作連結的點選
將型態改為inline-block

```html
      <a class="btn btn-full" href="#">I’m hungry</a>
      <a class="btn btn-ghost " href="#">Show me more</a>
```
* 希望連結與被點選的樣式都是一樣
* 顏色是到：UI flat color tool ; 要找尋顏色深淺 [0 to 255](https://www.0to255.com/e67e22)

```css
.btn:link,.btn:visited{
  display: inline-block;
  text-decoration: none;
padding: 10px 15px;
border-radius: 200px;

}

.btn-full:link,.btn-full:visited{
  /* UI flat color tool */
  background-color: #e67e22;
  /* border: 1px solid #e67e22 ; */
  color: #fff;
}

.btn-ghost:link,.btn-ghost:visited{
  border: 1px solid #e67e22 ;
  color:#e67e22;

}
```
![](https://i.imgur.com/MuiBMCl.png)






### 按鈕的觸碰與點選
`:active` 滑鼠按下的樣式; `:focus` 鍵盤聚焦的樣式 ;`:hover` 滑鼠滑過

* 注意transition此屬性是放在要被摸的元素裡面，不是hover那裡
[How TO - Transition on Hover](https://www.w3schools.com/howto/howto_css_transition_hover.asp)

`.btn:link,.btn:visited{}`
```css=
  /* transition 放在這裡！ */
 transition: background-color 0.5s,border 0.5s,color 0.5s;

}

.btn:hover,.btn:active{

 background-color: #cf6d17;
}
.btn-full:hover,.btn-full:active{ 
  border: 1px solid #cf6d17 ;
  color: #fff;
}

.btn-ghost:hover,.btn-ghost:active{
  border: 1px solid #cf6d17 ;
  color:#fff;

}

```
![](https://i.imgur.com/3UM8SpB.png)


## 表頭導覽列的製作

[金魚都能懂的這個網頁畫面怎麼切 : 導覽列](https://ithelp.ithome.com.tw/articles/10219845)

* header裡面的nav，html架構：
```htmlembedded

    <nav>
      <div class="row">
        <img src="./Omnifood Contents/logo-white.png" alt="Omnifood logo" class="logo">
        <ul class="main-nav">
          <li> <a href="#">Food delivery</a></li>
          <li><a href="#">How it works</a></li>
          <li><a href="#">Our cities</a></li>
          <li><a href="#">Sign up</a></li>

        </ul>

      </div>
    </nav>

```
![](https://i.imgur.com/jOySkuH.png)

* logo
```css=
header .logo{
height: 100px;
width: auto;
float: left;  
}
```
![](https://i.imgur.com/KMLsFMM.png)


## nav

* 讓nav區靠右
* 為了讓四個連結區塊可以橫向排列，設定inline-box
* 針對字型、顏色等做調整
* 並於連結下方增加線的動畫效果
```css

.main-nav{
float: right;
margin-top: 55px;

}
.main-nav li{
display: inline-block;
margin-left: 20px;

}
.main-nav li a:link,.main-nav li a:visited{
text-decoration: none;
font-weight: 400;
font-size: 90%; /*想要18px，18px / 20px=0.9*/
color: #fff;
text-transform: uppercase;
position: relative;
padding-bottom: 5px;

}

.main-nav a::after{
  content: '';
  width: 100%;
  position: absolute;
  border: solid .5px rgba(230, 126, 34,.6);
  left: 0;
  bottom:0 ;
  margin-top: 5px;
  transform: scale(0);
  transition: 0.5s;
  
}

.main-nav li:hover a::after,.main-nav li:active a::after{
transform: scale(1);

}

```
![](https://i.imgur.com/1su6NCZ.png)

---

# 特色區塊

![](https://i.imgur.com/nl0Z3uS.png)



* ``—`` 用html符號 --> `&mdash;`
     ![](https://i.imgur.com/kl8kJq1.png)
     更多...[HTML Entity List](https://www.freeformatter.com/html-entities.html)
* features這部分，使用section標籤來包覆，而其中的不同區塊則使用`.row`來包覆內容(並用來固定寬度)
* 這裏的html運用grid來配置欄位

```html
<section class="section-features">
    <div class="row">

      <h2>Get food fast &mdash; not fast food</h2>
      <p class="long-copy">Hello, we’re Omnifood, your new premium food delivery service. We know you’re always busy. we promise!</p>
    </div>
<div class="row">
      <!-- 第二區塊：features，這裏要開始應用grid -->
      <div class="col span-1-of-4">
        <h3>Up to 365 days/year</h3>
        <p>Never cook again! We really mean that. Our subscript </p>
      </div>
      <div class="col span-1-of-4">
        <h3>Ready in 20 minutes</h3>
        <p>You're only twenty minutes away from your delicious ant chefs in each town to ensure that you're 100% happy.
        </p>
      </div>
      <div class="col span-1-of-4">
        <h3>100% organic</h3>
        <p>All our vegetables are fresh, organic and local. Animals are raised without added hormones or antibiotbetter!</p>
      </div>
      <div class="col span-1-of-4">
        <h3>Order anything</h3>
        <p>We don't limit your creativity, which means you cacious meals. It's up to you!</p>
      </div>
    </div>
  </section>
```


![](https://i.imgur.com/sg0YUDI.png)

### icon 
使用ionicon:[ionicon](https://ionicons.com/)
```javascript
<script src="https://unpkg.com/ionicons@5.2.3/dist/ionicons.js"></script>`
```
* 讓他斷行排列使用block
* 字的大小適用比例設置

```css
.section-features .icon-big{
display: block;
  font-size: 350%;
  color: #e67e22;
  margin-bottom: 10px;
  font-weight: 100;

}
```


## h2的文字與p


```css
.section-features .long-copy{
width: 70%;
/* 總寬100%,左邊設置15%，就會平均 */
margin-left: 15%;

/* line-height: 1.3; */
line-height: 145%; /*120-145%最合適 */
/* word-spacing: 1px; */
}
```


## 欄位col的處理（class="items"）


```css
.section-features h3{
  font-weight: 300;
  font-size: 110%;
  text-transform: uppercase;
  
  margin-bottom: 15px;
}
.section-features .items {
  padding:1% ;

}

.section-features .items p{
  line-height: 145%;
  font-size:90% ;
}
```

![](https://i.imgur.com/Sr0NhBr.png)

---

# 食物照片區

![](https://i.imgur.com/BzV03pk.png)

* 這裏的內容不適用`.row`來包覆，因為我們希望它的寬度可以站滿橫向的視窗

### html結構
```html

  <section class="section-maels">
    <!-- 要製作2列各4格的食物照片，這裏使用ul,li的方式排列 -->
    <ul class="meals-showcase">
      <li>
        <figure>
          <img src="./Omnifood Contents/1.jpg" alt="Korean bibimbap with egg and vegetables">
        </figure>
      </li>
      <li>
        <figure>
          <img src="./Omnifood Contents/2.jpg" alt="Simple italian pizza with cherry tomatoes">
        </figure>
      </li>
      <li>
        <figure>
          <img src="./Omnifood Contents/3.jpg" alt="Chicken breast steak with vegetables">
        </figure>
      </li>
      <li>
        <figure>
          <img src="./Omnifood Contents/4.jpg" alt="Autumn pumpkin soup">
        </figure>
      </li>


    </ul>

    <ul class="meals-showcase">
      <li>
        <figure>
          <img src="./Omnifood Contents/5.jpg" alt="Paleo beef steak with vegetables">
        </figure>
      </li>
      <li>
        <figure>
          <img src="./Omnifood Contents/6.jpg" alt="Healthy baguette with egg and vegetables">
        </figure>
      </li>
      <li>
        <figure>
          <img src="./Omnifood Contents/7.jpg" alt="Burger with cheddar and bacon">
        </figure>
      </li>
      <li>
        <figure>
          <img src="./Omnifood Contents/8.jpg" alt="Granola with cherries and strawberries">
        </figure>
      </li>

    </ul>


  </section>
```
![](https://i.imgur.com/zBU6WCx.png)


* 設定此區塊滿版，並讓他們設定為區塊
* 讓li，每個設定寬度為25%(4個共100％)，並利用float使他們比鄰排列

```css
.meals-showcase{
  list-style: none; /* 去除ul的預設樣式*/
  display: block;
  width: 100%;

}
.meals-showcase li{
  float: left;
  width: 25%;
}
```

![](https://i.imgur.com/jaYired.jpg)


* 上面的照片並未合乎大小，以及置中

```css
.meals-showcase figure{
  width: 100%;

}
.meals-showcase figure img{
  width: 100%;
  

}
```

![](https://i.imgur.com/XNsC9JM.jpg)

* 去除上圖的空隙vertical-align: middle;

```css
.meals-showcase figure img{
  width: 100%;
  vertical-align: middle;}
  

```

![](https://i.imgur.com/QsAwjZx.jpg)



## 加入文字區在圖片上方

製作圖片被摸到，有圖片縮放的動畫

```css=
.meals-showcase figure img{
  width: 100%;
  vertical-align: middle;
transform: scale(1.15);
transition: transform .5s;

}
.meals-showcase figure:hover img{
  transform: scale(1);
}
```


* ### 當圖片的scale被放大，會超出原有容器
    * 處理方式：在所在的容器，設定overflow: hidden; 
    * [CSS深入理解：overflow:hidden——溢出,坍塌,清除浮动](https://juejin.cn/post/6844903650901540872),[overflow：hidden 失效](https://www.itread01.com/content/1524202931.html)
![](https://i.imgur.com/zBiOKSv.jpg)



* ### 讓圖片有黑色透明效果
    * 在圖片本身img設定透明度，在figure放圖標籤裡設定背景為黑色

![](https://i.imgur.com/ADUUni8.jpg)

```css
.meals-showcase figure{
  width: 100%;
  overflow: hidden;
  background-color:#000;

}
.meals-showcase figure img{
  width: 100%;
  vertical-align: middle;
transform: scale(1.15);
transition: transform .5s ,opicity .5s;
opacity: 0.7;
}
.meals-showcase figure:hover img{
  transform: scale(1);
  opacity: 1;
}

```

![](https://i.imgur.com/okb9F66.jpg)

---


# 訂餐方法介紹 的介面

![](https://i.imgur.com/HxrpNQG.png)

* html的結構

```html
<section class="meal-steps">
    <!-- 在row裡面，放一個h2標題，在增加一個row的div，使用grid兩欄的class -->
    <div class="row">
      <h2>How it works - Simple as 1, 2, 3</h2>

    </div>
    <div class="row">
      <!-- 左欄 -->
      <div class="col span-2-of-4">
        <img src="./Omnifood Contents/app-iPhone.png" alt="Omnifood-app" class="app-screen">
      </div>
      <!-------- 右欄 ------>
      <div class="col span-2-of-4">
        <div class="work-step">
          <div>1</div>
          <p>Choose the subscription plan that best fits your needs and sign up today.</p>
        </div>
        <div class="work-step">
          <div>2</div>
          <p>Order your delicious meal using our mobile app or website. Or you can even call us!</p>
        </div>
        <div class="work-step">
          <div>3</div>
          <p>Enjoy your meal after less than 20 minutes. See you the next time!</p>
        </div>
<!------- 與步驟同一欄，下載的按鈕使用<a> ------>

        <a href="#"><img src="./Omnifood Contents/download-app.svg" alt="apple store button"></a>
        <a href="#"><img src="./Omnifood Contents/download-app-android.png" alt="play store button"></a>

      </div>
    </div>


  </section>



```


![](https://i.imgur.com/F9MrnmA.png)


* ### 標題調整


:first-child

圖片靠右：text-align: right;


#### 右欄文字區

在數字部分：要讓他與文字區能夠排列，使用inline-block,float
    數字的外圈：參考以下css
```css=
.work-step{
  display: inline-block;
  margin-bottom: 50px;
}

  .work-step div{
 
  float: left;
  color:#e67e22 ;
  border: 2px solid #e67e22;
  border-radius: 50%;
  }
```
![](https://i.imgur.com/cPpSW9S.png)

#### 讓圓形變圓，增加寬高
```css=
.work-step div{
height: 55px;
  width: 55px;
  text-align: center;
  padding: 5px;
  font-size: 150%;}
```


![](https://i.imgur.com/odR0Tm5.png)

* margin-right: 25px;

![](https://i.imgur.com/OHIFt8g.png)

## 按鈕的處理

![](https://i.imgur.com/NOTJn47.png)


```css=
.btn-app img{
  height: 50px;
  width: auto;
  margin-right: 10px;
}
```


# 地點資訊

![](https://i.imgur.com/zvBsOgb.png)

```html
<section class="section-cities">
    <div class="row">
      <h2>We're currently in these cities</h2>
    </div>
    <div class="row">
      <!-- 第一個地點 -->
      <div class="col span-1-of-4 items">
        <img src="./Omnifood Contents/lisbon-3.jpg" alt="">
        <h3>Lisbon</h3>
        <div class="city-features">
          <ion-icon name="person" class="person"></ion-icon>
          1600+ happy eaters
        </div>
        <div class="city-features">
          <ion-icon name="star" class="star"></ion-icon>
          60+ top chefs
        </div>
        <div class="city-features">
          <ion-icon name="logo-twitter" class="logo-twitter"></ion-icon>
          @omnifood_lx
        </div>
      </div>
      <!-- 第二個地點 -->
      <div class="col span-1-of-4 items">
        <img src="./Omnifood Contents/san-francisco.jpg" alt="">
        <h3>San Francisco</h3>
        <div class="city-features">
          <ion-icon name="person" class="person"></ion-icon>
          3700+ happy eaters
        </div>
        <div class="city-features">
          <ion-icon name="star" class="star"></ion-icon>
          160+ top chefs
        </div>
        <div class="city-features">
          <ion-icon name="logo-twitter" class="logo-twitter"></ion-icon>
          @omnifood_sf
        </div>
      </div>
      <!-- 第三個地點 -->
      <div class="col span-1-of-4 items">
        <img src="./Omnifood Contents/berlin.jpg" alt="">
        <h3>Berlin</h3>
        <div class="city-features">
          <ion-icon name="person" class="person"></ion-icon>
          2300+ happy eaters
        </div>
        <div class="city-features">
          <ion-icon name="star" class="star"></ion-icon>
          110+ top chefs
        </div>
        <div class="city-features">
          <ion-icon name="logo-twitter" class="logo-twitter"></ion-icon>
          @omnifood_berlin
        </div>
      </div>
      <!-- 第四個地點 -->
      <div class="col span-1-of-4 items">
        <img src="./Omnifood Contents/london.jpg" alt="">
        <h3>London</h3>
        <div class="city-features">
          <ion-icon name="person" class="person"></ion-icon>
          1200+ happy eaters
        </div>
        <div class="city-features">
          <ion-icon name="star" class="star"></ion-icon>
          50+ top chefs
        </div>
        <div class="city-features">
          <ion-icon name="logo-twitter" class="logo-twitter"></ion-icon>
          @omnifood_london
        </div>

      </div>

  </section>
```



![](https://i.imgur.com/gC2Om3f.png)

![](https://i.imgur.com/YzSZlES.jpg)

```css=
.section-cities img{
  width: 100%;
  vertical-align: middle;
  margin-bottom: 15px;
}
```

```css=
.section-cities .icon-small{
  color: #e67e22;
 display: inline-block;
 text-align: center;
 font-size: 120%;
 margin-right: 10px;
 
/*讓icon與文字，置中、對其*/
 line-height: 120%;
 vertical-align: middle;
 margin-top: -5px;
  
}
```

![](https://i.imgur.com/1s1kBSH.png)


# 顧客回饋區

![](https://i.imgur.com/621x3W2.png)
* 區分為三個區塊，一列三欄的方式劃分
* 用`<blockquote>`來包顧客的回饋文字
* 並加入顧客的頭像以及姓名

```html
<section class="section-testimonials">
    <div class="row">
      <h2>Our customers can't live without us</h2>
    </div>
    <div class="row">
      <div class="col span-1-of-3">

        <blockquote>
          Omnifood is just awesome! I just launched a startup which leaves me with no time for cooking, so Omnifood is a
          life-saver. Now that I got used to it, I couldn't live without my daily meals!
          <cite><img src="./Omnifood Contents/customer-1.jpg" alt="">Alberto Duncan</cite>
        </blockquote>

      </div>
      <div class="col span-1-of-3">

        <blockquote>
          Inexpensive, healthy and great-tasting meals, delivered right to my home. We have lots of food delivery here
          in Lisbon,
          but no one comes even close to Omifood. Me and my family are so in love!
          <cite><img src="./Omnifood Contents/customer-2.jpg" alt="">Joana Silva</cite>
        </blockquote>

      </div>

      <div class="col span-1-of-3">

        <blockquote>
          I was looking for a quick and easy food delivery service in San Franciso. I tried a lot of them and ended up
          with
          Omnifood. Best food delivery service in the Bay Area. Keep up the great work!
          <cite><img src="./Omnifood Contents/customer-3.jpg" alt="">Milton Chapman</cite>
        </blockquote>

      </div>

    </div>



  </section>

```

![](https://i.imgur.com/LMPlqlx.png)

* 先做背景的處理

```css=
.section-testimonials {
  background:linear-gradient(0deg,rgba(0,0,0,.7),rgba(0,0,0,.7)) center center, url(./Omnifood\ Contents/back-customers.jpg)no-repeat center center/cover ;
  color: #fff;
  background-attachment: fixed;
}
```


![](https://i.imgur.com/ofR8ghb.png)







* 增加相片背景
* 可以看到文字與，照片相


![](https://i.imgur.com/deniLe4.jpg)




![](https://i.imgur.com/lO5KPTq.jpg)



![](https://i.imgur.com/VBEGepa.png)

* 在.section-testimonials img 增加vertical-align: middle;

![](https://i.imgur.com/bifGooB.png)

### 增加上面逗點符號

[符號表](https://css-tricks.com/snippets/html/glyphs/)


```css=
.section-testimonials blockquote:before{
 content:"\201C";
 font-size:500%;
 display: block;

position: absolute;
top: -5px;
left: -5px;
}

}
```


![](https://i.imgur.com/MJpSGz0.png)



# 訂購方案介紹區

![](https://i.imgur.com/6tr0o3E.png)


![](https://i.imgur.com/8lTrnUY.png)


* span標籤的應用
* 當全部設定寬度100%，會出現問題
    * 要利用篩選器，來選擇指定框



![](https://i.imgur.com/1XlErXn.png)







![](https://i.imgur.com/YnpnJBc.png)
* 解決方式，在html的標籤增加空行


![](https://i.imgur.com/Dke7ilN.png)



![](https://i.imgur.com/oC2aMcy.png)




# 頁尾表單的建構

![](https://i.imgur.com/y9sxEXR.png)


![](https://i.imgur.com/VQ5YHHR.png)


# footer


![](https://i.imgur.com/E4C4qY3.png)

* 放入相關連結的icon

```html
<footer>
    <div class="row">
      <div class="col span-1-of-2">
        <ul class="footer-nav">

          <li><a href="#">About us</a></li>
          <li><a href="#">Blog</a></li>
          <li><a href="#">Press</a></li>
          <li><a href="#">Android App</a></li>

        </ul>
      </div>

      <div class="col span-1-of-2 ">

        <ul class="socilmedia-link">
          <li><a href="#">
              <ion-icon name="logo-facebook" class="logo-facebook"></ion-icon>
            </a></li>
          <li><a href="#">
              <ion-icon name="logo-twitter" class="logo-twitte"></ion-icon>
            </a></li>
          <li><a href="#">
              <ion-icon name="logo-google" class="logo-google"></ion-icon>
            </a></li>
          <li><a href="#">
              <ion-icon name="logo-instagram" class="logo-instagram"></ion-icon>
            </a></li>

        </ul>


      </div>
      <div class="row">

        <p>
          copyright &copy; 2020 by Omnifood. All right reserved.
        </p>

      </div>



    </div>





  </footer>

```



![](https://i.imgur.com/X2b2hfu.png)

* 使用float來使物件放置左右側
* footer-nav li使用inline-block

```css
 footer{
  background-color: #333;
  padding: 50px;
  font-size: 80%;
}
footer .footer-nav{
  list-style: none;
  float: left;
}
.socilmedia-link{
  list-style: none;
  float: right;
}
.footer-nav li,.socilmedia-link li{
  display: inline-block;
  margin-right: 20px;
} 
```

![](https://i.imgur.com/Uph2clO.png)



### 連結顏色調整

```css
.footer-nav li:last-child,.socilmedia-link li:last-child{
  margin-right: 0px;
}

.footer-nav li a:link ,.footer-nav li a:visited, .socilmedia-link li a:link ,.socilmedia-link li a:visited{
 text-decoration: none;
 border: none;
 color: #888;
 transition: color .3s;
}


.footer-nav li a:hover ,.footer-nav li a:active{
  color: #ddd;

}

.socilmedia-link li a:link,.socilmedia-link  li a:visited{
 font-size: 160%;
 
}

```


![](https://i.imgur.com/A2Z6Meh.png)


```css
footer p{
  color:#888;
  text-align: center;
  
  margin-top: 20px;
}

.logo-instagram,
.logo-google,
.logo-twitte,
.logo-facebook{
transition: color .3s;
}

.logo-instagram:hover{
  color: #517fa4;
}
.logo-google:hover{
  color: #dd4b39;
}
.logo-twitte:hover{
  color: #00aced;
}
.logo-facebook:hover{
  color: #3b5998;
} 
```
![](https://i.imgur.com/bLtpdK7.png)




----

# 補充
## `background`與`background-imge`之差別
{% alert info no-icon %}
background属性是整體設置的，background image是background的属性之一，background的屬性包括
background-color
background-image
background-repeat
background-attachment
background-position，
你看不到圖片是因为設置錯，不能加no-repeat屬性，應改為：
background-image:url("some.jpg"); background-repeat:no-repeat;分開寫

[background和background-image什么区别](https://bbs.csdn.net/topics/310167508)
{% endalert %}

```css
.header{
background-image:url(./Omnifood\ Contents/hero.jpg) ;

background-size: cover;
background-position: center;
}
/********我改為下面這樣*******/
.header{
background: url(./Omnifood\ Contents/hero.jpg) no-repeat
 center center/cover;}
```
 
## 文字框置中（區塊（div）的垂直置中）
>pjchender
>> Position + Transform（常用）:先利用 position: absolute 把 top: 50% 設在 50%；接著再透過 transform: translateY(-50%) 把超過的部分修正回來，達到垂直置中。

```css=
.hero-text-box{
  display: flex;
  width: 1140px;
  height: 100%;
  flex-direction: column; 
  
   align-items: center; 
  justify-content: center;
} 
```

參考：
[pjchender](https://pjchender.blogspot.com/2015/04/css_15.html)



## 使用`button` vs `link`
[Button versus Link](https://a11y-101.com/design/button-vs-link)
{% alert info no-icon %}
From a semantics and accessibility viewpoint:
Links ``(<a>) ``are for "going someplace" such as "jumping to a different section of a page, going to another URL, etc.
Whereas
Buttons ``<button> ``are for "doing something" such as a function on the page to expand/collapse a menu, submit a form or etc.
{% endalert %}
## `grid`參考資料
[Spectacularly Easy Responsive Design](http://www.responsivegridsystem.com/)
[關於 Grid Layout 的使用姿勢](https://blog.hinablue.me/css-grid-layout/)
[六角學院 - Bootstrap Grid System 原理介紹](https://www.youtube.com/watch?v=uZRkVOeRHws)

### `margin` 與 `paggimg`

製作steps區塊時，因設定section的margin距離
使得想在此區塊多加上背景色彩，有了中間的空白處
```css
.meal-steps{
  background: #f4f4f4;
}
```
* 這裏應該改為padding
```css
section{
  padding: 80px 0; 
  /*margin: 80px 0;*/
}
```

![](https://i.imgur.com/ss2XfZd.png)


### 練習後的小心得：
1.在html建構會使用`.row`
2.應用grid,css
3.內部，字的大小用百分比
4.有一些可以共用的區塊，可以放在reuse區塊，並設一樣的class名稱，在建構時，可以更快速


