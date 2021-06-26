---
title: JS實作- scrollBlog
date: 2021-01-16 09:03:51
tags: ["js","ajax ","setTimeout"]
---
[成品](https://eva813.github.io/Eva_portfolio/scrollBlog/scrollBlog.html)
## 觀察功能需求

1.隨著頁面的往下捲動，會載入新的資料
* 一開始頁面載入，會呈現5筆資料，捲動往下，會再載入5筆
* 使用[{JSON} Placeholder](https://jsonplaceholder.typicode.com/)的資料來串接(運用ajax)
* 設定捲動位置，並於該位置時，會呈現載入的圖示
* setTimeout來呈現載入的時間與畫面

2.網頁的各資料是有順序，資料左上會有編碼
* 利用資料本身的id作為序號
* 於js時，帶入該變數取值

## css部分

### 1.製作左上方，數字
* 利用`position: absolute;`定位位置，並利用並利用`top`、`left`調整位置
* 使用`display: flex;`調整數字的位置，`align-items: center;
  justify-content: center;`使水平、垂直置中
```css
.post .number {
  position: absolute;
  top: -15px;
  left: -15px;
  font-size: 15px;
  width: 40px;
  height: 40px;
  border-radius: 50%;
  background: #fff;
  color: #296ca8;
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 7px 10px;
}
```
![](https://i.imgur.com/FkIcque.png)

### 2.載入畫面的原點圖示

* 先製作load的基本設定，如flex的排列、位置固定於下方，以及透明度的顯示
* 針對原點訂定背景、原點大小。新增動畫效果，`animation: bounce 0.5s ease-in infinite;`
* 動畫名稱`@keyframes bounce`，設定在某時間點，`translateY`，的位置。
* 為了讓圓點的跳動時間不同，針對2、3的原點，設定延遲時間`animation-delay: 0.2s;`
```css
.loader {
  /* opacity: 0; */
  display: flex;
  position: fixed; /*位置固定於最下面*/
  bottom: 50px;
  transition: opacity 0.3s ease-in;
}

.loader.show {
  opacity: 1;
}

/*製作圓點*/


.circle {
  background-color: #fff;
  width: 10px;
  height: 10px;
  border-radius: 50%;
  margin: 5px;
  animation: bounce 0.5s ease-in infinite;
}


.circle:nth-of-type(2) {
  animation-delay: 0.1s;
}

.circle:nth-of-type(3) {
  animation-delay: 0.2s;
}

@keyframes bounce {
  0%,
  100% {
    transform: translateY(0);
  }

  50% {
    transform: translateY(-10px);
  }
}


```

![](https://i.imgur.com/RuhdRt7.gif)

## js部分
### 1.jsonplaceholder:

* 為取得資料的位置
* 抓取5筆的方式，後面參數的設定而來
https://jsonplaceholder.typicode.com/posts?_limit5



### 2.使用ajax串接API

* [Async/Await 非同步流程控制-筆記](https://hackmd.io/Ciayzu-vQcyrh-WeVNeOPQ?view)
* 參考[Using async await with jQuery's $.ajax](https://petetasker.com/using-async-await-jquerys-ajax)、[How to await the ajax request?](https://stackoverflow.com/questions/27612372/how-to-await-the-ajax-request)，使用jQuery's`$.ajax`執行async
* 在try裡面，放入一般`$.ajax`抓取API資料的方式，如url、type、dataType
* 首先，在連結的部分，我們希望每次抓取5筆資料，呈現1頁，所以在外面設置`let limit = 5;let page = 1`於url中加入該變數
* 在抓取資料成功時，放入function，並用`$.each`來執行
     * 在`$.each`先傳入該資料(data)陣列，其`function (index, value)`放入索引以及值的內容。
     * 宣告`postEl`變數，創造名為`post`的`div`=> ``$('\<div />')``
     * 加入`post`class
     * 加入html
     * 最後將此變數，放置呈現文章的容器`postEl.appendTo('#posts-container');`

```javascript
let limit = 5
let page = 1

async function doAjax() {
  let result;

  try {
    result = await $.ajax({
      url: `https://jsonplaceholder.typicode.com/posts?_limit=${limit}&_page=${page}`,
      type: 'get',
      dataType: 'json',
      success: function (data) {
      
      $.each(data, function (index, value) {
          const postEl = $('<div />').addClass('post').html(`<div class="number">${value.id}</div> <div class="post-info"><h2 class="post-title">${value.title}</h2><p class="post-body">${value.body}</p>
      </div>`)
          //console.log(postEl)
          postEl.appendTo('#posts-container');
          //$('#posts-container').append(postEl)

        });

       
      }
    });

    return result;
  } catch (error) {
    console.error(error);
  }

}

doAjax();
```

查看是否有成功取得資料：`` console.log(data);``
![](https://i.imgur.com/wJPpdfh.png)


{%note info%}
使用jQuery創造div
[https://stackoverflow.com/questions/10402567/jquery-div-vs-div](https://stackoverflow.com/questions/10402567/jquery-div-vs-div)
{%endnote%}
{%note info%}
.each()
[jQuery.each()](https://api.jquery.com/jquery.each/)
{%endnote%}

### 3.Infinite Scroll無限捲動(瀑布流)

* `scrollTop`網頁右邊的卷軸到最上端網頁的距離有多少
* `scrollHeight` 取得整個沒被擋住的高、`clientHeight` 取得元素的高度（含padding 不含 border）
* 完整內容高度 (scrollHeight) = 內容頂端與捲軸頂端的距離 (scrollTop) + 捲軸本身高度 (clientHeight) + 內容底端與捲軸底端的距離。

例子:

```javascript
$(function () {
  $(window).scroll(function () {
    var scrollVal = $(this).scrollTop();
    $("span.qScrollTop").text(scrollVal);
  });
});

if(scrollVal > 500){
  /* 如果滾動的物件捲動 > 500 則觸發指定的動作。*/
}

```

{%note info%}
注意：window本身沒有scrollTop/Left這個屬性，所以window.scrollTop是undefined的
所以可能要從body、document來取得
{%endnote%}

#### 本段落語法：

* 上述說明各視窗高度得解釋，在此if判斷式中`scrollTop + clientHeight >= scrollHeight - 5`來計算，當捲軸捲到該位置時，要呈現載入畫面
* 接著根據載入畫面函式，來放入載入以及呈現接續畫面得設定
    * 使用`setTimeout()`，在1秒後消除載入圖示，接著在300毫秒後，馬上換頁執行載入新資料

```javascript
$(window).scroll(function () {
  var scrollTop = $(this).scrollTop();
  var scrollHeight = $('body').prop("scrollHeight");
  //一樣 var scrollHeight2 = document.documentElement.scrollHeight;
  var clientHeight = document.documentElement.clientHeight;
  //https://stackoverflow.com/questions/10423759/plain-javascript-to-jquery-clientheight

  // console.log('scrollTop:', scrollTop);
  // console.log('scrollHeight:', scrollHeight);
  // console.log('clientHeight:', clientHeight);


  if (scrollTop + clientHeight >= scrollHeight - 5) {
    //console.log('show up 123')
    showLoading();
  }
})


//顯示載入圖示，並取得更多串接資料
function showLoading() {
  $('.loader').addClass('show');


  setTimeout(function () {

    $('.loader').removeClass('show');
    setTimeout(function () {
      page++;
      doAjax();
    }, 300);

  }, 1000);  //1秒之後消失

}
```
參考資料：
[[筆記] 計算網頁底部位置，當網頁達到底部時才產生效果─jQuery](https://pjchender.blogspot.com/2015/04/jquery.html)
[一次搞懂 clientHeight/scrollHeight/scrollTop的區別](https://shubo.io/element-size-scrolling/#%E7%84%A1%E9%99%90%E6%8D%B2%E5%8B%95-infinite-scroll)
[談談 JavaScript 的 setTimeout 與 setInterval](https://kuro.tw/posts/2019/02/23/%E8%AB%87%E8%AB%87-JavaScript-%E7%9A%84-setTimeout-%E8%88%87-setInterval/)

### 4.篩選輸入框資料

* 綁定事件為`.keyup`指放開鍵盤的那個剎那，觸發該事件
* 執行的函式內容為：
    * `var text`取得輸入值並轉為小寫
    * 利用迴圈，去搜尋關鍵字，判斷，值轉為小寫文字的內容是否符合條件
    * -1 ：意指條件不符合
    * `indexOf()` 方法用來判斷字串字串變數中是否包含某字串。

```javascript
//輸入框搜尋//https://makitweb.com/jquery-search-text-in-the-element-with-contains-selector/  (=>Loop all .content )
  $('#filter').keyup(function () {

    // Search text
    var text = $('#filter').val().toLowerCase();

    // Hide all content class element
    $('.post').hide();

    // Search 
    $('.post').each(function () {

      if ($(this).text().toLowerCase().indexOf("" + text + "") != -1) {
        $(this).closest('.post').show();
      }
    });
```
參考資料：
[比較 keydown, keypress, keyup 的差異](https://medium.com/@yitailin/%E6%AF%94%E8%BC%83-keydown-keypress-keyup-%E7%9A%84%E5%B7%AE%E7%95%B0-4e873ba17e81)
[jQuery – Search text in the Element with :contains() Selector](https://makitweb.com/jquery-search-text-in-the-element-with-contains-selector/)
[JavaScript String indexOf()](https://www.fooish.com/javascript/string/indexOf.html)

---
補充參考：
[JS20min Day — 20 AJAX 非同步處理 (Asynchronous JavaScript and XML)](https://whien.medium.com/js20min-day-20-ajax-%E9%9D%9E%E5%90%8C%E6%AD%A5%E8%99%95%E7%90%86-asynchronous-javascript-and-xml-e74a059ab639)
[回呼函式 Callbacks、Promises 物件、Async/Await 非同步流程控制 - 彭彭直播 at 2019/04/07](https://www.youtube.com/watch?v=NOprCnnjHm0)
