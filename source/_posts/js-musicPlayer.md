---
title: JS實作- musicPlayer
date: 2021-01-15 21:51:43
tags: ["JS","jQuery ","append"]
---
## 觀察須製作的功能
[成品](https://eva813.github.io/Eva_portfolio/musicPlayer/musicPlayer.html)

1. 播放器API的樣式

[\<audio>: The Embed Audio element](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/audio)

2. 播放時，左方的圓型圖示會旋轉。
* 先於css檔案製作符合動畫的class
* 在js時，依照符合的情況加入該class

![](https://i.imgur.com/3LBdMiU.png)


3. 播放時，上方會跳出播放歌曲的名稱。
* 先於css檔案製作符合動畫的class，設定`opacity`1或0，`translateY`位置的移動
* 在js時，依照符合的情況加入該class

![](https://i.imgur.com/U3dx7yG.png)


4. 左右按鍵可以更換曲目，同時圖片會更換
* 設定陣列，使歌曲與圖片相符
* 並讓前後的跳轉鍵，依照順序或條件來變化

![](https://i.imgur.com/NuxfsOb.png)

---
## html部分

### 跳轉按鍵
* 播放鍵、前/後跳轉的按鈕 

```html
<div class="nav">
        <button id="prev" class="action-btn">
          <i class="fas fa-backward"></i>
        </button>

        <button id="play" class="action-btn  action-btn-big">
          <i class="fas fa-play"></i>
        </button>

        <button id="next" class="action-btn">
          <i class="fas fa-forward"></i>
        </button>
</div>
```

![](https://i.imgur.com/w1bpDjr.png)

## css部分

### 背景顏色

[linear-gradient()](https://developer.mozilla.org/en-US/docs/Web/CSS/linear-gradient())

```css
/* 傾斜度\顏色、從哪個位置開始 */
  background-image:linear-gradient(0deg
  ,rgb(247, 247, 247,1) 23.8%,
  rgba(252,221,221,1) 92% );
```

![](https://i.imgur.com/Xb5PpmY.png)

## 圖片圓形以及旋轉

```css
.img-container{
  position: relative;
  width: 110px;
}
/*圖片圓形*/
.img-container img{
  border-radius: 50%;
  object-fit: cover;
/*填滿元素的寬度及高度(維持原比例)，通常會剪掉部分的物件*/
 height: 110px;
 width: inherit; /*繼承自父層的寬度屬性值。*/
 position:absolute;
 bottom: 0;
 left: 0;

 animation: rotate 3s linear infinite;
 animation-play-state: paused;
}

/* 播放class的製作 */
.music-container.play .img-container img{
 animation-play-state:running;

}
@keyframes rotate{
  from{
    transform: rotate(0deg);
  }
  to {
    transform: rotate(360deg);
  }
}
```
* 使用animation\@keyframe
[新手前端也不該犯的錯：圖片變形](https://wcc723.github.io/development/2020/10/11/img-cover/)

![](https://i.imgur.com/RfUz8Pk.png)

* 在圖片中間加入圓形點，以看起來像光碟樣式

```css
.img-container::after{
  content: '';
  border-radius: 50%;
  background-color: #fff;
  position: absolute;
  left: 50%;
  bottom: 100%;
  width: 25px;
  height: 25px;
  transform: translate(-50%,50%);
}
```

![](https://i.imgur.com/HlnWKJb.png)


### 播放進度條的製作

1. 標題
```css
.music-info{
  background-color: rgba(255,255,255,.5);
  border-radius:15px 15px 0 0 ;
  position: absolute;
  top: 0;
  left: 20px;
  opacity: 0;
  transform: translateY(0%);
  transition: transform 0.3s ease-in,opacity 0.3s ease-in;
  z-index: 0;
}

.music-container.play .music-info{
  opacity: 1;
   transform: translateY(-100%);
}
```
* 將曲目標題設置背景、圓角
* 利用 transfor、opacity來呈現位置的變化

![](https://i.imgur.com/icBK0qt.png)

* 修改標題位置

```css=
 /*放入.music-info之中*/
 width: calc(100% - 40px);
  padding: 10px 10px 10px 150px;
```
{%note info%}
calc() 的運算式一樣是按照先乘除後加減進行計算
 width: calc(100% - 40px);
/* 意思是 100% 的寬度 - 去 40px */
[CSS： calc() 數值運算](https://5xruby.tw/posts/css-calc/)
{%endnote%}


![](https://i.imgur.com/er3G6Wd.png)

```css
.music-info h4{
margin: 0;
}

```
![](https://i.imgur.com/qiWmxmu.png)

2.進度條（progress）

```css
.progress-container{
  background-color: #fff;
  border-radius: 5px;
  height: 4px;
  width: 100%;
  margin: 10px 0;
  cursor: pointer;
}

.progress{
  background-color: #fe8daa;
 border-radius: 5px;
  height: 4px;
  width: 0%;
  transition: width .1s linear ;
}

```
![](https://i.imgur.com/uPUHWcM.png)



## js部分

### 1.音樂播放鍵(播放與停止)
* 分別有兩個函式：`playMusic()` 、`pauseMusic()`
* 在css設定`.play`的css，並於js綁定事件，加入css的效果(使圖片如同轉盤，會旋轉)
* 在icon的部分：在播放時：為暫停鍵圖示;在暫停時：為播放鍵的圖示
* 透過play()、pause()，讓音源播放與暫停

{%note info%}
因為play()、pause()是DOM元素，不是jQuery的function，而要用jQuery取得DOM元素
``$('#audio').get(0).play()`` =>``$('#audio')[0].play()``
`$('#audio').get(0).pause()` =>`$('#audio')[0].pause()`
{%endnote%}

參考資料：
[How do I pull a native DOM element from a jQuery object?](https://learn.jquery.com/using-jquery-core/faq/how-do-i-pull-a-native-dom-element-from-a-jquery-object/)
[Play/pause HTML 5 video using JQuery](https://stackoverflow.com/questions/4646998/play-pause-html-5-video-using-jquery)

```javascript
//播放音樂
function playMusic() {

  $('.music-container').addClass('play');
  $('#play').find('i').removeClass('fa-play');
  $('#play').find('i').addClass('fa-pause');
  $('#audio').get(0).play();

};
```
```javascript
//停止音樂
function pauseMusic() {
  $('.music-container').removeClass('play');
  $('#play').find('i').addClass('fa-play');
  $('#play').find('i').removeClass('fa-pause');
  $('#audio').get(0).pause();
}
```
* #### 綁定事件：
如果音樂是播放狀態的話，就執行`pauseMusic()`，反之，音樂不處於波方時，執行`playMusic()`
```javascript
 $('#play').click(function (e) {
    var hasPlay = $('#music-container').hasClass('play');
    //var a = $('#audio').paused;
    if (hasPlay) {
      pauseMusic();
    } else {
      playMusic();
    }
  });

```




### 2.載入音樂

* 將歌曲名稱建立陣列，並將此回傳到建立的函式中
* 設定預設的索引`let songIndex = 1`
* 使用 attr( 屬性名, 屬性值 )，獲取屬性的值，在這部分，透過歌曲名稱來取得歌曲與照片

```javascript
var songs_name = ['hey', 'summer', 'ukulele'];
let songIndex = 1;

function loadSongs(song) {
  //console.log(song);
  $('#title').text(song);
  
  $('#audio').attr('src', `music/${song}.mp3`);
  $('#cover').attr('src', `img/${song}.jpg`);
}

// Initially load song details into DOM
loadSongs(songs_name[songIndex]);
```
#### 綁定於下一首、前一首的圖示被點擊時
```javascript
$('#next').click(nextSong)
  $('#prev').click(function () { prevSong();
  });
```


* 查看函式回傳
```javascript
loadSongs(songs_name);
```
![](https://i.imgur.com/je9hXJY.png)

* 要抓取到該索引
```javascript
loadSongs(songs_name[songIndex]);
```
![](https://i.imgur.com/5not1Ji.png)
此段落執行時，出現問題：
![](https://i.imgur.com/dj4H5hb.png)
* 搜尋到的處理方式：在html的`head`<link rel="shortcut icon" href="#">
[I'm getting favicon.ico error
](https://stackoverflow.com/questions/31075893/im-getting-favicon-ico-error/31329574)

參考資料：
[jquery prop和attr的區別](https://iter01.com/470327.html)
[jQuery .attr() vs .prop()](https://cythilya.github.io/2017/09/10/jquery-attr-vs-prop/)

### 3.左右鍵的跳轉

* 下一首歌曲，與跳至前一首歌曲，兩個函式的撰寫有異曲同工之妙，只有在設條條件上的差異。
* `nextSong()`:設定條件為歌曲索引`songIndex`比 歌曲陣列長度-1`songs_name.length - 1`還要小，我們就要將索引+1。
    * 例如這裏預設的索引為1，所以在點擊當下，判斷<`songs_name.length - 1`，所以需加1，索引變成2，跳至下一首 'ukulele'
* prevSong()：設定條件為歌曲索引`songIndex`比0大，就要索引-1，跳轉至上一首歌曲。若沒符合的話，索引就等於`songs_name.length - 1`

```javascript
//歌曲的索引，如果比歌曲總數-1還小，就將索引加1，跳轉下一首
function nextSong() {

  if (songIndex < songs_name.length - 1) { songIndex += 1; } else {
    songIndex = 0
  };
  loadSongs(songs_name[songIndex]);
  playMusic();
}


//跳轉前一首


function prevSong() {
  //如果索引沒有大於0。就會播索引[2]
  if (songIndex > 0) { songIndex -= 1; } else {
    songIndex = songs_name.length - 1;
  };
  loadSongs(songs_name[songIndex]);
  playMusic();
}
```

參考資料：
[Create a Music Player using JavaScript](https://www.geeksforgeeks.org/create-a-music-player-using-javascript/)

### 4.點擊進度條，可以跳轉音樂位置

#### (1).顯示粉色進度條

* duration：音源的時間(一樣是使用get方式取得)
* currentTime：音樂播放的當前位置（以秒計）
* progressBar：進度條
* 計算百分比：`(currentTime / duration) * 100`

```javascript
//顯示進度條
//為何取[0]
function handleProgress() {
  var duration = $('#audio').get(0).duration;
  //console.log(duration)
  var currentTime = $('#audio')[0].currentTime;
  //console.log(currentTime)
  const progressBar = $('#progress');
  const progressPercent = (currentTime / duration) * 100;

  //currentTime目前播放時間去與影片長度duration轉換成百分比，即可得到目前播放時間的百分比

  progressBar.css('width', `${progressPercent}%`);
};

```

#### (2).點擊進度條，會跳轉到該位置播放

* width:獲取該進度條容器的寬度
* 抓取點擊位置：
    * e.pageX:取得滑鼠在頁面裡的位置
    * elm.offset().left:絕對座標X軸
* 將（點擊位置除以進度條的總長）乘以 音樂的時間長度，就可以取得點擊的時間位置`theTime = ((xPos / width))* duration;`
* 最後現在時間設為，計算好的點擊時間


```javascript
//點擊進度條

//https://ithelp.ithome.com.tw/articles/10194871
//https://tools.wingzero.tw/article/sn/102


$('#progress-container').click(function (e) {
  const width = $('#progress-container').width(); //216.25
  //console.log(width);
  //取得點擊位置
  var elm = $(this);
  var xPos = e.pageX - elm.offset().left;

  //console.log(xPos);

  var duration = $('#audio')[0].duration;
  var theTime = ((xPos / width))
    * duration;
  //console.log(theTime);
  $('#audio')[0].currentTime = theTime;
});

```
執行過程，利用console來查看
```javascript
console.log(e.pageX);
console.log(elm.offset().left);
console.log(xPos)
```
參考資料：
[取得滑鼠位置、元素位置與區塊內的相對位置](https://tools.wingzero.tw/article/sn/102)

### 5.歌曲播完後，換下一首

```javascript
  $('#audio').on('ended',nextSong);
```
參考資料：
[audio auto play next song when previous is finished](https://stackoverflow.com/questions/39325637/audio-auto-play-next-song-when-previous-is-finished/52228798)

## 6.增加隨機的變換背景色彩

* 色彩為0-256;而因為想取的較亮的色系，所以取64-256
    * `Math.random()` 會回傳一個偽隨機小數 (pseudo-random) 介於0到1之間(包含 0，不包含1) 
    * `Math.floor()`會將所有的小數無條件捨去到比自身小的最大整數
    * `Math.floor(Math.random() * 255) + 64`就會取得64 to 256
* 宣告變數``bgColor``為rdba(red,green,blue,a)
* 最後將此函式，放入`loadSongs()`函式當中，每次播放、跳轉歌曲，同時背景顏色也會改變
![](https://i.imgur.com/piYCbc5.png)


```javascript
function random_bg_color() {
  // Get a random number between 64 to 256 

  // (for getting lighter colors) 
  let red = Math.floor(Math.random() * 255) + 64;
  let green = Math.floor(Math.random() * 255) + 64;
  let blue = Math.floor(Math.random() * 255) + 64;
  let a = 0.5

  // Construct a color withe the given values 
  let bgColor = "rgba(" + red + ", " + green + ", " + blue + ", " + a + ")";

  var bg = $(document.body).css('background', `${bgColor}`);
  // Set the background to the new color 
  bg = bgColor;
  console.log(bg)

} 
```
參考資料：
[Create a Music Player using JavaScript](https://www.geeksforgeeks.org/create-a-music-player-using-javascript/)
[Won't Math.floor(Math.random() * 255) generate uneven probabilities?
](https://stackoverflow.com/questions/20790579/wont-math-floormath-random-255-generate-uneven-probabilities)[[筆記][JavaScript]用Math.random()取得亂數的技巧](https://ithelp.ithome.com.tw/articles/10197904)

