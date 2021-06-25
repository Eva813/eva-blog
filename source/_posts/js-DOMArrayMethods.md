---
title: JS實作- DOM Array Methods
date: 2021-01-15 22:19:25
tags: ["js","Array"]
---
[成品](https://eva813.github.io/Eva_portfolio/ArrayMethods/arrayMethod.html)
![](https://i.imgur.com/gtaxVol.png)


# 需求觀察
* 使用fetch來串接API
* 透過左側按鈕，使用陣列的方法來實現不同事件
    * 運用` forEach()`來將陣列資料呈現到畫面
    * 運用`map()`計算金額的倍數
    * `filter() `篩選出符合條件的資料
    * `sort()`用來排序資料
    * `reduce()`計算陣列中資料的累加結果


# 使用fetch來串接資料
* 實作專案的資料來源`https://randomuser.me/api`

1.原本使用`fetch`
* 送出 Request，如果得到回應就會回傳帶有 Response 的 Promise 物件，使用 then 將回傳值傳遞下去。
```javascript
 function getRandomUser() {
 fetch('https://randomuser.me/api').then(response =>    response.json()).then(data => data)
 }

```
[JavaScript Fetch API 使用教學](https://www.oxxostudio.tw/articles/201908/js-fetch.html)

2.Fetch 搭配 async、await
* 使用async很容易：在定義function時，開頭加上 async
* 用fetch取得資料

```javascript
  async function getRandomUser() {
  const res = await fetch('https://randomuser.me/api');  //執行完後發出promise
  const data = await res.json();
  //查看取得的資料，並從中選擇要用的部分
  //console.log(data);
  const user = data.results[0];

  //創造新的使用者，並建立新的物件
  const newUser = {
    name: `${user.name.first} ${user.name.last}`,
    money: Math.floor(Math.random() * 1000000)
  };
  console.log(newUser);
  addData(newUser);
}

```

3.函式中的撰寫
* 查看取得的`data`
![](https://i.imgur.com/TjYFhjC.png)
* 變數宣告要取用資料的哪個部分，並用以建立新物件的內容
    * `const user = data.results[0];`
    * `user.name.first`
![](https://i.imgur.com/NAmWK88.png)

## 將物件傳入陣列

* data 為前面
```javascript

//將新建立的物件(newUser)放入陣列中
function addData(newObj) {
  //這裡的data是在最前面所宣告的 初始陣列
  data.push(newObj);
  
    //不放入參數，使用預設data
  updateDOM();


}

```
## 將傳入新物件的陣列，顯示於畫面

* 使用`forEach()`來呈現畫面的資料
    * 傳入參數為item，為新創的物件資料，內有：name,money
    * 創造新的`div`並增加class給他
    * 用`innerHTML`插入內容
    * 用 appendChild() 把上面新增的內容放入，新增的內容會依序排列在後面，不會被洗掉

```javascript
//將新創立的物件輸出呈現到畫面上(dom)
function updateDOM(provideData = data) {
  console.log(provideData);
  //清除main區域的div
  main.innerHTML = `<h2><strong>Person</strong>  Wealth</h2>`;
  provideData.forEach(item => {
    const element = document.createElement('div');
    //在新div增加class
    element.classList.add('person');
    element.innerHTML = `<strong>${item.name}</strong> ${item.money}`;
    main.appendChild(element);
  })
}
```
{%note info%}
注意：如果本來 HTML 某元素有既有的內容，但是又用 innerHTML 在同樣的元素上加上內容的時候，innerHTML 會把原本寫的東西覆蓋掉。
{%endnote%}

![](https://i.imgur.com/xjxIGr7.png)

* 注意參數的回傳
```javascript
function updateDOM(provideData) {
  console.log(provideData);
}

```
![](https://i.imgur.com/0O0629Q.png)
```javascript
function updateDOM(provideData = data) {
  console.log(provideData);
}
```

![](https://i.imgur.com/qW5Vqq9.png)

[JavaScript 初心者筆記: DOM - 如何用 JS 新增 HTML 內容](https://ithelp.ithome.com.tw/articles/10218607)

## 數字轉金錢格式

![](https://i.imgur.com/JKQrEyd.png)

```javascript
//將回傳的隨機數字，轉換為金錢格式 -https://stackoverflow.com/questions/149055/how-to-format-numbers-as-currency-strings
function formatMoney(Money) {
  return '$' + Money.toFixed(2).replace(/\d(?=(\d{3})+\.)/g, '$&,');;
}

```
* ``formatMoney()``要放入剛剛forEach所傳入的金額，從這邊去調整數字的格式
```javascript
element.innerHTML = `<strong>${item.name}</strong>  ${formatMoney(item.money)}`;
```

![](https://i.imgur.com/8DLGtgC.png)


## 增加人員事件
```javascript
//設置點擊事件，按下add user，增加人
addUserBtn.addEventListener('click', getRandomUser);
```

## 運用`map()`來，建立 double money事件
* loop through array 就像forEach，但不同的是他會回傳東西
```javascript
//double money 
function doubleMoney() {
  data = data.map(item => {
    return { ...item }

  });
  console.log(data);
 
}
```
![](https://i.imgur.com/PpZ5v7v.png)

```javascript
//double money 
function doubleMoney() {
  data = data.map(item => {
    return { ...item, money: item.money * 2 }

  });
  console.log(data);

}

```
![](https://i.imgur.com/joXfDXT.png)

呈現於畫面

```javascript
//double money 
function doubleMoney() {
  data = data.map(item => {
    return { ...item, money: item.money * 2 }

  });
  console.log(data);
   updateDOM();
}

```

![](https://i.imgur.com/M0R5KV4.png)


{%note info%}
`forEach()`: 针对每一个元素执行提供的函数(executes a provided function once for each array element)。
`map()`: 创建一个新的数组，其中每一个元素由调用数组中的每一个元素执行提供的函数得来(creates a new array with the results of calling a provided function on every element in the calling array)。
[JavaScript中Map和ForEach的区别](https://blog.fundebug.com/2018/02/05/map_vs_foreach/)
{%endnote%}

## 運用`sort`來建立排名

```javascript

//sortEvent
function sortEvent() {
  data = data.sort((a, b) => {
    return b.money - a.money;
  });
  //console.log(data);
  updateDOM();
}
```

{%note info%}
`sort`依據字串的 Unicode 編碼進行排序，會改變原本的陣列。
* 排序進行方式：`sort()`會將所有元素轉成字串後，且以第一個字元為對象，再進行排序。
* 比較好的方式：一般會建議，還是以函式傳入參數來當排序條件會比較穩定。


```javascript
const arr = [5, 9, 1, 3, 2, 6];
// 以匿名函式回參數做「升序」排序
arr.sort(function(a, b) {
  return a - b; // a - b > 0
});
// [1, 2, 3, 5, 6, 9]



// 如果要反過來做「降序」排序
arr.sort(function(a, b) {
  return b - a;
});
// [9, 6, 5, 3, 2, 1]

```


{%endnote%}
參考資料：[JS 將陣列 Array 重新排列的 sort()](https://ithelp.ithome.com.tw/articles/10225733)、[淺談 JS sort() 到背後排序方法](https://medium.com/@leokao0726/%E6%B7%BA%E8%AB%87-js-sort-%E5%88%B0%E8%83%8C%E5%BE%8C%E6%8E%92%E5%BA%8F%E6%96%B9%E6%B3%95-1035f5b8cde8)

## `filter`篩選百萬以上的人員
```javascript
function filterMillionaires() {
  data = data.filter((item) => {
    return item.money > 1000000;
  });

  updateDOM();

}
```

{%note info%}
`filter()`的使用是回傳新陣列，原陣列不改變
{%endnote%}


## reduce 
拆解內部參數
accumulator
用來累積回呼函式回傳值的累加器（accumulator）或 initialValue（若有提供的話，詳如下敘）。累加器是上一次呼叫後，所回傳的累加數值。
currentValue
原陣列目前所迭代處理中的元素。


* `console.log(accumulator);`

![](https://i.imgur.com/dGpG42b.png)
* ` console.log(currentValue);`

![](https://i.imgur.com/P0yQK34.png)
* console.log(currentValue.money);

![](https://i.imgur.com/vNfFozL.png)

[MDN](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/Array/Reduce)
[JavaScript reduce 在做什麼？](https://w3c.hexschool.com/blog/a2cb755f)

### 理解完`reduce()`所回傳的參數資料後...

* 讓金額加總計算`accumulator += currentValue.money`

```javascript
//calculateMoney
function calculateMoney() {
  const reduceArr = data.reduce((accumulator, currentValue) => {
    //console.log(accumulator);
   // console.log(currentValue.money);
    return accumulator += currentValue.money;
  }, 0);


  const totalEl = document.createElement('div');
  totalEl.innerHTML = `<h3>Total Wealth: <strong> ${formatMoney(reduceArr)} </strong> </h3> `;
  main.appendChild(totalEl);
}

```
