---
title: "Amos金魚都能懂的切版教學影片- csscoke NO013"
date: 2020-11-28 19:54:27
tags: ["css","icon","box-sizing"]
---

[My page](https://eva813.github.io/my-projects/coke-NO013(v1).html)
![](https://i.imgur.com/BplwB2k.png)

## html架構
```html
<body>

  <div class="side-menu">
    <form action="">
      <input type="search">
      <button><i class="fa fa-search"></i></button>

    </form>
    <nav>

      <a href="#" id=""><i class="fa fa-users" aria-hidden="true"></i>鐵人賽</a>
      <a href="#" id=""><i class="fa fa-gavel" aria-hidden="true"></i>暴力班</a>
      <a href="#" id=""><i class="fa fa-object-group" aria-hidden="true"></i>RWD 醬子</a>
      <a href="#" id=""><i class="fa fa-globe" aria-hidden="true"></i>金魚網頁</a>
      <a href="#" id=""><i class="fa fa-cutlery" aria-hidden="true"></i>金魚切版</a>
    </nav>


  </div>

</body>

```

## .side-menu
* 設定寬度、背景色彩
* 利用flex排列，並設定直向
```css
  .side-menu {
      width: 300px;
      height: 100%;
      background-image: linear-gradient(0deg, #13547a, #80d0c7);
      display: flex;
      flex-direction: column;

```

## 側邊選單裡的選項（nav）

```css


   nav a {
      /* 讓a可以一行一行排列 */
      display: block;
      padding: 10px;
      text-decoration: none;
      color: #fff;
      position: relative;
    }


```
* ### 設定底線，利用＋篩選器

```css
nav a+a {
      border-top: 1px solid #fff;
    }
    
```
 * 使用此方式，會讓線滿版，無法單獨調整間距
![](https://i.imgur.com/HDrjfjS.png)


```css
nav a+a::before {
      content: '';
      position: absolute;
      border-top: 1px solid rgba(255, 255, 255, .4);
      left: 10px;
      right: 10px;
      top: 0;
    }
```

![](https://i.imgur.com/Vj3cZFy.png)


## form 搜尋表

![](https://i.imgur.com/JQygAWh.png)

* 讓inpt與button可以緊密並排

```css
.side-menu form {
      display: flex;
      margin: 0 10px 50px;}
```


![](https://i.imgur.com/EGk8tt6.png)

```css
 .side-menu form input,
    .side-menu form button {
      border: none;
      padding: 5px 10px;
      }
```

![](https://i.imgur.com/I6Ceugc.png)

* 讓form增加框線
```css=
 .side-menu form {
      
      /* 設定表單外框形狀有圓弧形 */
      border-radius: 100px;
      border: 1px solid rgba(255, 255, 255, .4);
    }
```

![](https://i.imgur.com/iTiqU93.png)

* 調整寬度
* 但要注意的是，如此調整後就蓋住了form的框線

```css
/* 寬度300，左右2邊有10px，剩下280，希望左邊選單大一點點 */


    .side-menu form input {
      width: 230px;
    }

    .side-menu form button {
      width: 50px;
    }
```


![](https://i.imgur.com/5nxR9Ru.png)


* 設背景顏色為透明

```css
.side-menu form input,
    .side-menu form button {
      border: none;
      padding: 5px 10px;

      /* 設定透明才會讓，form表單的框顯示清楚 */
      background-color: transparent;
      color: #fff;
    }

```
![](https://i.imgur.com/HpJUlbq.png)

* 取消focus的外框
![](https://i.imgur.com/jEv9f9o.png)

```css
.side-menu form input:focus,
    .side-menu form button:focus {
      outline: none;
    }
```

![](https://i.imgur.com/NrGoGVN.png)


### 動畫
```css
 /* 動畫：讓icon消失，文字左縮 */
    nav a .fa {
      margin-right: -1.1em;
      transform: scale(0);
      transition: .3s;
    }

    nav a:hover .fa {
      margin-right: 0em;
      transform: scale(1);
    }`
```

