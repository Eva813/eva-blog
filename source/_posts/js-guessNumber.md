---
title: JS實作- 猜數字遊戲
date: 2021-01-22 09:28:14
tags: ["js","jQuery ","random"]
---
[成品](https://eva813.github.io/Eva_portfolio/guessNumber/guessNumber.html)
## 觀察功能需求
* 練習語音辨識的使用： speech recognition API
    * [SpeechRecognition-MDN](https://developer.mozilla.org/en-US/docs/Web/API/SpeechRecognition)
* 根據不同情形，有不一樣的回覆
    * 依據猜測數字的情形給予：go higher,go lower

![](https://i.imgur.com/IjVZV5d.png)

* 數字猜中後，會出現恭喜答對等敘述，並呈現`Play Again`的按鈕

![](https://i.imgur.com/MhtrTrR.png)

## js部分

### 1.設定隨機數字
* `Math.floor(Math.random() * 100) + 1`取得1-100

```javascript
// 取得隨1-100隨機數字
function theRandomNum() {
  return Math.floor(Math.random() * 100) + 1;
  //沒有加1，數字會是0-99
}

let randomNum = theRandomNum();
console.log('randomNum:', randomNum);
```

### 2.SpeechRecognition

* 第一步要先將SpeechRecognition設為全域變數
    * 檢查瀏覽器是否有支援不需要 prefix 的 speechRecognition 介面，若沒有則將 webkit 標頭的 webkitSpeechRecognition 指定給該全域變數，
* 要使用 speechRecognition ，必須透過 SpeechRecognition 建構式建立一個物件實例。
* `recognition.onresult` 設定取得語音辨識的結果
* 執行`writeMessage(myWord);checkNumber(myWord);`兩個函式
```javascript
try {
  var SpeechRecognition = window.SpeechRecognition || window.webkitSpeechRecognition;
  var recognition = new SpeechRecognition();
  //設定辨識語言。
  recognition.lang = 'zh-tw';
}
catch (e) {
  console.error(e);

}



// 開始語音辨識
recognition.start();
//取得語音辨識的結果
recognition.onresult = function (event) {
  //console.log(event);
  let myWord = event.results[0][0].transcript
  //console.log('You said: ', myWord);
  writeMessage(myWord);
  checkNumber(myWord);
};

```
* 簡易測試，取得得語音辨識 :point_down: 
```javascript=
//取得語音辨識的結果
recognition.onresult = function (event) {
  console.log('You said: ', event.results[0][0].transcript);
};
```
![](https://i.imgur.com/QLWaJB0.png)


![](https://i.imgur.com/FfF5Aqv.png)



[Using the Web Speech API](https://developer.mozilla.org/en-US/docs/Web/API/Web_Speech_API/Using_the_Web_Speech_API)
[How to build a simple speech recognition app](https://www.freecodecamp.org/news/how-to-build-a-simple-speech-recognition-app-a65860da6108/)

### 3.記錄下使用者所說的話
* 在`msg`的div插入辨識語言的結果文字
```javascript
function writeMessage(myWord) {
  $('#msg').html(`
  <div>You said:</div>
    <span class="box">${myWord}</span>
    
  `)
}
```
### 4.核對隨機數字與猜的數字是否一致
* 設定當辨識到的不是數字：This is not a valid number
* 當範圍不在1-100之間：Number must be between 1 and 100
* 猜中數字的結果：Congrats! You have guessed the number!
* 數字猜測GO LOWER,GO HIGHER
```javascript
function checkNumber(myWord) {
  let number = +myWord; //轉為數字
  if (Number.isNaN(number)) {
    $('#msg').append(`<div>This is not a valid number</div>`)
    return
  }
  //設定語句
  if (number > 100 || number < 1) {
    $('#msg').append(`<div>Number must be between 1 and 100</div>`);
    return;
  }

  if (number === randomNum) {
    $('body').html(`
    <h2>Congrats! You have guessed the number! <br><br> 
    It was ${number}</h2>
    <button class="play-again" id="play-again" > Play Again </button>
    `);
  } else if (number > randomNum) {
    $('#msg').append(`<div>GO LOWER</div>`);
  } else {
    $('#msg').append(`<div>GO HIGHER</div>`)
  }

}
```


* 透過 Number.isNaN() 語法可以判斷，若回傳 true，則表示值為 NaN，否則為 false

[Number.isNaN()](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/Number/isNaN)

### 5.使猜數字可以持續猜，直到答對為止
* `recognition.onend`使用於控制語音識別的服務，並應用在結束語音識別時的情境。
* `recognition.start();`每次數字猜完後，都會重新開始語音識別，直到猜中。
* 在數字猜中後，會出現`play again`按鈕，所以要設定此按鈕，按下後能夠重新開始遊戲 =>`window.location.reload()`

```javascript
//https://stackoverflow.com/questions/51080738/how-to-make-speech-recognition-continous-for-a-fix-time-period
recognition.onend = function () {
  recognition.start();
  //點擊按鈕，重啟遊戲畫面
  $('body').find('#play-again').click(

    function () {
      window.location.reload();
    }

  );
}
```
[實戰智慧插座 14 - 我說開燈就開燈 ( 語音辨識 )](https://ithelp.ithome.com.tw/articles/10187827)

---

其他參考資料:
[Converting from Speech to Text with JavaScript](https://tutorialzine.com/2017/08/converting-from-speech-to-text-with-javascript)
[JavaScript Speech Recognition](https://davidwalsh.name/speech-recognition)
[Day21 -- Speech Recognition (lukechu)](https://ithelp.ithome.com.tw/articles/10248999)
[Day 21 - Speech Detection (Arel)](https://ithelp.ithome.com.tw/articles/10196577)
[DAY20 : Speech Detection (BROWN)](https://ithelp.ithome.com.tw/articles/10224891?sc=rss.qu)
