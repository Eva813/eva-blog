---
title: SCSS- ClothesStore
date: 2021-03-05 14:26:19
tags: ["SCSS","grid"]
---

[成品](https://eva813.github.io/Eva_portfolio/sass-ClothesStore/ClothesStore.html)
## scss 檔案的建立
所建立的各分檔，個別會負責不同的區塊，並透過`@import`放入主要的sass檔
* main.scss 引入其他分檔
 ![](https://i.imgur.com/PLNNF5k.png)
* abstracts
    * /_variables.scss：設定常用的變數，如：顏色、字體大小
    * /_mixin.scss：設定某些區段中，重複用到的樣式，並於該區段要使用的時候引用
* base
    * /_base.scss：為設定大框架，如html、body、container，等主幹的大小、字型的設定
* components:放置部分功能元件
    * /_button.scss：按鈕的設定
    * /_heading.scss：標題
    * /_logo.scss
    * /_dropdown.scss
* layout （設計網頁版面配置(Layout)）
    * /_header.scss
    * /_navigation.scss
    * /_products.scss
    * /_slidshow.scss
    * /_footer.scss
* pages 通常是用來放置其他分頁（網頁內會連結到其他相關分頁）
* venders 放置外部套件


![](https://i.imgur.com/GmeHZ7w.png)

### (1)基礎設置

#### (1.1)Grid 練習
`_base.scss`
* 總共要設置10欄
* 列的則是設置4區塊
* 並設置每個區塊都有1.6rem的gap
```scss
.container{
    display: grid;
    grid-template-columns:minmax(6rem,1fr) repeat(8,minmax(min-content,16 rem))   minmax(6rem,1fr);//10欄
    grid-template-rows: repeat(4,min-content);
    grid-row-gap:1.6rem ;
   
}
```

![](https://i.imgur.com/QA3NLgt.png)

* `minmax(min, max)`
`minmax(6rem,1fr)`:設定格線最小6rem，最大1fr
fr 單位就是為了格線布局而生，fraction，中文意思是分數，當使用在網格軌道上時，可以直接想像成 "我的這個空位要佔有總 fr 數量的幾等份"
* `repeat(8,minmax(min-content,16rem))`:重複8個，最小：min-content;最大16rem
    * repeat() 重複網格線及間距
     * max-content/min-content 最小內容尺寸/最大內容尺寸
        * max-content：給予網格區域所需的最大尺寸空間，內容盡可能避免折行。
        * min-content：給予網格區域所需的最小尺寸空間，內容盡可能的折行，但不會溢出邊界。

[[Day19] grid-template 屬性之 rows/columns](https://ithelp.ithome.com.tw/articles/10248418)
[Min & Max Content Sizing in CSS Grid — 1/3 Flexibility](https://www.youtube.com/watch?v=lZ2JX_6SGNI)
[CSS Layout很難？用這招30秒就讓你輕鬆生出網頁版面！](http://csscoke.com/2013/10/17/css-layout-generator/)


---
#### (1.2)常用變數
* `` _variable.scss``map與function的搭配使用
* 設定不同的顏色，並設置函式，以利後續使用

```scss
$colors:(
  primary:#333,
  secondary:#ffe,
  tertiary:#f2f0f1,
  quaternary:#f5b149

);
//創造函式
//函式名稱為color代入參數為$color-name，使用map代入物件，最為回傳的結果
@function color($color-name){
  @return map-get($map: $colors, $key:$color-name )
}

$font-size:(
  xl:3rem,
  lg:2.5rem,
  md:2rem,
  sm:1.8rem,
  xs:1.6rem
);
@function size($size){
  @return map-get($map: $font-size, $key:$size )
}

```
---
### (2)`layout/_header.scss`
header的版面
* `grid-column: 1/-1; (1/11) grid-row: 1/2;`表示所佔的區隔
![](https://i.imgur.com/C1W2Urq.png)
* 而在header這區，在設置grid劃分區塊
    * 有9欄
    * 列設置3區塊
```scss
.header{
  grid-column: 1/-1; //1/11
  grid-row: 1/2;
  display: grid;
  grid-template-columns: 1fr repeat(7,minmax(min-content,16rem)) 1fr;
  grid-template-rows: repeat(3,min-content);
 grid-gap: 2rem;
}
```
* ` grid-column: 1/-1; //1/11  ; grid-row: 1/2;` [What's the difference between grid-column value 1/1 and 1/2?
](https://stackoverflow.com/questions/49005233/whats-the-difference-between-grid-column-value-1-1-and-1-2)

### (3)logo的大小設置
`_logo.scss`
* 注意：設立所設定的欄和列的區塊，是在header設置的grid底下
 ![](https://i.imgur.com/OEkTxyS.png)

```scss
.logo{
  grid-column: 1/2;
  grid-row: 1/2;
  padding: 1rem;
  //class="logo-img"
  &-img{  
    width: 10rem;
    height: 100%;
  }
}

```
### (4)heading
* 標題的位置一樣利用grid來設定
* 在文字的字體、大小、間距進行調整
`_heading.scss`
```scss
.heading{
  grid-column: 4/7;
  grid-row: 2/3;
  text-align: center;
  &-text{
    font-family: 'Great Vibes',cursive;
    font-size: size(xl)*2; //結果為6rem
    font-weight: lighter;
    letter-spacing: 0.5rem;
  }
}
```
![](https://i.imgur.com/JfkrqEb.png)

### (5)heading中的navigation
 `layout/_navigation.scss`
* `.first-nav`為放置右上角
* `.second-nav`為放置下方，中間的位置
```scss

.first-nav{ 
  //放在最後三欄
  grid-column: 7/10;
  grid-row: 1/2;
  @include navigation;
}
.second-nav{
  grid-column: 3/8;
  grid-row: 3/4;
  @include navigation;
}
.dropdown{
  display: none;
}


```

![](https://i.imgur.com/PWJoEoH.png)

* @mixin，創造navigstion共用樣式
    * 使navigation的li-list可以橫向分散排列
    * 設定hover效果，使游標摸到顏色變淺`lighten()`
```scss
//.first-nav-list
@mixin navigation{

  &-list{  
    display: flex;
    justify-content: space-around;
    align-items: center;
    height: 100%;
  }
  &-item{
    list-style: none;
  }
  &-link{
    color: color(primary);
    text-decoration: none;
    font-size: size(sm);
    transition: all .3s;

    &:hover{
      color: lighten(color(primary),40%);
    }
  }
}

```
![](https://i.imgur.com/t6KdpJ9.png)

### (6)下拉選項：dropdown list的箭頭符號
`components/_dropdown.scss`
* `font-family: 'Font Awesome 5 Free'`：把icon當作字體使用
[使用 fontAwesome 動態加入連結 icon](https://yuwensaf.github.io/6110beb1/)

```scss
.dropdown-li{
  position: relative;
 //創造下拉選單的箭頭符號
 &::after{
   font-family: 'Font Awesome 5 Free';
   content: '\f078';
   font-weight:bold ;
 }

}

```
![](https://i.imgur.com/4pg8oNR.png)

* 製作下拉式選單的三角形
    * 先利用border製作出上、下、左、右的三角
```scss
.dropdown{
 &::before{
    content: '';
    display: block;
    position: absolute;
    top: -1.5rem;
    left: 7.3rem;
    border-top: 15px solid red;
    border-right: 15px solid green;
    border-bottom: 15px solid orange;
    border-left: 15px solid blue;

  }
 }
```
![](https://i.imgur.com/chq5TM1.png)

* 調整上方各色的三角形，並留下要保留的一個三角形

```scss
  &::before{
    content: '';
    display: block;
    position: absolute;
    top: -1.5rem;
    left: 7.3rem;
    // border-top: 15px solid red;
    border-right: 15px solid transparent;
    border-bottom: 15px solid orange;
    border-left: 15px solid transparent;

  }
```
![](https://i.imgur.com/YhxKI4C.png)
* `right: -2.8rem;`:條整位置對齊正方形list
* `border-bottom: 15px solid darken(color(tertiary),5%);`：修改三角形的顏色，使整體為灰色

```scss
.dropdown{
  position: absolute;
  top: 3rem;
  right: -2.8rem; //使整個往右移動
  width: 10rem;
  background-color:darken(color(tertiary),5%);
  @include navigation;
  padding: 1rem;
  text-align: center;
  border-radius: 0.2rem;

  &::before{
    content: '';
    display: block;
    position: absolute;
    top: -1.5rem;
    left: 7.3rem;
    // border-top: 15px solid red;
    border-right: 15px solid transparent;
    border-bottom: 15px solid darken(color(tertiary),5%);
    border-left: 15px solid transparent;

  }
```

![](https://i.imgur.com/JMI2Zgc.png)

*  hover使下拉選單消失\出現
    * 建立游標摸到`.dropdown-li`，會顯示下拉選單
    * `visibility`、`opacity`
```scss
.dropdown-li{

  &:hover .dropdown{
  visibility: visible;
   opacity: 1;
}
}

.dropdown{
visibility: hidden;
   opacity: 0;
  transition: opicity .3s;
}
```

![](https://i.imgur.com/vY3cbuj.gif)

![](https://i.imgur.com/0zOvMTI.png)

## 使slideshow可以跳轉(如幻燈片效果)
* 使用`@keyframes`做出動畫位置
* Animation Duration(動畫整個完成一次的時間): (fade-in + visible) × images = duration.
    * 假設你要讓圖片花1秒fade-in，再讓他顯示1秒。而你共有3張圖片，所以總共所需時間為6秒。
* Animation Delay:(fade-in + visible) × (ordinal position − 1) = delay.
* Keyframes: 100 ÷ animation duration = percentage for 1 second.(100/20=5%)，5％為1秒，所以4秒大約是20％的位置
```scss
.slideshow{

 &-slide{
    //使圖片、文字都放在同一個位置
  position: absolute;
   top: 0;
   left: 0;
   width: inherit;
   height: inherit;//預設先讓slide消失

   visibility: hidden;
    opacity: 0;
   animation: slideshow 20s linear infinite ;
   }

}

@keyframes slideshow{ 

  //主要是0~20時圖片顯示，之後都消失，讓其他圖片得宜顯示
  //0~2%創造fade的效果
  0%{
    visibility: hidden;
    opacity: 0;
  };
  2%{
    visibility: visible;
    opacity: 1;
  }
  //2%~18% slide要顯示
  18%{
    visibility: visible;
    opacity: 1;
  }
  //18~20要消失
  20%{
    visibility: hidden;
    opacity: 0;

  }
  100%{
    visibility: hidden;
    opacity: 0;
  }

}
```
[Simplest CSS Slideshow](https://snook.ca/archives/html_and_css/simplest-css-slideshow)
[CSS3 Keyframes Animation](https://www.smashingmagazine.com/2012/04/pure-css3-cycling-slideshow/#4)
[[筆記] CSS動畫Animation --- @keyframes](https://pjchender.blogspot.com/2015/12/cssanimation-keyframes.html)
[完整解析 CSS 動畫 ( CSS Animation )](https://www.oxxostudio.tw/articles/201803/css-animation.html)

* 設置延遲時間(4秒)，使每張圖片呈現(css的撰寫方式)
    * `:nth-child()`選擇第幾張圖片
```css
/*設置延遲時間，讓每張slide呈現4秒替換*/
 .slideshow-slide:nth-child(1){
   animation-delay: 0s;
 }
 .slideshow-slide:nth-child(2){
   animation-delay: 4s;
 }
 .slideshow-slide:nth-child(3){
   animation-delay: 8s;
 }
 .slideshow-slide:nth-child(4){
   animation-delay: 12s;
 }
 .slideshow-slide:nth-child(5){
   animation-delay: 16s;
 }
```


* 將上方轉化為`@each`的寫法
    * 先創造一個列表`$animlist`，在each迴圈時要但進去的項目
    * 在each迴圈中`.slideshow-slide:nth-child`放入slide of number(取得列表中第一個值)，後面接著寫下要執行的動作`animation-delay: nth($item,2);`

```scss
///改寫為=>$animlist:第幾個 延遲秒數,
//#{nth($item,1)}: 1表示(2 4s)，第一個值=2
$animList:1 0s,2 4s,3 8s,4 12s, 5 16s;
@each $item in $animList {
   .slideshow-slide:nth-child(#{nth($item,1)}){
     animation-delay: nth($item,2);
     //2就是指秒數
   }
}
```
[Sass Tutorials #9 - Nth Function In Lists](https://www.youtube.com/watch?v=O4hPOMX8AaU)

## 產品區下方see more按鈕
* html part
    * 有三個`arrow-line`，於後續在scss中會運用此三個div製作箭頭符號
```html
 <div class="products-btn">
        <a href="#" class="btn">
          <div class="btn-bg">
            <div class="arrow">
              <div class="arrow-line arrow-line-1"></div>
              <div class="arrow-line arrow-line-2"></div>
              <div class="arrow-line arrow-line-3"></div>
            </div>
          </div>
          <span class="btn-text">
            See More
          </span>
        </a>
      </div>
```


* 創造arrow
` transform: rotateZ` :Z軸 (使其斜向旋轉45度)
```scss
 &-line {
      height: .2rem;
      background-color: color(secondary);
      position: absolute;
  }

    &-line-2 {
     
      width: 1rem;
      //透過旋轉線2,線3使其成為交叉
      transform: rotateZ(45deg);
    }

    &-line-3 {
     
      width: 1rem;
      transform: rotateZ(-45deg);
    }
```
![](https://i.imgur.com/s62AXMY.png)

`transform-origin去設定物件變形的起始點`

```scss
&-line {
      height: .2rem;
      background-color: color(secondary);
      position: absolute;
      //將x變成arrow > ;因預設原點是在center，所以須將原點拉到右邊，形成箭頭
      transform-origin:right ;
  }

```

![](https://i.imgur.com/wbRTSeb.png)

[CSS 属性篇(三)：transform-origin属性
](https://juejin.cn/post/6844903780937367565)

* 增加橫向直線

```scss
&-line-1{
      width: 2rem;
      transform: translateX(-0.7rem);
    }
```
![](https://i.imgur.com/sdIdcZI.png)

* 設置動畫，透過摸到箭頭，會延展
    * bg的的寬度進行延展
    

```scss

.btn{

&:hover .btn-bg{
    width: 18rem;
  }
  
  &-bg{
     width: 4rem;
     height: 4rem;
    background-color: color(primary) ;
    //使其變圓形
     border-radius: 5rem ;
    position: absolute;
    left: 0;
    transition: width .2s ;
  } 

  &-line-1{ 
 
      width: 0;
      transform: translateX(-0.7rem);
    }
    //* line-1預設看不到，透過hover出現
  }
```

![](https://i.imgur.com/vQjUp05.gif)

* 使see more 出現
    * 先修改see more的顏色
    * 即便改變顏色，還是會被bg覆蓋，所以要調整z-index
```scss
.btn{

  &:hover .btn-text{
    color: color(secondary);
   }
  
 
  &-text{
    font-size: size(xs);
    text-transform: uppercase;
    color: color(primary);
    z-index: 10;
  }



}
```
![](https://i.imgur.com/bo8TJLy.gif)

* 將箭頭向右移動，並呈現完整箭頭符號
    * 設定`.arrow`右移，並讓line-1得宜呈現並延展
```scss
.btn{

   &:hover .arrow{
    transform: translateX(1rem);
  }
   &:hover .arrow-line-1{
    width: 2rem;
  }

 .arrow{
    @include flexPosition;
    transition: transform .2s ;
    
    &-line-1{
      width: 0;
      transform: translateX(-0.7rem);
      transition: width .2s ;
      }
   
    }



}
```
![](https://i.imgur.com/9pptj0t.gif)

## 表尾區塊

![](https://i.imgur.com/yHeHkU8.png)
### (1)footer區塊的樣式
 `layout/_footer.scss`
* footer有三區，中間是表單，左右邊分別為資訊連結
```scss
.footer{


  grid-column: 1/-1;
  grid-row: 4/5;
  @include flexPosition(space-around);
  padding:5rem 0 ;
  border-top: .1rem solid color(primary);
}
```

![](https://i.imgur.com/tSWtPTW.png)

* 透過`＠mixin`一次修改2區的條例項目（`mixin_scss`）
    * 其中包含文字大小、樣式、hover效果
```scss
@mixin footerList {
  &-heading{
    font-size: size(lg);
    color:color(primary);
  }
  &-item{
    list-style: none;
    margin: 1rem 0;
  }
  &-link{
    font-size: size(xs);
    text-decoration: none;
    color: lighten(color(primary),15%);
    transition: color .2s ;

    &:hover{
      color:lighten(color(primary),35%)
    }
  }
}
```
![](https://i.imgur.com/X9BA5K0.gif)

### (2)社群icon

```scss

.social-icons{
    display: flex;
    justify-content: space-between;
    padding: 1rem;
    box-sizing: border-box;

    &-item{
      list-style: none;
    }
    &-link{
      text-decoration: none;
      width: 4rem;
      height: 4rem;
      border:.1rem solid color(primary);
     
    }


```
![](https://i.imgur.com/IZzM7eK.png)

*  `border-radius: 100%;`調整為圓形外框
*  a標籤的前放為i的圖示，所以flex的設置，是針對`social-icons-lik`
 ![](https://i.imgur.com/kd6jbyV.png)
* 利用`@each`來套入icon的顏色

```scss
&-link{
      text-decoration: none;
      width: 4rem;
      height: 4rem;
      border:.1rem solid color(primary);

      //修改icon成圓形
      // display: block;
      border-radius: 100%;
      @include flexPosition;

      i{
        font-size: size(md);
      }
    }
    
    
$socialMediaColors: 1 #3b5998, 2 #b31217, 3 #dc4e41, 4 #55acee, 5 #517fa4, 6 #0077b5;

@each $color in $socialMediaColors {
  .social-icons-item:nth-child(#{nth(($color),1)}) .social-icons-link{
    color:nth($color,2);
    border:.1rem solid nth($color,2);
  }
}    
```
* 轉為css
```css
.social-icons-item:nth-child(1) .social-icons-link {
  color: #3b5998;
  border: 0.1rem solid #3b5998; }

.social-icons-item:nth-child(2) .social-icons-link {
  color: #b31217;
  border: 0.1rem solid #b31217; }

.social-icons-item:nth-child(3) .social-icons-link {
  color: #dc4e41;
  border: 0.1rem solid #dc4e41; }

.social-icons-item:nth-child(4) .social-icons-link {
  color: #55acee;
  border: 0.1rem solid #55acee; }

.social-icons-item:nth-child(5) .social-icons-link {
  color: #517fa4;
  border: 0.1rem solid #517fa4; }

.social-icons-item:nth-child(6) .social-icons-link {
  color: #0077b5;
  border: 0.1rem solid #0077b5; }
```

![](https://i.imgur.com/8aWeLiw.png)
