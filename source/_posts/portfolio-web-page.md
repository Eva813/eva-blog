---
title: css練習- portfolio web page 作品集網頁
date:  2020-12-13 13:48:00
tags: ["udemy","css","grid"]
---

## 介紹 
[成品](https://eva813.github.io/Eva_portfolio/portfolio01/portfolio01.html)
* 這是一個展現作品集的一頁式網頁
    * 頁面的排版主要使用grid執行
    * 網頁整的色調，練習`:root `設定
    * 練習聯絡欄的排版
* 另外，有使用到一些些js的部分，如設定滾動的效果、點擊後畫面的變化，但因主要練習排版，所以筆記內容並未多加提及

![](https://i.imgur.com/gpZLEE8.png)

##  HEADER
```html
 <!--===== HEADER =====-->
  <header class="l-header">


    <nav class="nav bd-grid">
      <div>
        <a href="#" class="nav__logo">Jhon Doe</a>
      </div>
      <div class="nav_manue" id="nav-menu">
        <ul class="nav_list">
          <li class="nav_item"><a href="#home" class="nav_link">Home</a></li>
          <li class="nav_item"><a href="#abour" class="nav_link">About</a></li>
          <li class="nav_item"><a href="#skills" class="nav_link">Skills</a></li>
          <li class="nav_item"><a href="#portfolio" class="nav_link">Portfolio</a></li>
          <li class="nav_item"><a href="#contact" class="nav_link">Contact</a></li>

        </ul>

      </div>
      <div class="nav__toggle" id="nav-toggle">
        <i class='bx bx-menu'></i>
      </div>
    </nav>

  </header>
```
#### header的建構
![](https://i.imgur.com/NPyM8sR.png)


### css 變數


```css
/*建立全域變數*/
:root {
	--csscoke-red: #f00;
}

/*套用變數*/
.section-item {
	color: var( --csscoke-red );
}
```
> csscoke
 `:root `中使用關鍵符號 --建立了一個 CSS 變數，並且給予該變數名稱叫做 csscoke-red ，然後給予這個變數名稱一個值 `#f00`，這表示後面要套用這個變數名稱的項目，會直接取得這個 `#f00 `。
 
參考資料：[:root 根目錄選取器 - 叫你阿爸出來講](https://ithelp.ithome.com.tw/articles/10228111)


### grid-template-columns

```css
.bd-grid{
    max-width: 1024px;
    display: grid;
    grid-template-columns: 100%;
    grid-column-gap: 2rem;
    width: calc(100% - 2rem);
    margin-left: var(--mb-2);
    margin-right: var(--mb-2);
}
```
>卡伯斯
>grid-template-columns 定義水平方向的空間，grid-template-rows 定義垂直方向的空間
> `.wrap {
  grid-template-columns: repeat(2, 1fr 2fr) 100px;     
  /* grid-template-columns: repeat({次數}, {格線...} | {格線...}) | {格線...}; */
} `
> fr 這個單位，這個單位能夠將可用的 剩餘空間 做比例分割，以上面的 1fr 2fr 為例，空間將被分割成 1/3、2/3 兩個大小。
另一個是 repeat，可以重複隔線。

[CSS Grid 屬性介紹](https://wcc723.github.io/css/2017/03/22/css-grid-layout/)
[一行 CSS 程式碼搞定響應式佈局](https://www.mdeditor.tw/pl/pn1h/zh-tw)
[CSS沒有極限 - CSS的神奇Calc運算](https://ithelp.ithome.com.tw/articles/10130065)




# home

![](https://i.imgur.com/ngnNmzl.png)


```css
.home__scroll{
    align-self: flex-end;
    padding-bottom: var(--mb-4);
}

/* 直行旋轉 */

.home__scroll-link{
    writing-mode: vertical-lr;
    transform: rotate(-180deg);
    color: var(--white-color);
}
```

![](https://i.imgur.com/we7sEnu.png)



# skills

![](https://i.imgur.com/0Nsj8gH.png)

```html
<!--===== SKILLS =====-->
    <section class="skills section" id="skills">
      <h2 class="section-title">Skills</h2>

      <div class="skills__container bd-grid">
        <div class="skills__box">
          <h3 class="skills__subtitle">Development</h3>
          <span class="skills__name">Html</span>
          <span class="skills__name">Css</span>
          <span class="skills__name">Javascript</span>
          <span class="skills__name">Scss</span>
          <span class="skills__name">React</span>
          <span class="skills__name">Vue</span>

          <h3 class="skills__subtitle">Design</h3>
          <span class="skills__name">Figma</span>
          <span class="skills__name">Adobe XD</span>
          <span class="skills__name">Photoshop</span>
        </div>

        <div class="skills__img">
          <img src="./img/skill.jpg" alt="">
        </div>
      </div>
    </section>
```

`.skills__container{
    row-gap: 2rem;
} 使欄位2rem的空格`
>rem 也是相對的文字尺寸，和 em 使用方法接近，不同的是他僅相對於 root 層級的文字大小(網頁中的 html)。
[如何更愉快地使用rem —— 別說你懂CSS相對單位](https://iter01.com/23690.html)
[實際展示 EM 與 REM 的差異](https://www.hexschool.com/2016/01/02/2016-08-08-em-vs-rem/)



```css
.skills__name{
    display: inline-block;
    font-size: var(--small-font-size);
    margin-right: var(--mb-2);
    margin-bottom: var(--mb-3);
    padding: .25rem .5rem;
    background-color: var(--white-color);
    border-radius: .25rem;
}
```

* 中間的技能名稱設定

![](https://i.imgur.com/YcJoUD6.png)


# contact 

![](https://i.imgur.com/FMyeUse.png)

* 輸入框

```css
/* name,email */
.contact__inputs{
    display: grid;
    grid-template-columns: repeat(2, 1fr);
     column-gap: 1rem; 
}

/* 文字輸入框 */

.contact__input{
    width: 100%;
    padding: .8rem;
    outline: none;
    border: 1.5px solid var(--dark-color);
    font-size: var(--normal-font-size);
    margin-bottom: var(--mb-4);
    border-radius: .5rem;
}
```



* 按鈕

```css

.contact__button{
    display: block;
    background-color: var(--first-color);
    color: var(--white-color);
    padding: .75rem 2.5rem;
    /* 若用margin-right:auto<會靠左邊 */
     margin-left: auto; 
    border-radius: .5rem;
    border: none;
    outline: none;
    font-size: var(--normal-font-size);
    cursor: pointer;
}
```




# footer

![](https://i.imgur.com/JH5KLYT.png)



---
## 參考資料：
### 1. z-index
[CSS position 、 z-index 筆記│鼠年全馬鐵人挑戰 #13](https://its-okay.medium.com/css-position-z-index-%E7%AD%86%E8%A8%98-%E9%BC%A0%E5%B9%B4%E5%85%A8%E9%A6%AC%E9%90%B5%E4%BA%BA%E6%8C%91%E6%88%B0-13-f7c4159ac5d)
[合理使用z-index数值](https://www.cnblogs.com/starof/p/4424926.html)

### 2. 滾動

點擊連結之後，自動滾動到適當的位置

[CSS scroll-behavior和JS scrollIntoView讓頁面滾動平滑](https://www.itread01.com/fyehkp.html

### 3. icon
![](https://i.imgur.com/vEVt8qF.png)

### 4. flex
[圖解 Flexbox 基本屬性
](https://cythilya.github.io/2017/04/04/flexbox-basics/)


## 練習來源：
youtube：[Responsive Personal Portfolio Website HTML CSS And JAVASCRIPT | Mobile First](https://www.youtube.com/watch?v=6cidbUHNZRQ&list=PL4ruhWbyIwi2vvT2y7j45egFSTwz9FHoJ&index=15&t=168s)
