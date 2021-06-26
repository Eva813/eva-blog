---
title: CSS 預處理器- Sass/SCSS 基本介紹
date: 2021-03-05 09:41:46
tags: ["Sass","drag&drop"]
---

## 前言
CSS預處理器，增加了一些編寫的特性，也可以說是運用程式化的方式寫CSS。
* 其中SASS最廣泛使用：Sass包含SASS與SCSS兩種
    * 兩者之間不同之處有以下兩點：
    * 1.文件擴展名不同，Sass 是以“.sass”後綴為擴展名，而SCSS 是以“.scss”後綴為擴展名
    * 2.語法書寫方式不同，Sass是以嚴格的縮進式語法規則來書寫，不帶大括號({})和分號(;)，而SCSS的語法書寫和我們的CSS語法書寫方式非常類似。
## 安裝sass
[sass-github](https://github.com/sass/sass)
* 在專案底下，安裝
```javascript
npm install -g sass
```
* 安裝後，建立`index.scss`檔案
 ![](https://i.imgur.com/cQtE8T5.png)
* 手動轉擋
 ![](https://i.imgur.com/6TYjGnV.png)
 * 會出現css檔案
![](https://i.imgur.com/SHoaMLN.png)

## 使用node-sass編譯
1. 先取得package.json檔案
 安裝語法：`npm init --yes`
  (要注意安裝的路徑位置)
![](https://i.imgur.com/f9QFcH0.png)
![](https://i.imgur.com/4B623eU.png)
2. 安裝node-sass
* `npm i -g node-sass`  (i -> install)
![](https://i.imgur.com/vgGzk85.png)
* 查尋安裝是否完成
![](https://i.imgur.com/EdeoV1H.png)
3. 將檔案編譯為css
 建立css(資料夾)
### 使用watch，使每次修改sass檔案時，會自動編譯到css中
* 在package.jason檔案中的`"scripts"`
* 要注意編譯資料的位置
  * css :為資料夾路徑 
  * scss/main.scss:建立的css路徑

```javascript
 "scripts": {
    "watch": "node-sass -o css scss/main.scss -w"
  }
```
* 在終端機要輸入
`npm run watch`，`control+c`退出
![](https://i.imgur.com/KNReHQL.png)

## 基本語法實作
###  變數
* 在變數前加上`$`，來宣告要使用的變數
* 運用抽取變數方式，使運用版面顏色時，可以有一致性，修改版面時也較快速
* 變數：數量不要超過10個，自己在記憶上會混亂
```scss
// 創造顏色、字大小的變數
$white:#fff;
$black:#000;
.button{
text-align:center;
background-color:$black;
border:1px solid $black;
color:$white;
}

$font-lg:40px;
$font-md:30px;
$font-sm:20px;

.footer h3{
  font-size: $font-md;
  color: $color-secondary;
  text-align: center;

}

```
#### scope of sass variable
* 一般常見屬於global scope
* 如果將變數設在，標間架構中->local scope
![](https://i.imgur.com/oAeBzah.png)
* 在後面加上`！global`就變成全域
![](https://i.imgur.com/jbDV03A.png)

{%note info%}
還是建議以global 的方式進行修改，才不會造成維護的混亂
{%endnote%}
* 命名中使用`-`以及`_`，在sass中是一樣的
    * 如`$font-lg`改為`$font_lg`一樣也會有效

### Nested 巢狀
#### 1.針對nav下的`ul`、`li`

```scss
.nav{
  background-color: $color-primary;
  ul li{
    list-style: none;
  }
  a{
    text-decoration: none;
    font-size: $font-sm;
    color: $color-secondary;
  }
}
```
* 轉換為css
```css
.nav {
  background-color: orange; }
  .nav ul li {
    list-style: none; }
  .nav a {
    text-decoration: none;
    font-size: 20px;
    color: gray; }
```
![](https://i.imgur.com/wQXDhMZ.png)

#### 2.增加hover的效果
 * `&`，來指出`parent element`：在這便是指`<a>`

```scss
.nav{
  background-color: $color-primary;
  ul li{
    list-style: none;
  }
  a{
    text-decoration: none;
    font-size: $font-sm;
    color: $color-secondary;
    &:hover{
      color: $color-tertiary;
    }
  }
}
```
* 轉換為css
```css
.nav {
  background-color: orange; }
  .nav ul li {
    list-style: none; }
  .nav a {
    text-decoration: none;
    font-size: 20px;
    color: gray; }
    .nav a:hover {
      color: royalblue; }

```

![](https://i.imgur.com/IL4dwvL.png)

{%note info%}
巢狀層級不可以太多層，否則會出現效率問題
{%endnote%}

### @import 是什麼
* import 讓我們可以拉出特定區塊或是根據功能性不同拆成不同區塊，來撰寫scss，並利用@import放入主要的scss檔案，最後彙整到同一個css之中。
* 再用一個css引入(@import)，各個功能
* 要引入的檔案命名：在要import的檔案要加`_`

>六角學院
>![](https://i.imgur.com/SRD31V7.png)

`_test.scss`為撰寫footer樣式的檔案


```scss
.footer h3{
  font-style:italic ;
  border: blue 3px solid;
}
```
`main.scss` 要引入檔案的主要檔案
```scss
@import "test";
```
轉譯為css(main.css)
```css=
.footer h3 {
  font-style: italic;
  border: blue 3px solid; }
```


## @mixin 與 @include
* 當在多個地方想使用同一種style的方式。可以組成一組的樣式，並於scss中重複使用。
* 以 ``@mixin`` 開頭，並自定義一個名稱
* 需要它的時候就用 `@include`呼叫
1. 設定對齊方式
```scss
@mixin horizontal-list{
 li{
 display:inline-block;
 margin:{
   left:-2px;
   right:2em;
 }
 }
}

/*當要引用時*/
nav ul{
  @include horizontal-list
}

```
2. 調整字體大小（以帶參數的方式）
```scss
// 發現h1,h3樣式一樣
@mixin headingStyles($fontSize){
  //以代參數方式來放入不同字體大小的變數設定
  font-size: $fontSize;
 color: $color-secondary;
  text-align: center;
} 


.banner h1{
 
 @include headingStyles($font-lg);
}
.footer h3{

  @include headingStyles($font-md);
}
```
* 在css的地方，可以看到字的大小不同
```css
.banner h1 {
  font-size: 40px;
  color: gray;
  text-align: center; }

.footer h3 {
  font-size: 30px;
  color: gray;
  text-align: center; }

```
3. 加入動畫效果

* 在`footer h3`加上`hover`
* `@mixin transition($param...)`加上設定參數特性
    * 當要同時設定特性中的多個值時，要記得在參數後面加上`...`

```scss
// 發現h1,h3樣式一樣
@mixin headingStyles($fontSize){
  //以代參數方式來放入不同字體大小的變數設定
  font-size: $fontSize;
 color: $color-secondary;
  text-align: center;
} 

@mixin transition($param...) {
  transition: $param;
}
.banner h1{
 
 @include headingStyles($font-lg);
}
.footer h3{

  @include headingStyles($font-md);
  @include transition(color .5s ,background-color 1s);
  &:hover{
    color:$color-tertiary;
    background-color: $color-primary;
  }
}
```
* 轉譯為css
```css
.footer h3 {
  font-size: 30px;
  color: gray;
  text-align: center;
  transition: color 0.5s, background-color 1s; }
  .footer h3:hover {
    color: royalblue;
    background-color: orange; }
```




[Day27：小事之 Transition 與 Animation](https://ithelp.ithome.com.tw/articles/10197303)
[CSS3 Animation](https://cythilya.github.io/2017/08/27/css-animation/#transition-%E6%BC%B8%E8%AE%8A)


[新手也可以輕鬆玩轉 SASS - @mixin and @include](https://5xruby.tw/posts/play-sass-mixin-and-include)

### Functions
* 透過函式的建立，可以在需要使用時，不斷地呼叫使用
* `@function fontSize($size){
   @return $size*2; }`建立函式

```scss
@function fontSize($size){
   @return $size*2;
}


 h1{


.banner p{
  font-size: fontSize($font-sm);
  //字的大小會變成 20px*2 =>40px
}

```

轉譯為css
```css
.banner p {
  font-size: 40px; }
```
* 將文字大小，預先設定預設值

```scss
//設定預設為25px
@function fontSize($size:25px){
   @return $size*2;
}



.footer p{
  font-size: fontSize();
}
```
轉譯為css
```css
.footer p {
  font-size: 50px; }
  /* 25*2=>50 */
```
* 若插入數字

```scss
.footer p{
  font-size: fontSize(30px);
}

```
* 轉譯為css
```css
.footer p {
  font-size: 60px; }
  /* 30*2=>50 */
```
### 內建函式介紹
1. `lighten()`
```sass=
.nav{
  background-color: lighten($color-primary,20%);}
```
css
```css=
.nav {
  background-color: #ffc966; }
```
* 顏色變淡
![](https://i.imgur.com/WhTLwso.png)
2. `darken()`
```scss
.nav{
  background-color: darken($color-primary,20%);}
```
* 編譯為css
```css
.nav {
  background-color: #996300; }
```
![](https://i.imgur.com/LRzx1fH.png)


3.`transparentize()`
* 透明度從0-1 (1是完全透明)
```scss
.nav{
  background-color: transparentize($color-primary,0.6);}
```
* 編譯為css
```css
.nav {
  background-color: rgba(255, 165, 0, 0.4); }
```
![](https://i.imgur.com/6ON5Rfk.png)
4.`mix()`
* 將顏色混合
```scss
.nav{
   background-color: mix(blue,green);}
```
css
```css
.nav {
  background-color: #004080; }
```
![](https://i.imgur.com/M9sFOr6.png)

#### 加入百分比（利用百分比來調配顏色的比重）
* `mix(blue,green,10%)`表示只有10%的藍色，90%為綠色
```scss
.nav{
   background-color: mix(blue,green,10%);}
```
css
```css
.nav {
  background-color: #00731a; }
```
![](https://i.imgur.com/TdUkHSn.png)
---
## 補充scss檔案編制
* 主要視自己專案上的區分來編制
vendor:外部套件，放置bootstrap或其他frameworks、liberaries、hover.css
utils(helpers):工具類的class，放置字的大小、margin、border顏色設置、hover效果
partials:放置一些部分區域的css，如：nav、footer; layout: （共通的佈局）如表頭、表尾，就是每頁都會出現
而這幾個檔案，放在main.scss要有順序
![](https://i.imgur.com/tjRLJjK.png)
base:p段落、h1..等的全站設定(css reset)
components ：元件，如按鈕、卡片的設定
---
參考資料：[alphacamp-Sass/SCSS 基本語法介紹，搞懂CSS 預處理器](https://tw.alphacamp.co/blog/css-preprocessor-sass-scss)
