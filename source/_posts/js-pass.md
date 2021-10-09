---
title: JS基本觀念 - 認識 pass by value、pass by reference 、 pass by sharing，Javascript 又是哪一個？
date: 2021-10-09 12:18:41
tags: ["JS"]
---

![](https://i.imgur.com/zT0UU5c.jpg)


之所以會撰寫這篇文，是因為面試過程被考倒的觀念，才發現自己過去從沒注意到這部分啊，那就來寫篇文章來認識它們之中的傳遞方式。

## 首先，先從型別的認識開始

在Javascript分兩大類，一種是基本型別(primitive type)，另一種是物件(Object)。
* Primitive type （以純值的形式存在）
Boolean
Null
Undefined
Number
BigInt
String
Symbol（於 ECMAScript 6 新定義）
* Object
物件型別指的是可能由零或多種不同型別 (包括純值與物件) 所組合成，例如object,array, function, map

知道型別後，可以簡易的分類：
* primitive type會是 pass by value，
* object 是 pass by reference。

## 接下來來觀察，它們之間不同

### pass by value 

範例1：
```javascript
let x=10;
let y=x;

x=20;
console.log(x); //20
console.log(y); //10

```
* 注意：x和y是兩個獨立變數 （先記著這點）
    * 值會存入該變數
 `var y = x;` 看起來會像是y的內容要複製x，但可以的話要理解為，變數 y 是去建立了一個新的值，然後將變數 x 的內容複製了一份過來。
* 因為兩的變數，各自獨立，所以當變數 x 的內容後來經過更新變成 20 之後，變數 y 的內容依舊保持原來的 10 而不受影響。


範例2
```javascript
var num=3;
console.log("num start:",num);

function passByValue(func_num){
  func_num=5;
}

passByValue(num);

console.log("num end:", num);

```
#### 結果：
```javascript
num start:3
num end: 3

```
* 先是宣告新變數
* 隨後建立`passByValue()` 函式
* 呼叫`passByValue(num)` 複製變數num的值，3傳入`passByValue(func_num)`。
    * 一開始 值會是3
    * 遇到`func_num=5;` => 將值改為 5
* 但因為出去了函式範圍(scope)，最終的值 `num end:3`


### pass by refrence

範例
```javascript
let x={value:10};
let y=x;

x.value=20;
console.log(x); //{value:20}
console.log(y); //{value:20}
console.log( x === y );  //true

```

### 拆解說明一下

* 當宣告一個物件
* JavaScript 會在記憶體的某處建立起一個物件 (圖右側)，然後再將這個 `x`變數指向新生成的物件

![](https://i.imgur.com/LPwkZAg.png)

* 接著，當我們宣告了第二個變數 y ，並且透過 = 將y 指向 x 的位置。
* 接著當我們更新了 x.value 的內容後， y.value 的內容也被更新了。

![](https://i.imgur.com/vFUhP6X.png)


範例2
```javascript
var obj1={item:"unchanged"};

console.log("obj1 start:",obj1);

function passByReference(ref){
  ref.item= "changed";
}

passByReference(obj1);

console.log("obj end", obj1);

```
#### 結果
```javascript
obj1 start:{item: "unchanged"}
obj1 end:{item:"changed"}

```

* 當執行passByReference(obj1) 。想像他是個地址(0x0016)，進入函式中將地址複製，傳入。
    * 此時他的value 是個地址(0x0016)
* 進入函式，遇到`ref.item`
    * de-reference ，進入原本的記憶體位置，找到item，並改變他的值


![](https://i.imgur.com/3Ytn7bk.png)

#### 在不一般情況下，基本型別是 pass by value，而物件型別是 pass by reference的方式，但總有例外的時候。


### pass by  sharing

```javascript
var obj1={item:"unchanged"};

console.log("obj1 start:",obj1);

function passBySharing(ref){
  ref={ item: "changed"};
}

passBySharing(obj1);

console.log("obj end", obj1);

```

```javascript
obj1 start:{item: "unchanged"}
obj1 end:{item:"unchanged"}

```
* 傳入之前start 沒有改變
* 呼叫函式，並進入`passBySharing()`，還是複製地址，傳入
* 遇到`ref={ item: "changed"};`，會直接覆蓋地址(有點類似pass by value)
    * 這並不是直接更改物件特性


![](https://i.imgur.com/Kst3565.png)

## 最後，來說說 JavaScript 屬於？
看了多篇文章，實在也是有點混亂，該認為三種形式都有呢？還是就是Pass by sharing、Pass by reference呢？ 那例外情形又該如何解釋？

所以這邊的結尾，直接引用Kuro、Huli老師文章的資訊，供給大家參考。

從Kuro Hsu 的[文章](https://ithelp.ithome.com.tw/articles/10191057)、[書](https://www.tenlong.com.tw/products/9789864344130)
提及認為 JavaScript 應該更屬於 Pass by sharing 的形式。
* JavaScript 不屬於單純的傳值或傳址。

參考 ECMA-262-3 in detail. Chapter 8. Evaluation strategy
> Regardless of usage concept of reference in this case, this strategy should not be confused with the “call by reference” discussed above. The value of the argument is not a direct alias, but the copy of the address.
> 由於在 JavaScript 的物件類型是可變的 (mutable)，當物件更新時，會影響到所有引用這個物件的變數與其副本，修改時會變動到原本的參考，但當賦與新值時，會產生新的實體參考。

另外在 Huli 的文章中：
> 依據細分程度的不同，下面幾句話都是正確的：
JavaScript 裡面只有 pass by value
JavaScript 的 primitive type 是 pass by value，object 是 pass by sharing

 
---
## 最終來個綜合練習：
相信在最後的這個練習，可以更清楚，pass by value，pass by reference，pass by sharing

```javascript
function changeStuff(num,obj,obj2){
  num=num*10;
  obj.item="changed";
  obj={item:"changed"};
}

var num=10;
var obj={item: "unchanged"};
var obj2= {item: "unchanged"};

changeStuff(num, obj, obj2);
console.log(num);
console.log(obj.item);
console.log(obj.item);
```
```
console=> 10 "changed" "unchanged"
```

參考資料：
[JS基本觀念：call by value 還是reference 又或是 sharing?](https://medium.com/@mengchiang000/js%E5%9F%BA%E6%9C%AC%E8%A7%80%E5%BF%B5-call-by-value-%E9%82%84%E6%98%AFreference-%E5%8F%88%E6%88%96%E6%98%AF-sharing-22a87ca478fc)
[重新認識 JavaScript: Day 05 JavaScript 是「傳值」或「傳址」？](https://ithelp.ithome.com.tw/articles/10191057)
[Tech Talk: Pass By Sharing with Javascript](https://www.youtube.com/watch?v=1YFss_4B_o4&t=302s)
[深入探討 JavaScript 中的參數傳遞：call by value 還是 reference？](https://blog.techbridge.cc/2018/06/23/javascript-call-by-value-or-reference/)
