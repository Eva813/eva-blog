---
title: API-youtube
date: 2021-02-23 14:47:04
tags: ["Ajax","API"]
---
[成品](https://eva813.github.io/Eva_portfolio/YoutubeAPI/YoutubeAPI.html)

## 觀察功能需求
* 搜尋框，輸入搜尋關鍵字，下方會列出5筆列表
    * 有圖片縮圖、影片標題、影片描述
* 按下next page 會取得下5筆資料
* 按下prev page，會取得上5筆資料

ajax 在同一個頁面，不做刷新就可以改變頁面的內容。
q: query

## youtube API(application programming interface)資料查看
[YouTube 資料 API 參考手冊](https://developers.google.com/youtube/v3/docs/)

取得授權：[憑證](https://console.developers.google.com/projectselector2/apis/credentials?pli=1&supportedpurview=project)

![](https://i.imgur.com/asf7coq.png)

API 金鑰:AIzaSyCKPRCaNS-PQJLl2jzoQEf-O4I0tnrtd8M

{% alert info no-icon %}
jQuery API D ocument => 表示看官方文件
串API =>
{% endalert %}

#### 檢視範例，ajax資料的情形

![](https://i.imgur.com/wc6VWtV.png)

![](https://i.imgur.com/tSYR4vu.png)

## JS 部分
### 1.嘗試串接Youtube API

* 首先需考量，我們所需要的資料參數有哪些(
* 在抓取成功的部分，要設置抓取五筆搜尋到的五筆資料，並將其呈現於頁面
    * 除了5筆資料外，還需要跳轉上、下頁的按鈕
```javascript
function getVideo() {
  let value = $('#search-field').val();
  let youtubeURL = "https://www.googleapis.com/youtube/v3/search";
  $.ajax({
    type: "GET",
    url: youtubeURL,
    data: {//將要放入網址的參數放在這
      part:
        'id,snippet',// 必填，把需要的資訊列出來
      q: value,// 查詢文字
      maxResults: 5,// 預設為五筆資料，可以設定1~50
      type: "video",
      key: 'AIzaSyCKPRCaNS-PQJLl2jzoQEf-O4I0tnrtd8M'// 使用 API 只能取得公開的播放清單

    },
    dataType: "json",
    success: function (data) {
      //console.log(data);
      let nextPageToken = data.nextPageToken;
      let prevPageToken = data.prevPageToken;

      //試著將收到的資料傳入，並呈現於畫面中
      insertItems(data);

      getBtn(data, prevPageToken, nextPageToken)


    }, error: function (err) {
      console.log('oh no');
    }
  });
}
```
網址放入參數
[[JS] 使用 JavaScript 解析網址與處理網址中的參數（URL Parameters）](https://pjchender.blogspot.com/2018/08/js-javascript-url-parameters.html)

### 2.將資料呈現於頁面(insertItems())

* 透過迴圈方式將要抓取的資料依序填入
* 觀察原本畫面結構，並依據去找尋資料中符合的資料
    * videoId、videoTitle、description、channelTitle...

```javascript
function insertItems(item) {
  let output = '';
  $.each(item.items, function (index, value) {
    let videoId = item.items[index].id.videoId;
    let videoTitle = item.items[index].snippet.title;
    let description = item.items[index].snippet.description;
    let thumbnailURL = item.items[index].snippet.thumbnails.high.url;//高解析度影片縮圖
    let channelTitle = item.items[index].snippet.channelTitle;
    let publishedDate = item.items[index].snippet.publishedAt;
    // 按鈕



    output += `<li><div class="list-left">
    <img src=" ${thumbnailURL}"></div>
    <div class="list-right">
    <h3>
    <a data-fancybox data-type="iframe" data-src="https://www.youtube.com/embed/${videoId}" "href="javascript:;"> ${videoTitle} </a></h3> <small>By <span class="cTitle"> ${channelTitle}</span> on ${publishedDate}</small>
    <p> ${description} <p></div></li> <div classs="clearfix"></div> 
    `

    $('#results').html(output);
  });
};
```

## 3.製作上、下頁的按鈕鍵

* 注意該函式要帶入的參數有哪些
* 我們要判斷為，在第一頁時，只有下一頁的按鈕
    * 判斷方式為，`(!prevPageToken)`沒有上一頁的token
    * （截圖）
* 在判斷之中，我們需要將要插入的標籤與內容，放入，並append到畫面要呈現的位置
* 注意：在插入的button之中，加入`data-token="${nextPageToken}" data-query="${value}"`，用於後續抓取屬性內容。

```javascript
function getBtn(value, prevPageToken, nextPageToken) {
  console.log(nextPageToken)
  if (!prevPageToken) {
    var btnOutput = $(`<div class="button-container">
    <button id="next-button" class="paging-button" data-token="${nextPageToken}" data-query="${value}" onclick="pressNext();"> Next Page</button></div>`
    )
    $("#buttons").append(btnOutput);
  } else {
    var btnOutput = $(`<div class="button-container"><button id="prev-button" class="paging-button" data-token="${prevPageToken}" data-query="${value}" onclick="pressPrev();"> Prev Page</button>  
    <button id="next-button" class="paging-button"  data-token="${nextPageToken}" data-query="${value}" onclick="pressNext();">Next Page</button></div>`)
    $("#buttons").append(btnOutput);
  }

}
```

[Youtube:Implementation: Pagination](https://developers.google.com/youtube/v3/guides/implementation/pagination)

## 4.下一頁點下後，下五筆的資料
* 同樣以ajax的方式來抓取
    * 在參數的地方我們要抓取的是，按取下一頁的token
* success之中所應用的函示，同樣為插入5筆的資料，以及上下頁的按鈕

```javascript
//取得下一頁的資料
function pressNext() {

  let youtubeURL = "https://www.googleapis.com/youtube/v3/search";
  let token = $('#next-button').data('token');
  let value = $('#search-field').val();
  $.ajax({
    type: "GET",
    url: youtubeURL,
    data: {//將要放入網址的參數放在這
      part:
        'id,snippet',// 必填，把需要的資訊列出來
      q: value,// 查詢文字
      pageToken: token,
      maxResults: 5,// 預設為五筆資料，可以設定1~50
      type: "video",

      key: 'AIzaSyCKPRCaNS-PQJLl2jzoQEf-O4I0tnrtd8M'// 使用 API 只能取得公開的播放清單

    },
    dataType: "json",
    success: function (data) {
      console.log(data);
      let nextPageToken = data.nextPageToken;
      let prevPageToken = data.prevPageToken;

      // 清空內容
      $('#results').html('');
      $('#buttons').html('');
      //試著將收到的資料傳入，並呈現於畫面中
      insertItems(data);

      getBtn(data, prevPageToken, nextPageToken)


    }, error: function (err) {
      console.log('oh no');
    }
  });

}
```

### 5.上一頁
* 同樣的抓取方式，只有要帶入的資料有些差異
* `token = $('#prev-button').data('token');`
```javascript
//前一頁
function pressPrev() {
  // 清空內容
  $('#results').html('');
  $('#buttons').html('');
  let youtubeURL = new URL(`https://www.googleapis.com/youtube/v3/search?`);
  let token = $('#prev-button').data('token');
  let value = $('#search-field').val();
  $.ajax({
    type: "GET",
    url: youtubeURL,
    data: {//將要放入網址的參數放在這
      part:
        'id,snippet',// 必填，把需要的資訊列出來
      q: value,// 查詢文字
      pageToken: token,
      maxResults: 5,// 預設為五筆資料，可以設定1~50
      type: "video",

      key: 'AIzaSyCKPRCaNS-PQJLl2jzoQEf-O4I0tnrtd8M'// 使用 API 只能取得公開的播放清單

    },
    dataType: "json",
    success: function (data) {
      console.log(data);
      let nextPageToken = data.nextPageToken;
      let prevPageToken = data.prevPageToken;

      // 清空內容
      $('#results').html('');
      $('#buttons').html('');
      //試著將收到的資料傳入，並呈現於畫面中
      insertItems(data);

      getBtn(data, prevPageToken, nextPageToken)


    }, error: function (err) {
      console.log('oh no');
    }
  });

}
```


[HTML5 自定義屬性 data-* 和 jQuery.data 詳解
2018.06.20](https://codertw.com/%E5%89%8D%E7%AB%AF%E9%96%8B%E7%99%BC/177833/)
[[技術分享] 什麼是 HTML 5 中的資料屬性（data-* attribute）](https://pjchender.blogspot.com/2017/01/html-5-data-attribute.html)

### 6.運用fancybox的方式彈出
* 使用fancyBox展示影片
    * [fancybox Documention](http://fancyapps.com/fancybox/3/docs/#iframe)
    * [fancybox-CDN](https://cdnjs.com/libraries/fancybox)
* 使用iframe連結欲嵌入的外部影片連結
    * iframe 是框架的一種，也稱為內置框架或內聯框架，用來在網頁內嵌入另外一個網頁
[](https://fancyapps.com/fancybox/3/docs/#iframe)

* 透過CDN方式連結fancybox套件
```javascript
<!-- fancyBox -->
  <link rel="stylesheet" href="https://cdn.jsdelivr.net/gh/fancyapps/fancybox@3.5.7/dist/jquery.fancybox.min.css" />
  <script src="https://cdnjs.cloudflare.com/ajax/libs/fancybox/3.5.7/jquery.fancybox.min.js"></script>
```

* 在我們點選該影片名稱區，插入影片id
```javascript
<h3><a data-fancybox data-type="iframe" data-src="https://www.youtube.com/embed/${videoId}" "href="javascript:;"> ${videoTitle} 
</a></h3>
```
```javascript
$('[data-fancybox]').fancybox({
  toolbar  : false,
  smallBtn : true,
  iframe : {
    preload : false
  }
})
```
[[jQuery] - 練習-Youtube Search Engine](https://orow.github.io/2019/03/16/jQuery-youtube-search/)

---
補充知識：
XMLHttpRequest(XHR):是一種規範，用來發ajax使用

token(令牌)：唯一的、可以認證身份或取得資料

GET:向伺服器發request，（伺服器會告訴我們要發伺服器的網址，我們向它請求資訊）

#### 用瀏覽器debug

![](https://i.imgur.com/IlQ8Glx.png)

* 可在watch加上要觀察的變數
![](https://i.imgur.com/7RcELS4.png)



---
參考資料
[[30apis] Day 7 : YouTube Data API + 基礎 Fetch API](https://ithelp.ithome.com.tw/articles/10194007)
[How To Implement A Youtube Video Search Using Youtube Data API V3](https://www.c-sharpcorner.com/article/how-to-implement-youtube-video-search-using-youtube-data-api-v3-in-wordpress-web2/)
[Youtube Data API 使用手札](https://hackmd.io/@c36ICNyhQE6-iTXKxoIocg/S1eYdtA1P)
[Youtube Data API 申請](https://a42033.gitbooks.io/api/content/Youtube/youtube_data_api/applicate.html)
[配額](https://console.cloud.google.com/iam-admin/quotas?hl=zh-tw&_ga=2.128962188.1087850229.1615615433-929425058.1615373715&orgonly=true&angularJsUrl=%2Fprojectselector%2Fiam-admin%2Fquotas%3Fhl%3Dzh-tw%26_ga%3D2.128962188.1087850229.1615615433-929425058.1615373715%26supportedpurview%3Dproject%26orgonly%3Dtrue%26folder%3Dtrue%26organizationId%3Dtrue&project=my-youtube-api-307211&folder=&organizationId=&supportedpurview=project)

```javascript=
{
  "error": {
    "code": 403,
    "message": "The request cannot be completed because you have exceeded your \u003ca href=\"/youtube/v3/getting-started#quota\"\u003equota\u003c/a\u003e.",
    "errors": [
      {
        "message": "The request cannot be completed because you have exceeded your \u003ca href=\"/youtube/v3/getting-started#quota\"\u003equota\u003c/a\u003e.",
        "domain": "youtube.quota",
        "reason": "quotaExceeded"
      }
    ]
  }
}
```

---
[favicon.ico error](https://stackoverflow.com/questions/31075893/im-getting-favicon-ico-error)

問題：
* script.js: Allow attribute will take precedence over 'allowfullscreen'.
![](https://i.imgur.com/0JiThlt.png)
