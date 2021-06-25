---
title: JS基本功- 物件與陣列
date: 2020-12-27 21:17:13
tags: ["js","array","object"]
---
>程式柴
>![](https://i.imgur.com/44kqCRM.png)
> ### Function、array都屬於物件
>![](https://i.imgur.com/1bYo7eM.png)

##  物件的例子
```javascript
var person = {
      name: ['Bob', 'Smith'],
      age: 32,
      gender: 'male',
      interest: ['music', 'movie'],
      greeting: function () {
        console.log('hi I\'m' + this.name[0] + '.');
      }
    }
```
## 物件的創建
`var x={}`
* 有一隻狗,其名字、年齡，函式
```javascript
var dog = {
      name: 'tracy',
      age: 20,

      bow: function () {
        console.log('bow!bow!');
      }
    }
```
* 用.呼叫屬性，或是dog['key'] :point_down: 
![](https://i.imgur.com/OgrYwjF.png)
* 物件內的函式
![](https://i.imgur.com/jOWuwxQ.png)

## 添加屬性至物件上
* #### 增加值的方式
    * ` dog.color = 'white';`
    * `dog['size'] = 'small';` =>較常用
* #### 增加函式 => 一樣用.的方式
    * `dog.bowbow = function () {
      console.log('bow!bow!～～') }`

```javascript
 var dog = {
      name: 'tracy',
      age: 7,
      bow: function () {
        console.log('bow!');
      }
    }

    //增加值的方式
    //1
    dog.color = 'white';
    dog.eyes = 'big';
    //2
    dog['size'] = 'small';
    //增加函式
    dog.bowbow = function () {
      console.log('bow!bow!～～')
    }
```

![](https://i.imgur.com/4qqgZRy.png)




## 陣列
>程式柴
>![](https://i.imgur.com/FdH3fty.png)
* 陣列用 中括號` [ ] `
* 陣列的計算是從零開始（取陣列的索引）
    * 呼應for 迴圈，從0開始，從索引第一個
```javascript
arr = [111, 222, 333]
    console.log(arr);
    console.log(arr[1]); //222
    //呼應for 迴圈，從0開始，從索引第一個
```
* 陣列中的東西都是相同屬性，比較好操作
```javascript
arr2 = ['dog', 'cat', 'sth']
    console.log(arr.length); //3
  arr3 = [111, 'cat', 'sth']
```
![](https://i.imgur.com/2WztSsO.png)

* 練習：要紀錄10位學生的分數，並加總
```javascript=
var score = [1, 3, 4, 5, 100];
    console.log(score);
```
### 陣列基本操作 -> `pop` 與`push` 較常用
* `score.push(1000)` -> 新增元素到現有陣列中，加入尾端
* `score.unshift(888)` -> 加入陣列最前面
* pop:從最末端抽走元素
 ![](https://i.imgur.com/e55Oxbz.png)

```javascript
var myHeros=['孫悟空', '佐助', '女超人', '美國隊長'];
//從最末端抽走元素
myHeros.pop();

//從頭抽走元素
myHeros.shift();
console.log(myHeros);
```


### 陣列替換元素

* 在原本陣列中，選取要取代的該元素，後面加=，並輸入內容

![](https://i.imgur.com/gnVybmt.png)

## 陣列取得最後一個元素
* 該陣列長度-1，就可以取得最後一個元素
    * 長度為七，最後一個的索引是6 :point_down: 
![](https://i.imgur.com/Ytlzihy.png)

##  用 indexOf 取得元素的索引

* Of的o要大寫

```javascript
var myHeros=['孫悟空', '佐助', '女超人', '美國隊長'];
//檢查誰誰誰有在陣列中嗎
console.log(myHeros.indexOf('達爾')); //印出-1，意指此元素不存在陣列中
console.log(myHeros.indexOf('美國隊長')); //印出3（在陣列中第幾個索引
```

### 切片 slice 與方法

![](https://i.imgur.com/h3ltQ3Q.png)

```javascript
 var arr = ['a', 'b', 'c', 'd', 'e', 'f', 'g'];
    //用索引
    console.log(arr.slice(2, 4));//不包含最後一個
    console.log(arr.slice(2, 5));
```
![](https://i.imgur.com/bzKmwQi.png)

* 複製陣列
```javascript
var shallowCopy = fruits.slice(); // 這就是複製陣列的方式
// ["Strawberry", "Mango"]
```

[MDN slice](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/slice)
[MDN array](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/Array)
[JavaScript Array 陣列操作方法大全 ( 含 ES6 )](https://www.oxxostudio.tw/articles/201908/js-array.html)


## 陣列與物件
* 例子：被老師要求要記錄，學生的名字,分數,地址...
* 以陣列的想法，會將各類別分類，放入同屬性的結果
    * 學生的分數,名字個一個陣列
 ![](https://i.imgur.com/6DP47iL.png)
    * 要取得同一位學生的所有資料，要個別從矩陣中拿取
![](https://i.imgur.com/5q4VitE.png)

* 一個資料集合，就能代表一個學生 -->物件
    * 以下面的資料結構來代表學生的一些屬性
![](https://i.imgur.com/Gk5T01w.png)
---
### 補充 
* 物件與陣列，與等號的關係,理解
    * 下方的結果都是false
![](https://i.imgur.com/6L0AI2n.png)

因為：
* `var obj=[a:1]`，會將它放在某一個記憶體位置
* `var obj2=[a:1]`，會將它放在另一個記憶體位置
{%note info%}
即便內容,數值一樣，但js判斷的是記憶體位置不一樣
{%endnote%}
