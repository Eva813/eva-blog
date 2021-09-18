---
title: JS實作- typingGame
date: 2021-01-22 09:31:35
tags: ["JS","jQuery ","toggleClass"]
---
[成品](https://eva813.github.io/Eva_portfolio/typeGame/typeGame.html)
![](https://i.imgur.com/fC1gVUt.png)

## 觀察功能需求
* 在difficuly地方，有設置難易度，並於重新整理後，仍然儲存選擇的難易度
    * 使用到取表格中的值
    * localstorage的方式，儲存設置的難易度
* 創造出隨機的單字，並呈現於畫面
    * 輸入的值與隨機出現的值要核對
* 左上方有倒數計時;右上方有分數的計算
    * `setInterval`設定倒數
* 設置reload的按鈕
    * 在要加入文字內容的容器中，添加`location.reload()`

## HTML 部分

1.icon來源
[font-awesome - cdnjs](https://www.codegrepper.com/code-examples/c/font-awesome+-+cdnjs.com+-+The+best+FOSS+CDN+for+web)

* fa-cog
![](https://i.imgur.com/uN4HVq7.png)


1.設置難易度選單

 * difficulty為`label`
 * 下拉選單使用`option`
 
 ![](https://i.imgur.com/99JhtOs.png)

```html
<div id="settings" class="settings">
    <form id="settings-form">
      <div>
        <label for="difficulty">Difficulty</label>
        <select id="difficulty">
          <option value="easy">Easy</option>
          <option value="medium">Medium</option>
          <option value="hard">Hard</option>
        </select>
      </div>
    </form>
  </div>


```

## JS 部分

### 1.如何隨機取到已製作好的單字
`words[Math.floor(Math.random() * words.length)]`
* `$('#word').text(randomItem);`讓文字呈現於畫面中

```javascript
const words = [
  'sigh',
  'tense',
  'airplane',
  'ball',
  'pies',
  'juice',
  'warlike',
  'bad',
  'north',
  'dependent',
  'steer',
  'silver',
  'highfalutin',
  'superficial',
  'quince',
  'eight',
  'feeble',
  'admit',
  'drag',
  'loving'
];
```
```javascript
function getRandomword() {
  //https://www.codegrepper.com/code-examples/javascript/get+random+word+from+array+javascript
  randomItem = words[Math.floor(Math.random() * words.length)];
  

  $('#word').text(randomItem);
};

getRandomword()
```


### 2.單字的核對
* 要確認抓到的單字是否一樣
* `let text = $('#text').val(); let Item = randomItem;`
*  `console.log(Item);`查看抓取到的單字
* 設定文字核對一樣，就執行分數加一，以及創造另一組新單字  
* 每次輸入完，並呈現新單字的同時，要清空輸入框的內容
```javascript
//輸入單字核對
function checkWord() {
  let text = $('#text').val();
  let Item = randomItem;
  //console.log(Item);
  if (text === Item) {
    getRandomword();
    updateScore();
    $('#text').val("");
  }
}
```

### 3.倒數計時

* 設置起始時間為10
* `setInterval()`「不斷循環」地固定延遲了某段時間之後，才去執行對應的程式碼，使用 `clearInterval()`来終止
* 在秒數倒計完之後，終止循環，並呈現結束畫面


```javascript
// Init time
let time = 10;
// 倒數計時
//https://jsfiddle.net/satyasrinivaschekuri/y03m54Le/
function countTime() {
  time--;
  $('#time').text(`${time}s`)
  if (time <= 0) {
    clearInterval(downloadTimer);
    gameOver();
  }
  //   } else {
  //     time--;
  //     $('#time').text(`${time}s`)
  //   }
}

var downloadTimer = setInterval(countTime, 1000);
```


#### 4.倒數計時結束，終止畫面

![](https://i.imgur.com/r9oXFmc.png)

* 在該div容器中，插入要顯示的標籤與內容
* 利用`display:flex`呈現該內容


```javascript
//遊戲結束
//location.reload()
function gameOver() {
  $('#end-game-container').html(`<h1>Time ran out</h1>
  <p>Your final score is ${score}</p>
  <button onclick="location.reload()">Reload</button>
  `)
  // 顯示指定的flex容器
  $("#end-game-container").css('display', 'flex');
}
```


### 5.設置難易度

* 設定在表格變動選項後的綁定事件
* 抓取難易度選項的值，並以其值作為儲存local storage的內容
* 接著將儲存的難易度，呈現於畫面`$('#difficulty').val()`

```javascript
//如果儲存端不是空值(已有選擇)，就以此為主，否則的話就是medium
let difficulty = localStorage.getItem('difficulty') !== null
  ? localStorage.getItem('difficulty')
  : 'medium';
  
  
 //設置select的選擇
  $('form').change(function () {
    difficulty = $('#difficulty').val();
    localStorage.setItem('difficulty', difficulty);

  });
  //使畫面的難易度選擇，呈現儲存的選項
  $('#difficulty').val(function () {
    let a = $('#difficulty').val()
    if (localStorage.getItem('difficulty') !== null) {
      return a = localStorage.getItem('difficulty');
    } else {
      return a = 'medium';
    }
  }) 
  
```


### 6.調整難易度，每次打對題目可以增加秒數
* 在`checkWord()`函式中，如過答對題目的條件下設置
* 判斷如果難易度為高、中、低時，可以添加的秒數分別為多少

```javascript
//time += 5;
    if (difficulty === 'hard') {
      time += 2;
    } else if (difficulty === 'medium') {
      time += 3;
    } else {
      time += 5;
    }

//判斷完之後，隨之繼續倒數時間
    countTime();
```
### 7.設定結束時，分數(>=5)，會出現good job圖示

![](https://i.imgur.com/2YKGGsw.png)

* 在css檔案加入show的class，並插入該圖示的顯現方式
* 於js部分，將判斷式，加入`gameOver()`


```javascript
//增加判斷，分數達到一標準，會出現圖示
  let s = score
  console.log(s)
  if (s > = 5) {
    $("#end-game-container").find('img').addClass('show');

  };

```
