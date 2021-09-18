---
title: JS基本功- function函式
date: 2020-12-27 18:55:19
tags: ["JS","parseInt"]
---
### 使用函式可以讓我們執行效率提升，不用重複撰寫，只需幾行語法便可以達到快速的運算、或是功能的執行。以下範例為數羊計數，透過函式以及迴圈，以幫助設定計數的起始以及終點，並快速的執行範圍內的計數。

```javascript
//為什麼我們需要function
    //DRY -> Don't repeat yourself
    function echoSheep(index) {
      console.log('第' + i + '隻綿羊')
    }
    for (var i = 1; i < 11; i++) {
      echoSheep(i)
    }
```

* #### 與過去所學結合

![](https://i.imgur.com/O6BuNU9.png)

* 也可以return後面接物件 
    * 回傳結果為20
![](https://i.imgur.com/ln90sd9.png)

### 簡單練習題
* 創建空陣列，並於陣列中放數1,2,3,...10

![](https://i.imgur.com/9tTJ3uL.png)
要放入return，否則會出現undefined
![](https://i.imgur.com/TGCWt71.png)
![](https://i.imgur.com/lRysWNO.png)

* 也可以將參數改成兩個數

```javascript
function generateArray(a, b) {  //a,b 可以改為 from,to
      var result = [];
      for (var i = a; i <= b; i++) {
        result.push(i);
      }
      return result;
    }
    console.log(generateArray(3, 10))
```

![](https://i.imgur.com/12sURMh.png)


### return
* 函式中一放進return，在函式，return後面的部分就不會運行了

* 不需要知道結果
![](https://i.imgur.com/4oPINY3.png)
* 需要回傳值
![](https://i.imgur.com/yb0arjk.png)


* 參數：函式後面要傳入的值
* 引數：真正傳進去的東西

![](https://i.imgur.com/n66mD1K.png)

* Arguments:為類陣列，屬於物件

## 表達式(Expression)與陳述句(Statement)的差異
* 表達式：會回傳值
* 陳述句：不會回傳值 (if...else,switch，等控制流程)

## 使用函式陳述式(Function Statement)與函式表達式(Function Expression)

* 函式陳述式(Function Statement):在使用函式之前，放入helloFunctionStatement();是可以執行

```javascript
//helloFunctionStatement();  放在前面沒關係
function helloFunctionStatement() {
      console.log('this is Function Statement ');

    }

    helloFunctionStatement();
```
* 函式表達式(Function Expression):反之，要放在宣告之後
```javascript
//helloFunctionExpression(); 放前面，會出現錯誤
var helloFunctionExpression = function () {
      console.log('this is Function Expression ');

    }
    helloFunctionExpression();
```
:::warning
因為hoisting(變數提升)的關係
:::

[JS 原力覺醒 Day07 - 陳述式 表達式](https://ithelp.ithome.com.tw/articles/10218937)

## 變數能夠影響的範圍作用域( Scope 作用域：變數可以影響的範圍 )
* 在function中，所宣告的 var a = 50;，範圍就只有涵蓋在函式中，不會影響到外面

```javascript
 var a = 100;
    function helloWorld() {
      var a = 50;
      console.log(a);
    }
    helloWorld();
    console.log(a);
```
![](https://i.imgur.com/16xE1No.png)

* 如果函式中，宣吿某變數，沒有加var/let，該變數就會影響外部
```javascript
 var a = 100;
    function helloWorld() {
      a = 50;
      console.log('function:', a);
    }
    helloWorld();
    console.log('外部:', a);
```
![](https://i.imgur.com/tJTnR9W.png)

### 全域變數與區域變數
* #### 使用var,let,const，來宣告
* #### 全域變數：在瀏覽器中，沒有在fuction裡，直接進行宣告
    * 也可以用window來存取
    * window，是瀏覽器上的根物件
![](https://i.imgur.com/TNAMSWA.png)

```javascript=
 var y = 1;  //在外面宣告為 全域變數
    function abc() {
      //var,let,const
      var x = 1
    }
```

* #### 區域變數：在function中進行宣告
```javascript=
function abc() {
      //var,let,const
      var x = 1
    }
```
* #### 不是變數 => 全域的屬性
    * z=1;
    * 也可以用window來存取
    
![](https://i.imgur.com/eMBkGKy.png)

## 回呼函數 Callback Function
* 把函數做為參數傳遞
* 當檔案讀取完畢時，請來執行這個 function，並且把結果傳進來
* heyFunc(console.log)->console.log 可以替換別的

```javascript
 function heyFunc(myFunction) {
      myFunction('hi');
      myFunction('hi');
    }
    heyFunc(console.log);
```

![](https://i.imgur.com/imMi0vS.png)

* 非同步（Asynchronous）:回呼常用來延續非同步行動完成後的程式執行

### 其他介紹 [超入門 JavaScript 教學 13 - 回呼函式 callback](https://www.youtube.com/watch?v=U0YbyCaG93k)
 * 以下範例：讓cb接到shout這個函式
![](https://i.imgur.com/zMT2p0j.png)
```javascript
function shout() {
      console.log('hello,i am done')
    }
    function countToSeven(cb) {
      for (var i = 1; i <= 7; i++) {
        console.log(i);
      }
      if (typeof cb === 'fuction')
        cb(); //啟動shout函式
    }
    countToSeven(); //會數1-7
    //要數完七之後，說'hello,i am done
    countToSeven(shout);
    countToSeven(1);//1不是函式，所以在if判斷句終止
    
    //每隔一段時間，幫我做一次
    setInterval(shout, 2 * 1000);//每隔兩秒執行sout
    //在多少時間之後，幫我執行
    setTimeout(shout, 1 * 1000)
```
### js內建函式
* 每隔一段時間，幫我做一次
    `setInterval(shout, 2 * 1000);`//每隔兩秒執行sout
* 在多少時間之後，幫我執行
    `setTimeout(shout, 1 * 1000)`

### 參考資料：
[重新認識 JavaScript: Day 18 Callback Function 與 IIFE](https://ithelp.ithome.com.tw/articles/10192739)
[JavaScript 中的同步與非同步（上）：先成為 callback 大師吧！](https://blog.techbridge.cc/2019/10/05/javascript-async-sync-and-callback/)
[JS20min Day — 18 關於回呼生活化 (Callback)](https://whien.medium.com/js20min-day-18-%E9%97%9C%E6%96%BC%E5%9B%9E%E5%91%BC%E7%94%9F%E6%B4%BB%E5%8C%96-callback-1a112db1a788)

## 匿名函式
* heyFunc裡面的function沒有名稱
```javascript
function heyFunc(myFunction) {
      myFunction('hi');

    }
    heyFunc(function (message) {
      console.log('message is:', message);
    });
//message is:hi
  </script>
```

## 立即函式(IIFE)
>Luka
>![](https://i.imgur.com/sriaR2r.png)

* 它是沒有名字的函式，要立即調用
    * 不讓函式內的變數污染到外面的東係
    * 讓jquery的＄＄變成是jquery使用
```javascript
(function (name) {
        var str = 'Hi ' + name + '!';
        console.log(str);
      })('Sam');
     
```
* 一般常見寫法
```javascript
    var sayHi = function (name) {
      var str = 'Hi ' + name + '!';
      console.log(str);
    }('Peter')//在後面立即執行
      // sayHi("Peter");
```

[[筆記] 談談JavaScript中的IIFEs(Immediately Invoked Functions Expressions)](https://pjchender.blogspot.com/2016/05/javascriptiifesimmediately-invoked.html)
[[筆記] 為什麼我們要用IIFEs(Immediately Invoked Functions Expressions)
](https://pjchender.blogspot.com/2016/05/iifesimmediately-invoked-functions.html)


## hoisting 變數提升淺談

* js的變數與函式的宣告提升

* 不管在哪一行使用了變數，都視為第一行宣告
> 比較好的流程 By 彭彭
> ![](https://i.imgur.com/DcnsGsL.png)

但在js有hoisting 變數提升
* 就算把變數宣告放在後面，依然可以運作(會將`var x`提升到最前面)
> ![](https://i.imgur.com/VmUcFV3.png)

進一步細節...
> ![](https://i.imgur.com/vBfQGUk.png)
電腦解讀如下 
* `var x`會提升
> ![](https://i.imgur.com/0qjf9j8.png)
結果會印出undefined


### 在函示fuction的變數提升
* 先宣告函式，在能呼叫函式執行
> ![](https://i.imgur.com/jSAAaWN.png)
* 在js先呼叫，依然可以運作
>![](https://i.imgur.com/vKZ9eXh.png)

其他狀況...(把函式裝到變數中當作資料)
* 在此情形，它會出現錯誤，回報test不是一個函式
>![](https://i.imgur.com/k2ANLPn.png)
* 程式的解讀 :point_down: 
* 只有var test被提升
> ![](https://i.imgur.com/ZOasHkk.png)

> 程式柴
![](https://i.imgur.com/WuqIKWR.png)

>* 沒有宣告
> * ![](https://i.imgur.com/tlctwnQ.png)


## 常用的內建函式
### Number類型

* #### 字串轉數字

![](https://i.imgur.com/SKn9IWm.png)
* parseInt 是取整數，若遇到如20.35，就只會取到20

![](https://i.imgur.com/MwOQLxK.png)

* 遇到小數點且要保留的時候，使用parseFloat
    * 結果為30.35
    
![](https://i.imgur.com/fVqNBhR.png)

![](https://i.imgur.com/xct9oPf.png)

* `Math.ceil() `=> 無條件進位
* `Math.floor() `=> 無條件捨去
* `Math.round()` => 四捨五入
* `Math.sqrt()` => 開根號
* `Math.pow()` => 次方
* `Math.radom()` => 產生隨機數(0-1，不包含1)
* `toString()` => 數字轉字串
    * 或是加空字串
    
![](https://i.imgur.com/3TGrI7J.png)
 
![](https://i.imgur.com/u9axhKX.png)

### String類型
* `toUpperCase`=> 轉大寫
* `toLowerCase` => 轉小寫
* `indexOf` => 找出字串中，單字的索引（用於檢查某字是否存在字串中）
![](https://i.imgur.com/u51AqqO.png)

* `replace()` => 取代
    * 用正規表達式來選擇 某些字
![](https://i.imgur.com/AboC56u.png)
![](https://i.imgur.com/JQ5KHBI.png)

* `split()` => 切字串
```javascript
  //split較常使用的情形
    //當資料為一連串字串，利用逗號切割 變成陣列，比較好運用
    var str = 'data1,data2,data3,data4';
    console.log(str.split(','));
```
![](https://i.imgur.com/XA193Oc.png)

* `trim()` =>從一個字符串的两端刪除空白字符
```javascript
const greeting = '   Hello world!   ';

console.log(greeting);
// expected output: "   Hello world!   ";

console.log(greeting.trim());
// expected output: "Hello world!";
```

### Array 類型
* [Array.prototype.join()](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/Array/join) 
* 方法會將陣列（或一個類陣列（array-like）物件）中所有的元素連接、合併成一個 '字串'，並回傳此字串(在陣列中每個元素間插入設定的東西)
```javascript
const elements = ['Fire', 'Air', 'Water'];

console.log(elements.join());
// expected output: "Fire,Air,Water" //會變成字串

console.log(elements.join(''));
// expected output: "FireAirWater"

console.log(elements.join('-'));
// expected output: "Fire-Air-Water"
```
* [Array.prototype.map()](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/Array/map)
* 建立一個新的陣列，其內容為原陣列的每一個元素經由回呼函式運算後所回傳的結果之集合。
```javascript
 var arr = [1, 2, 3];
    console.log(
      arr.map(function (x) {
        return x * -1
      })
    )
```
![](https://i.imgur.com/0FKeOro.png)

```javascript=
//也可以接著寫下去
    var arr = [1, 2, 3];
    console.log(
      arr
        .map(function (x) {
          return x * -1
        })
        .map(function (x) {
          return x * 2
        })
    )
```
![](https://i.imgur.com/FrkeTMZ.png)

* [Array.prototype.filter()](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/Array/filter)
* 過濾
```javascript
var arr = [1, 2, 3, -5, 3, -2];
    console.log(
      arr
        .map(function (x) {
          return x * 2
        })
        .filter(function (x) {
          return x > 0
        })
        //把負數過濾掉，留下正數
    )
```
![](https://i.imgur.com/G8TKP1k.png)

* [Array.prototype.slice()](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/Array/slice)
* [Array.prototype.splice()](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/Array/splice)
 * splice 1.插入元素
```javascript
const months = ['Jan', 'March', 'April', 'June'];
months.splice(1, 0, 'Feb');
// inserts at index 1
console.log(months);
// expected output: Array ["Jan", "Feb", "March", "April", "June"]

months.splice(4, 1, 'May');
// replaces 1 element at index 4
console.log(months);
// expected output: Array ["Jan", "Feb", "March", "April", "May"]

```
* splice 2.刪除元素
```javascript
var myFish = ['angel', 'clown', 'drum', 'mandarin', 'sturgeon'];
var removed = myFish.splice(3, 1);

// removed 為 ["mandarin"]
// myFish 為 ["angel", "clown", "drum", "sturgeon"]
```

* [Array.prototype.sort()](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/Array/sort)
```javascript
const months = ['March', 'Jan', 'Feb', 'Dec'];
months.sort();
console.log(months);
// expected output: Array ["Dec", "Feb", "Jan", "March"] //按照第一個字母順序排列

const array1 = [1, 30, 4, 21, 100000];
array1.sort();
console.log(array1);
// expected output: Array [1, 100000, 21, 30, 4] //是一字串，以第一個數字來排列

```
* 依照數字大小排列
```javascript
   var arr = [1, 30, 4, 2];
    arr.sort(function (a, b) {
      if (a === b) return 0;
      if (b > a) return -1; //不換
      return 1; //正數，換位置
    })
    console.log(arr);
```
![](https://i.imgur.com/dMAXe66.png)


####  「回傳」與「印出」的差異
* 函式可以透過 return 回傳函式運算完的結果
>  述程式碼的執行：
1.`console.log(add(1, 2))` => 帶入functoin，`console.log(a, b);` => 印出1,2
2.往下，到return undefined; 然後進入add(1, 2)的回傳值，就會是undefined
```javascript
function add(a, b) {
      console.log(a, b);
      return undefined;  //不加return，預設會是undefined
    }

    console.log(add(1, 2))

   
```
* 以為 `add(1, 2)` 的回傳值是 3，但不是，那是因為它在裡面 console.log() 所以把結果印出來了。  
```javascript
function add(a, b) {
   console.log(a + b)
 }
 add(1, 2)
```
* 如果真的要有回傳值的話要這樣寫：
```javascript
function add(a, b) {
   return a + b
 }
 console.log(add(1, 2))
```

參考資料： [JavaScript 初心者筆記: 函式實際運作 - 回傳值與函式間互相傳遞](https://ithelp.ithome.com.tw/articles/10214256)
,[後設鐵人 Day4：請幫我簽個名好嗎？](https://ithelp.ithome.com.tw/articles/10214364)

### Immutable 觀念 (不可變)
* 除了物件、陣列以外，其他都是不可變的
* 沒辦法改它，就只能回傳新的
```javascript
 var a = 'hello';
    a.toUpperCase;
    console.log(a);
    //印出：hello，沒有改變原本變數的內容
```
```javascript
   var a = 'hello';
    a = a.toUpperCase(); //要回傳給a，把原本的a值蓋掉
    console.log(a);
    
    //或是增設一個新變數
    var a = 'hello';
    var b = a.toUpperCase(); 
    console.log(b);
    
```
