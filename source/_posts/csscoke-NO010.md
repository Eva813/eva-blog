---
title: Amos金魚都能懂的切版教學影片-csscoke NO010
date: 2020-11-27 15:48:59
tags: ["CSS","切版","float"]
categories: CSS
---
## 練習使用float排版

[成品](https://eva813.github.io/my-projects/coke-NO010(v2).html)
![](https://i.imgur.com/HSxrHVY.jpg)

## HTML架構
```html
<div class="wrap">
	<div class="item">
		<img src="https://picsum.photos/500/500?random=10">
		<div class="txt">
			<h3>金魚都懂切版</h3>
			<p>金魚都懂的這個網頁怎麼切，是 IThome 鐵人賽的主題，主要訴求在簡。</p>
		</div>
	</div>
	<div class="item">
		<img src="https://picsum.photos/500/500?random=20">
		<div class="txt">
			<h3>金魚也懂CSS</h3>
			<p>金魚都能懂的 CSS 選取器，是 IThome 鐵人賽的主題之一，</p>
		</div>
	</div>
	<div class="item">
		<img src="https://picsum.photos/500/500?random=30">
		<div class="txt">
			<h3>金魚還會HTML</h3>
			<p></p>
		</div>
	</div>
	<div class="item">
		<img src="https://picsum.photos/500/500?random=40">
		<div class="txt">
			<h3>阿你會甚麼?</h3>
			<p></p>
		</div>
	</div>
	<div class="item">
		<img src="https://picsum.photos/500/500?random=66">
		<div class="txt">
			<h3>對不起我又嗆人了</h3>
			<p> CSScoke 的直播，趁現在趕快去定閱一波阿!</p>
		</div>
	</div>
</div>
```
## 調整版面

* 1.在wrap來訂出畫面尺寸大小
* 2.使用float
* 3.在第一個的item去設定，佔畫面的50%;而另外四個則是以25％去放置在畫面中
* 4.蚯蚓選取器：『對象 A 同層後方的所有 B』

```css
/* 先將畫面尺寸定出來 */
    .wrap {
      width: 960px;
      margin: auto;
      /* 在父層設置這個，就可以讓父層抓到所有子層的高度 */
      overflow: hidden;
    }

    .item {
      float: left;
    }

    .item img {
      width: 100%;
      vertical-align: middle;
    }


    .item:first-child {
      width: 50%;
    }

    .item:first-child~.item {
      width: 25%;
    }
```


* 設定.item .txt區塊的寬為100%，以及paddimg:15px，這會使得文字區塊超出div。
* 要使用 `box-sizing: border-box;`

![](https://i.imgur.com/iaIicVC.jpg)

![](https://i.imgur.com/c5BGt1E.jpg)


## 互動效果的設定

* 設定文字被摸到出現或消失
```css
.item .txt {
      
      /* 調整兩個段落的垂直居中 */
      /* display: flex; */
      flex-direction: column;
      justify-content: center;
      align-items: center;

      /* 要設定互動，display: flex;要去掉 */
      display: none;

    }
    .item:hover .txt {
      display: flex;
    }
     /* ******************************/
    /* ***或是以透明度的方式來做顯示互動的效果*** */
  .item .txt {  
      opacity: 0;
      transition: .5s;

    }

    .item:hover .txt {
      opacity: 1;
    }

    
    
```

* 也可將互動改唯有縮放效果的方式
* `scale()`用於修改元素大小

```css=
 .item .txt {  
      opacity: 0;
      transform: scale(0);
      transition: .5s;

    }

    .item:hover .txt {
      opacity: 1;
      transform: scale(1);
    }
```


## 補充教學-float 浮動
* 以圖片與段落文字為例
```html

<img src="https://picsum.photos/500/500?random=6">
<p>
"Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur.

</p>


```

![](https://i.imgur.com/VkWWZpD.png)


![](https://i.imgur.com/jIi69In.png)


* 當將img設定浮動之後，他會浮上來一層，在p之上，與裡面的文字同一層（所以會擠壓到文字）
* `float:left;` `float:right;` 沒有center

示意圖：
![](https://i.imgur.com/lUTqlRy.png)

形成文繞圖：
![](https://i.imgur.com/6NuHHch.png)


![](https://i.imgur.com/a8d7ERZ.png)

* 讓圖片與文字有些距離，可以在img設定margin

![](https://i.imgur.com/4vzaQnJ.png)


## float排版

* 設定三個欄位

* 子物件設定為浮動之後，父層就會抓不到子物件的高度


![](https://i.imgur.com/LcieUW2.png)


* 物件設定float之後，物件會排排站


* 再將三個欄位設定margin，且增加ooxx的div
* 並將ooxx，設定`clear：both;` 讓父層抓到高度

>父層其實是被ooxx所拉開高度，而不是被內容的float物件所撐開
>clear作用是散開浮動物件，跑到所有浮動物件後方(最下方) 圖二，紅色。

![](https://i.imgur.com/9kLyaNW.png)

![](https://i.imgur.com/dsqmPw6.png)


---

[Day 19 | 我比較喜歡脆笛酥 - 方塊酥版面 Part 1](https://ithelp.ithome.com.tw/articles/10248813)
[親代選取器之妹妹選取器與鞭炮串選取器](https://ithelp.ithome.com.tw/articles/10220656)
[【DAY24】transform，進入視覺系的CSS第一步，網頁要動起來了！（一）](https://ithelp.ithome.com.tw/articles/10196979)
[Transform變形](https://ithelp.ithome.com.tw/articles/10194395)
[[CSS] 浮動 (float) 與清除浮動 (clear)](https://medium.com/%E9%A6%AC%E6%A0%BC%E8%95%BE%E7%89%B9%E7%9A%84%E5%86%92%E9%9A%AA%E8%80%85%E6%97%A5%E8%AA%8C/css-%E6%B5%AE%E5%8B%95%E8%88%87%E6%B8%85%E9%99%A4%E6%B5%AE%E5%8B%95-34d3f49ef817)
