---
title: JS實作- SortableList
date: 2021-01-15 22:10:22
tags: ["js","drag&drop"]
---
[成品](https://eva813.github.io/Eva_portfolio/sortableList/sortableList.html)
## 觀察功能需求
* Drag & Drop API
    * 使元素可以拖放，在 HTML5，只要為它加上 draggable="true" 屬性，它就能「被拖曳」了

* 拖曳該項目時，會使下方的格子顏色變成灰色

![](https://i.imgur.com/sZVXh9r.png)
* 拖曳項目到目標位置，會使了者交換
* 排序最後會有一個檢查順序正確與否的按鈕

## js部分
### 1.排行榜的設置
* 設定熱門旅遊縣市排行榜的陣列
* 並宣告一個空陣列，以利後面使用

```javascript
const bestPlace = [
  '台南',
  '台中',
  '高雄',
  '台東',
  '屏東',
  '新北',
  '宜蘭',
  '花蓮',
  '澎湖',
  '桃園'
];


const listItems = [];
```
### 2.創建排行列表，並將其呈現於頁面上
* 宣告新的變數，並利用Fisher-Yates，創造隨機的效果
    * 注意新變數，是帶入展開運算元(把陣列中的元素取出)
* 利用`each`跑陣列，創造變數填入`<li>`，並於其中填入html標籤，使畫面呈現列表。
* jquery稱增加屬性`listItem.setAttribute('data-index', index);`,[.attr()](https://api.jquery.com/attr/)

![](https://i.imgur.com/OqjHMda.png)

* `listItems.push(listItem);`將列表項目，放入空陣列中

```javascript
//Fisher-Yates洗牌演算法
function fisherYatesShuffle(arr) {
  for (var i = arr.length - 1; i > 0; i--) {
    var j = Math.floor(Math.random() * (i + 1)); //random index
    [arr[i], arr[j]] = [arr[j], arr[i]]; // swap
  }
}
/製作實際可以看到的list
function creatList() {
  let placeArr = [...bestPlace];
  fisherYatesShuffle(placeArr);
  $.each(placeArr, function (index, place) {
    const listItem = $('<li></li>').appendTo('#draggable-list');
    // console.log(listItem);
    listItem.attr("data-index", index);
    //listItem.addClass('wrong')
    listItem.html(`<span class="number">${index + 1}</span>
        <div class="draggable" draggable="true">
          <p class="place-name">${place}</p>
          <i class="fas fa-grip-lines"></i>
        </div>`)


    listItems.push(listItem);
    //insert into dom
    $('#draggable-list').append(listItem);
  });

  addEventListeners()
}
```
{%note info%}
參考資料：
push：
[JS 從陣列 Array 尾端新增元素的 push()](https://ithelp.ithome.com.tw/articles/10222922)
[JavaScript Array 陣列操作方法大全 ( 含 ES6 )](https://www.oxxostudio.tw/articles/201908/js-array.html)
{%endnote%}
### 3.拖曳的監聽事件
* 拖曳開始的監聽，元素是抓取`draggables`，後續的drop、dragenter、dragover等事件，元素是`.draggable-list li`
* 針對要被拖曳的元素（dragSource）監聽事件
* 利用forEach方式，允許多個可拖曳的物件
- 抓取的元素：
![](https://i.imgur.com/5G60Pmr.png)
![](https://i.imgur.com/zXR4YWT.png)


```javascript
//監聽事件
function addEventListeners() {
  const draggables = document.querySelectorAll('.draggable');
  const dragListItems = document.querySelectorAll('.draggable-list li');

  //Dragable dot 增加監聽事件

  draggables.forEach(draggable => {
    draggable.addEventListener('dragstart', dragStart);
  });
  dragListItems.forEach(item => {
    item.addEventListener('dragover', dragOver);
    item.addEventListener('drop', dragDrop);
    item.addEventListener('dragenter', dragEnter);
    item.addEventListener('dragleave', dragLeave);
  });

}
```
[[筆記] 製作可拖曳的元素（HTML5 Drag and Drop API）](https://pjchender.blogspot.com/2017/08/html5-drag-and-drop-api.html)


### 4.針對監聽事件，設置各執行的函式
* 為了能夠追蹤抓取項目，所以一開始先宣告一個索引變數。
* `dragStart()`:宣告`dragStartIndex`變數為抓取到的`li`->`data-index`

![](https://i.imgur.com/6tMHuYr.png)

* 有拖曳的開始，就會有結束，也就是放置(`dragDrop()`)：`dragEndIndex`同樣也是以索引來作為依據，並在設置一個交換的函式


```javascript
//要能夠持續追蹤每個項目的index，所以要有個初始變數
let dragStartIndex;
//拖拉的各函式

function dragStart() {
  //console.log('Event: ', 'dragstart');
  dragStartIndex = +$(this).closest('li').attr('data-index');

  console.log(dragStartIndex);
};
function dragOver(e) {
  // console.log('Event: ', 'dragover');
  e.preventDefault();
}
//設定開始的index,以及結束的index是為了交換
function dragDrop() {
  // console.log('Event: ', 'dragdrop');
  const dragEndIndex = +$(this).attr('data-index');
  swapItems(dragStartIndex, dragEndIndex);
  $(this).removeClass('over');
}

```

### 5.交換索引，使物件交換位置`swapItems`
* 透過抓取到的開始與結束的索引，來填入要交換的變數

```javascript
function swapItems(fromIndex, toIndex) {
  //要設定交換之前，要先到dragover去設定e.preventDefault();（因為dragover會擋到交換的執行）

  const itemOne = listItems[fromIndex].find('.draggable');
  const itemTwo = listItems[toIndex].find('.draggable');

  //console.log(itemOne, itemTwo)
  listItems[fromIndex].append(itemTwo);
  listItems[toIndex].append(itemOne);
}
```

### 6.延續拖曳監聽事件，所要執行的其他函式

* `dragEnter()`拖曳元素進入要放置的目標位置，所觸發的事件。在此是設定要放入目標位置時，增加一個`class`的效果

* 進入目標位置，其相互搭配的事件就是離開`dragLeave()`，當拖曳元素離開放置位置時，所發生的事件。在此為設定，去除於原本`class`的效果。

```javascript
function dragEnter() {
  // console.log('Event: ', 'dragenter');
  $(this).addClass('over');
}
function dragLeave() {
  // console.log('Event: ', 'dragleave');
  $(this).removeClass('over');
}

```

### 7.核對順序
* 抓取listItems，來跑forEach，其要參照的參數是，項目、索引。
* 宣告`placeName`為參數`listItem`，draggable的文字字串。
    * trim()用來去除字串前後的空白
* 判斷句`placeName !== bestPlace[index]`
* `console.log(bestPlace[index])`會印出，正確的順序。

![](https://i.imgur.com/xIR3X98.png)

* 核對結果：就是分別加入`wrong`、`'right'`。
```javascript
//核對排名順序
function checkOrder() {
  listItems.forEach((listItem, index) => {
    const placeName = listItem.find('.draggable').text().trim();

    if (placeName !== bestPlace[index]) {
      listItem.addClass('wrong');
    } else {
      listItem.removeClass('wrong');
      listItem.addClass('right');
    }
  });
}
```

---
## 以下為補充資料：

### 拖曳補充資料：
[Top 5: Best Sortable & Draggable List JavaScript and jQuery Plugins](https://ourcodeworld.com/articles/read/664/top-5-best-sortable-and-draggable-list-javascript-and-jquery-plugins)
[實用網頁工具庫 - jQuery UI (上) 元件篇
](https://ithelp.ithome.com.tw/articles/10120587)


### 使陣列隨機排序
* 使用`map`將陣列轉換為新陣列，創造vale、以及加上sort排序值(利用`Math.random()`來產生亂數)
* 使用sort隨機排序，剛剛的sort值是隨機產生，再一次使用sort，來使排的順序也隨機。
* 最後，因為排序後陣列中的物件是`{ value: , sort:  }`，而我們要讓畫面只顯示`value:`，所以再使用`map`將新陣列轉回只有該資料的陣列。
```javascript
function createList() {
  [...richestPeople]
    .map(a => ({ value: a, sort: Math.random() }))
    .sort((a, b) => a.sort - b.sort)
    .map(a => a.value)
    .forEach((person, index) => {
      const listItem = document.createElement('li');

      listItem.setAttribute('data-index', index);

      listItem.innerHTML = `
        <span class="number">${index + 1}</span>
        <div class="draggable" draggable="true">
          <p class="person-name">${person}</p>
          <i class="fas fa-grip-lines"></i>
        </div>
      `;

      listItems.push(listItem);

      draggable_list.appendChild(listItem);
    });

  addEventListeners();
}
```
[[筆記] 如何正確實作 JavaScript Array Random Shuffle 亂數排序演算法](https://shubo.io/javascript-random-shuffle/)
[在 JavaScript 中對一個陣列進行隨機化或洗牌
](https://www.delftstack.com/zh-tw/howto/javascript/shuffle-array-javascript/)

---

### jquery ui

```javascript
//https://stackoverflow.com/questions/6426795/what-is-disableselection-used-for-in-jquery-ui
function dragdrop() {
  $("#draggable-list").sortable({
    //connectWith: "ul",
    delay: 300,
    distance: 15,
    //number: "span:not(.number)",
    iframeFix: true,

  }).disableSelection();

}
```


--- 

### 顏色
[0-255](https://www.0to255.com/f2e8cf)
[漸層顏色](https://webgradients.com/)
[colors](https://coolors.co/palettes/trending)
[Why is my JQuery selector returning a n.fn.init[0], and what is it?](https://stackoverflow.com/questions/34494873/why-is-my-jquery-selector-returning-a-n-fn-init0-and-what-is-it)
