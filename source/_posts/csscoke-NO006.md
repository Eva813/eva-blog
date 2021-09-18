---
title: Amos金魚都能懂的切版教學影片-csscoke NO006
date: 2020-11-28 18:46:11
tags: ["CSS","flex","footer"]
---

[my_page](https://eva813.github.io/my-projects/coke-NO006(v1).html)
![](https://i.imgur.com/tYapgm2.png)

此切版主要是針對footer區域的切版練習，有四個欄位，最下方有copy right區域。

## 我製作時遇到的問題：

* copyright一直跟左邊的item並行，是因為container的範圍應該只要涵蓋四個item就好

![](https://i.imgur.com/sU1bBXb.png)

* 設定nav的display:flex;並讓設定方向為colum

![](https://i.imgur.com/CYzhwHq.png)

## HTML

```html
<body>
  <div class="main-footer">
    <div class="container">
      <div class="footer-item">
        <h4>關於暴力課程</h4>
        <nav>
          <a href="#"><i class="fa fa-angle-right"></i> 課程目標</a>
          <a href="#"><i class="fa fa-angle-right"></i> 辦學理念</a>
          <a href="#"><i class="fa fa-angle-right"></i> 課程宗旨</a>
        </nav>
      </div>
      <div class="footer-item">
        <h4>課程列表</h4>
        <nav>
          <a href="#"><i class="fa fa-angle-right"></i> 暴力網頁入門班</a>
          <a href="#"><i class="fa fa-angle-right"></i> RWD網頁深入理解</a>
          <a href="#"><i class="fa fa-angle-right"></i> CSS3互動動畫設計</a>
          <a href="#"><i class="fa fa-angle-right"></i> Bootstrap框架實務</a>
        </nav>
      </div>
      <div class="footer-item">
        <h4>服務項目</h4>
        <nav>
          <a href="#"><i class="fa fa-angle-right"></i> 網站建置顧問</a>
          <a href="#"><i class="fa fa-angle-right"></i> 網站設計建置</a>
          <a href="#"><i class="fa fa-angle-right"></i> 網站規劃</a>
          <a href="#"><i class="fa fa-angle-right"></i> 企業教育訓練</a>
        </nav>
      </div>
      <div class="footer-item footer-subs">
        <h4>訂閱電子報</h4>
        <form>
          <input type="text" name="">
          <input type="submit" value="訂閱">
        </form>
      </div>
    </div>
    <div class="copyright">
      Copyright &copy; 2019 金魚都能懂得這個網頁畫面怎麼切
    </div>
  </div>
</body>
```

* 在大區塊先訂出寬度，並讓他們橫向排列

```css=
 .main-footer {
      /* 讓上面有距離出來 */
      padding: 150px 0 0 0;
      background: linear-gradient(-20deg, #3f5494, #08c7a5);
    }

    .main-footer .container {
      display: flex;
      width: 1200px;
      margin: auto;
    }

```

![](https://i.imgur.com/jVPxmCS.png)

```css=
    .footer-item {
      /* 使用flex的排版特性，來進行欄位大小的配置 */
      width: 0;
      flex-grow: 1;
      margin: 0 20px;

    }
```

![](https://i.imgur.com/vpFKfJ8.png)

## copyright

```css
.copyright {
      text-align: center;

      /* 這裡也加距離 */
      margin: 150px;
      padding: 10px 0;
      background-color: #3e5293;
      color: #70f6df;
      width: 100%;
      /* 左方有一個空格，沒有填滿 */

    }
```

![](https://i.imgur.com/vhMFqSF.png)

## nav區

```css=
 .footer-item nav {
      /* 設定直向排列之後，裡面的小項目就會排列整齊 */
      display: flex;
      flex-direction: column;
    }
```


![](https://i.imgur.com/8joRYrl.png)


#### 訂閱電子報的區域
* 再給予一個class名稱，在設定css時比較有指向性

```htmlembedded
<div class="footer-item footer-subs">
        <h4>訂閱電子報</h4>
        <form>
          <input type="text" name="">
          <input type="submit" value="訂閱">
        </form>
      </div>
```

## 訂閱電子報的居中

![](https://i.imgur.com/2PBp5rH.png)

```css=

    /* footer-item是flex的項目，所以他的高度和其他地方等高，但這個訂閱電子報欄位內容，並沒有等高<所以讓footer-item設定flex，設定直排，讓表單上下的距離能夠撐開 */
    .footer-subs {
      display: flex;
      flex-direction: column;

    }


    .footer-subs form {
      /* 這裏的flex是為了讓輸入框與訂閱紐能夠並排 */
      display: flex;
      /* 這裏的margin與寬度的設定是為了讓輸入框能夠整個居中   */
      margin: auto 0;
      width: 100%;

    }
```

![](https://i.imgur.com/NCJMKxk.png)


## 最後的調整

![](https://i.imgur.com/YRqfCso.png)

* 因為copyright的margin:150px; > 應該修改為margin: 150px 0 0 ;(只有上面有margin)

![](https://i.imgur.com/Rn0pOpz.png)


[t_page](https://eva813.github.io/my-projects/coke-NO006(v2).html)


---


[Day24 Flex 空間分配 flex-grow / flex-shrink / flex-basis](https://ithelp.ithome.com.tw/articles/10208741)
[HTML中button和input button的區別](https://www.itread01.com/content/1511769483.html)
