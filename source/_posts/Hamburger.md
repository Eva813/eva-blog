---
title: css練習- Hamburger 漢堡表單
date: 2020-12-13 14:08:58
tags: ["udemy","css","transform"]
---

## 介紹
[成品](https://eva813.github.io/Eva_portfolio/hamburger-menu/hamburger-menu.html)
運用css可以做出許多意想不到的效果，有立體的呈現、動畫的改變等等...
* 漢堡表單的製作，以及點選時頁面的跳轉、動畫的改變
* nav按鈕，立體翻轉
* 中間icon圖示的排版，以及旋轉的效果

![](https://i.imgur.com/PYZZI5K.png)

## banner區的架構

```html
<div class="banner">
            <div class="logo">
                <a href="#"><img src="./hamburger-images/logo.png" alt=""></a>
            </div>
            <div class="btn-wrapper">
                <button class="banner-btn">Explore More</button>
            </div>
        </div>
```

* ### 導覽列

```html
<nav class="top-nav">
                <ul class="nav-list">
                    <li>
                        <a href="#" class="nav-link" data-text='Home'>Home</a>
                    </li>
                    <li>
                        <a href="#" class="nav-link" data-text='About As'>About As</a>
                    </li>
                    <li>
                        <a href="#" class="nav-link" data-text='Our Team'>
                            Our Team
                        </a>
                    </li>
                    <li>
                        <a href="#" class="nav-link" data-text='Services'>
                            Services
                        </a>
                    </li>

                    <li>
                        <a href="#" class="nav-link" data-text='Contanct'>
                            Contanct
                        </a>
                    </li>


                </ul>
            </nav>
```


![](https://i.imgur.com/SFLZx31.png)



* ### 加入社群媒體icon

```html
<nav class="bottom-nav">
                <ul class="icons">
                    <li class="icon-item">
                        <a href="#" class="icon-link"><i class="fab fa-facebook-f"></i></a>
                    </li>
                    <li class="icon-item">
                        <a href="#" class="icon-link"><i class="fab fa-youtube"></i></a>
                    </li>
                    <li class="icon-item">
                        <a href="#" class="icon-link"><i class="fab fa-twitter"></i></a>
                    </li>
                    <li class="icon-item">
                        <a href="#" class="icon-link"><i class="fab fa-google-plus-g"></i></a>
                    </li>
                </ul>
            </nav>
```

![](https://i.imgur.com/DDKx6sq.png)



* ### 先讓nav列hidden起來
    * 再html的class後面，增加hidden，瀏覽器就不會顯示該隱藏的部分

```html

<nav class="top-nav" hidden>
                <ul class="nav-list">
                    <li>
                        <a href="#" class="nav-link">Home</a>
                    </li>
                    <li>
                        <a href="#" class="nav-link">About As</a>
                    </li>
                    <li>
                        <a href="#" class="nav-lik">
                            Our Team
                        </a>
                    </li>
                    <li>
                        <a href="#" class="nav-link">
                            Services
                        </a>
                    </li>

                    <li>
                        <a href="#" class="nav-link">
                            Contanct
                        </a>
                    </li>


                </ul>
            </nav>

        </div>


```

![](https://i.imgur.com/UdQIyRA.png)

* 讓兩個a連結的nav都hidden


# 右上選單
先製作三個`div`
![](https://i.imgur.com/vLXU6ny.png)

```html
<div class="hamburger-menu">
                <div class="line line-1"></div>
                <div class="line line-2"></div>
                <div class="line line-3"></div>
            </div>
```

* ### 在hamburger-menu這裏的寬度、高度、位置先設定好
* ### 針對div的line設定
```css
.hamburger-menu{
  width: 35px;
  height: 30px;
  background-color: #fff;
  position: fixed;
  top: 40px;
  right: 50px;
}

.line{
  width: inherit;
  height: 5px;
  
  background-color: #16c3cf;
  border-radius: 25px;
}
.line-2{
  background-color: #000;
}
```

* 他是三個div組合的
* 這裏故意把2設為黑色

![](https://i.imgur.com/62XCqGg.png)


* 讓他分三條線可以分散對齊
* 把白色去掉
```css

.hamburger-menu{
 display: flex;
  flex-direction: column;
  justify-content: space-between;
  cursor: pointer;
}

```
![](https://i.imgur.com/yPLLtSs.png)

---

# 3D按鈕

```css
/* 3d按鈕 */
.banner-btn{
  width: 350px;
  height: 100px;
  background: linear-gradient(#16c3cf,#156459);
}
```

![](https://i.imgur.com/hs2ZbW8.png)

* `text-shadow: 0 10px 10px #000;`

### 參考資料：
* #### 3D `.btn-wrapper->perspective: 1000px;` `.banner-btn -> transform-style: preserve-3d;`
* [變形(transform) 3D基本使用](https://eyesofkids.gitbooks.io/css3/content/contents/transform3d.html)
 [w3schools](https://www.w3schools.com/css/css3_3dtransforms.asp)
 [3dtransforms](https://3dtransforms.desandro.com/3d-transform-functions)
 [CSS 實做 3D 立體正方形](https://www.puritys.me/docs-blog/article-354-CSS-%E5%AF%A6%E5%81%9A-3D-%E7%AB%8B%E9%AB%94%E6%AD%A3%E6%96%B9%E5%BD%A2.html)
 [CSS 3D 旋轉 rotate3d 與 translate3d](https://www.puritys.me/docs-blog/article-353-CSS-3D-%E6%97%8B%E8%BD%89-rotate3d-%E8%88%87-translate3d.html)


:::success
我們會在要進行3D變形的外層容器元素來定義perspective，這樣包含在其內的子元素(物件)都可以按照這個深度來進行變形。
perspective設定為300px或更少時，會有強烈的失真，500px到1000px的失真較為中等，2000px以上的失真就很輕微。
:::

* ### 要在該按鈕前面增加一個偽元素


```css=
.banner-btn::before{
  content: '';
  width: 100%;
  height: 15px;
  background-color: red;
  position: absolute;

}
```

![](https://i.imgur.com/uRCxqlS.png)

```css
.banner-btn::before{
  content: '';
  width: 100%;
  height: 15px;
  background-color: #156469;
  position: absolute;
  bottom: 0px;
 right: 0px;

  transform: rotateX(90deg);  /*會使他消失 */

  transform-origin:bottom ;

}
```

![](https://i.imgur.com/eAfVcwr.png)

* `` transform-style: preserve-3d;``

![](https://i.imgur.com/NxcSBfC.png)

* `transform-origin:bottom ;`


![](https://i.imgur.com/pDZA4Mf.png)

* `.banner-btn::after`

![](https://i.imgur.com/loDXaL2.png)

```css
.banner-btn::after{
  content: '';
  width: 15px;
  height: 100%;
  background-color: #16c3cf;
  position: absolute;
  top: 0px;
 right: 0px;

  transform: rotateY(-90deg);  /*會使他消失 */

  transform-origin:right;

}
```

![](https://i.imgur.com/zG40Jfg.png)

---

# nav列


* z-index:100
![](https://i.imgur.com/IpB3rfs.png)

![](https://i.imgur.com/copdVTv.png)


* ### 在上層導覽列，先設定灰色的背景，以方便看清楚大小
* ### 使用flex排列

```css
.nav-list{
  background-color: #ccc;
list-style: none;
width: 80%;
margin: auto;
height: inherit;

/* 使他水平 */

display: flex;
justify-content: space-evenly;
align-items:center;

}
```
![](https://i.imgur.com/z2eFyRV.png)


## 上層導覽列，連結的3d按鈕製作

* 在他們的html 設定data-text
```htmlembedded=
<li> <a href="#" class="nav-link" data-text='Home'>Home</a> </li>
                   
```

```css
.nav-link::after{
 content: attr(data-text);
 position: absolute;
 left:0;
 bottom:-100%;
 background-color: #000;
 padding: inherit;
}
```



![](https://i.imgur.com/5Qw1iCD.png)

* 將after創造出的rotate到父層元素的下方

* #### `transform: rotateX(-90deg);`
```css
.nav-link:hover{
  transform: rotateX(90deg);
}
```

![](https://i.imgur.com/dpyaO6e.png)


### 位置不對：
![](https://i.imgur.com/auUp49u.png)
* transform-origin: top;

![](https://i.imgur.com/LpBcL0D.png)

---

# icon button


![](https://i.imgur.com/rv84ID4.png)

```css
.icons{
  position: absolute;
  /* 父層已經有設定fixed */
 top: 50%;
 left: 50%;
 transform: translate(-50%,-50%);
```

![](https://i.imgur.com/cXJ8VVD.png)

![](https://i.imgur.com/ieKQMjU.png)

```css
.icons{
display: flex;

 width: 60%;
 justify-content: space-between;

}
```

![](https://i.imgur.com/FjGcoHj.png)



![](https://i.imgur.com/LG9TVCS.png)
* #### 上面因為原本為inline元素， `display:block` 或`display:flex`

![](https://i.imgur.com/expCFZr.png)

* #### 要使icon在中間，使用flex

```css
.icon-link{
  width: 200px;
  height: 200px;

  border:8px solid #fff ;

  display: flex;
  justify-content:center;
  align-items: center;
  
  text-decoration: none;

border-radius: 5px; }

```

![](https://i.imgur.com/1LaJiu4.png)



![](https://i.imgur.com/iZsXFCJ.png)

* 調整height

![](https://i.imgur.com/ej932dL.png)

```css
.icon-link::before{
content: '';
height:25px ;
width: 5px;
background-color: #fff;
position: absolute;
top: 0;


} 

 .icon-link::after{
content: '';
height:25px ;
width: 5px;
background-color: #fff;
position: absolute;
bottom: 0;


} 
```

![](https://i.imgur.com/Yuri7rC.png)

## 背景顏色的動畫

#### 1.在icon後面增設背景色彩

```css
.icon-item:nth-child(1) .icon-link i::after{
  background-color: #3b5999;
}

.icon-item:nth-child(2) .icon-link i::after{
  background-color: #cd201f;
}

.icon-item:nth-child(3) .icon-link i::after{
  background-color: #55acee;
}



.icon-item:nth-child(4) .icon-link i::after{
  background-color: #dd4b39;
}
```

#### 2.設定hover之後，位置移動到原本icon的位置

```css
/* 讓顏色回到原來的位置 */
.icon-link:hover i::after{
  top: 0;
  right: 0;
  border-radius: 0;

}
```



![](https://i.imgur.com/cdJuIsW.png)

#### 3.讓圓形色彩隱藏

```css
.icon-link i{
over-flow:hidden;
}
```


![](https://i.imgur.com/tqPlffY.png)


# 漢堡的動畫

* line-1,line-2,line-3 位置改變的設定
* 這裏使用js，增加change的class名稱
![](https://i.imgur.com/UNGeilF.png)

![](https://i.imgur.com/trwX0C8.png)


```css
.change .line-1{
  transform: rotateZ(-45deg);
}

.change .line-2{
  transform: translate(5px,20px);
}

.change .line-3{
transform: rotateZ(45deg);
}

```

```javascript
document.querySelector('.hamburger-menu').addEventListener('click', () => {
  document.querySelector('.nav-wrapper').classList.toggle('change')
})
```

---

# resposive

* 針對螢幕大小，來設定內容為的寬、高與字型大小

[Day22：小事之 Media Query](https://ithelp.ithome.com.tw/articles/10196578)

[響應式網站設計基本觀念(2)：CSS媒體查詢(CSS Media Queries)](https://medium.com/%E7%8B%90%E7%8B%B8%E7%9A%84%E8%91%A1%E8%90%84%E9%85%92/css%E5%AA%92%E9%AB%94%E6%9F%A5%E8%A9%A2-css-media-queries-357e2b03b3eb)

---

