---
title: "Amos金魚都能懂的切版教學影片-csscoke NO017"
date: 2020-11-27 18:28:58
tags: ["css","切版"]
---
[成品](https://eva813.github.io/my-projects/coke-NO017(v1).html)
![](https://i.imgur.com/h8XYPgY.png)

## 會員登入
製作會員登入頁面，使用form表單，製作標籤、輸入框，以及登入按鈕。
```html
<body>
  <div class="login">
    <form class="form" action="">
      <h2>會員登入</h2>
      <!-- 用群組來分帳號與密碼 -->
      <div class="group">
        <label for="user_id">帳號</label>
        <input type="text" name="" id="user_id">
        <!-- for,與input的名字設一樣，才能在點到label時，會直接把插入點帶進去 -->
      </div>
      <div class="grop">

        <label for="user_password">密碼</label>
        <input type="text" name="" id="user_password">

      </div>
      <div class="gtn-group">
        <button class="btn">登入</button>
        <button class="btn">取消</button>
      </div>
    </form>
  </div>




</body>
```
![](https://i.imgur.com/VtS2Juk.png)

## html,body 背景照片

要讓畫面整個垂直置中，首先需要讓畫面高度是夠高的！（預設的body,html是由內容所撐開，所以預設不是100%的高）


```css
 html,
    body {
      height: 100%;
    }
 body {
      background: url('/img/office.jpg') no-repeat
 center center/cover;
    }
```

![](https://i.imgur.com/2u1BBDa.jpg)


## 登入區

```css
.login {
      width: 600px;
      height: 400px;
      background-color: rgba(0, 0, 0, .5);
      border-radius: 10px;
      border: 10px solid #fff;
    }
```
![](https://i.imgur.com/IsNlLK5.jpg)

* 讓此login區置中於版面
    * 在body設display:flex

![](https://i.imgur.com/YdGWpUZ.jpg)

#### 陰影、模糊

```
box-shadow: x y blur spread color inset;
常用box-shadow多半用在區塊的光暈，來達成陰影的效果
```
{%note info%}
- x：水平位移
- y：垂直位移
- blur：模糊程度，預設為0
- spread：擴散程度，預設為0
- color：顏色
- inset：內陰影
{%endnote%}

##### 毛玻璃特效 (注意：這是透過瀏覽器在處理特效，會比較耗效能)
```
backdrop-filter: blur(5px);
```
更多相關...[純 CSS 毛玻璃特效 - backdrop-filter 屬性介紹](https://wcc723.github.io/development/2020/10/12/frosted-glass/),[Secret 18: 毛玻璃效果](https://ithelp.ithome.com.tw/articles/10208692)


![](https://i.imgur.com/tzdNwg3.png)


## 調整文字、排版細節

![](https://i.imgur.com/yYg2Oga.png)

```css
  .form input {
      /* 讓輸入框設定100%，就會撐開 */
      width: 100%;
    }
```
![](https://i.imgur.com/hyKUW8d.png)

* 使用line-height，讓輸入框的高度撐開

```css
.form input {
      /* 讓輸入框設定100%，就會撐開 */
      width: 100%;
      border: 1px solid #aaa;
      line-height: 3;
    }
```
![](https://i.imgur.com/HSsKhS5.png)

* 將帳號、密碼的間距拉開，以及h2的調整

```css
.form h2 {
      margin-bottom: 20px;
      border-bottom: 1px solid #fff;
      padding-bottom: 10px;


    }

    .form .group {
      margin-bottom: 20px;
    }

    .form label {
      line-height: 2;
    }

    .form input {
      /* 讓輸入框設定100%，就會撐開 */
      width: 100%;
      border: 1px solid #aaa;
      line-height: 3;
      border-radius: 5px;
    }
```


![](https://i.imgur.com/0621fhk.png)


## 按鈕的調整

* btn並排中建出現空隙，在父元素div上加上 font-size:0;
* .form .btn+.btn，選取到『緊跟在對象 A 同層後方的 B』


```css
.form .btn-group {
      font-size: 0px;
      /* 讓按鈕區塊與上方有些距離，首先將原本wrap，height: 400px;改為500px ，再調整按鈕的margin*/
      margin-top: 50px;
    }

    .form .btn {
      font-size: 20px;
      border-radius: 5px;
      border: none;
      background-color: #6ab4fe;
      width: 190px;
      padding: 10px 0;
      color: #fff;

    }

    .form .btn+.btn {
      margin-left: 15px;
    }
```

[Tutoria page](https://eva813.github.io/my-projects/coke-NO017(v2).html)

---

[w3schools](https://www.w3schools.com/css/css_form.asp)
[CSS 實現背景圖尺寸不隨瀏覽器縮放而變化](https://www.itread01.com/p/681002.html)
[應用CSS3 box-shadow複製圖型](https://medium.com/@savemuse/%E5%88%A9%E7%94%A8css-box-shadow%E8%A4%87%E8%A3%BD%E5%9C%96%E5%9E%8B-51e3da4431cf)
[css3_buttons](https://www.w3schools.com/css/css3_buttons.asp)
[input標籤和button放在一行寫:inline元素的空白間隙問題](https://www.itread01.com/p/647911.html)
[親代選取器之妹妹選取器與鞭炮串選取器
](https://ithelp.ithome.com.tw/articles/10220656)


