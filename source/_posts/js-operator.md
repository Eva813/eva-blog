---
title: JS基本功- 運算元與運算子
date: 2020-12-26 18:35:07
tags: ["js","parseInt"]
---
## 比較運算子
* 運算符號，使用`>`,`<`,`=`來做判斷

![](https://i.imgur.com/XPfa6QC.png)

### 三個等於與兩個等於 有什麼不一樣

* 只有一個等於：`var x=1;` -> 賦值運算子
* 所以比較運算值的時候，會使用兩個等於以上

#### 兩個等於
* 不是嚴格的比較
* 轉換過的值是相同的，就會符合

![](https://i.imgur.com/aXRMO9v.png)
#### 三個等於
* 實務常用，且較為嚴謹
* 字串需經過轉換為數值，進行比較  

![](https://i.imgur.com/qFnbPBt.png)

## 算數運算子

* `+`,`-`, `*` ,`/`
* 先乘除後加減
* `parseInt(10/3)` ->取整數
    * 除了取整數外，也將文字轉數值

![](https://i.imgur.com/GDNiVIa.png)


### 餘數與被除數
* 取餘數：`5%3` -->2 指5對3取餘數為2
* [重新認識 JavaScript: Day 06 運算式與運算子](https://ithelp.ithome.com.tw/articles/10191180) | [MDN運算式與運算子](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Guide/Expressions_and_Operators)

## 邏輯運算子
* #### 用於結合流程判斷：兩件事同時發生，要兩個都符合、或是達成其中之一

### AND (&&)
{%note info%}
運算式1 ``&&`` 運算式2
* 假如 運算式1 可以被轉換成 false的話，回傳 運算式1; 否則，回傳 運算式2。 因此，`&&`只有在 兩個運算元都是True 時才會回傳 True，否則回傳 false。
{%endnote%}

### OR(`||`)
{%note info%}
運算式1 || 運算式2
* 假如 運算式1 可以被轉換成 true的話，回傳 運算式1; 否則，回傳 運算式2。 因此，||在 兩個運算元有任一個是True 時就會回傳 True，否則回傳 false。
{%endnote%}

* 以布林值為例 
 在OR中，只要有其中一個為true，就會回傳true
 
![](https://i.imgur.com/MVdrWHD.png)

* 運算子的判斷 

![](https://i.imgur.com/QrVIaVk.png)

* `x-y` 是正確的，再往下執行`(y-1)` 回傳1`(2-1) `

![](https://i.imgur.com/ePTB51j.png)

* 執行`x-y`時，就確定它有值了，所以先行回傳3 

![](https://i.imgur.com/jDgoWtM.png)

* 在運算式中第一個值為false，於`&&`,`||`

![](https://i.imgur.com/ozAgEpK.png)

* 其他範例： 

{%note info%}
z沒有設定值，就會被設定一個預設值 ->變數沒有值的時候，給他預設值

n已經有值，就會回傳他自己的值

![](https://i.imgur.com/GCQrL1J.png)
{%endnote%}


### NOT (!)

![](https://i.imgur.com/AgVDowW.png)



清楚介紹：[MDN](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Guide/Expressions_and_Operators#%E9%82%8F%E8%BC%AF%E9%81%8B%E7%AE%97%E5%AD%90)

## 三元運算子
* 有三個運算元
* 如果 ？ 我就 ： 不然 (如果我有1千萬 ? 我就 ： 否則)
    * `1>=3`是false，所以回傳b 

![](https://i.imgur.com/zx1ABJ0.png)

![](https://i.imgur.com/kWSDA5Q.png)

* 也同等於if else

![](https://i.imgur.com/e4e2VMo.png)

## 賦值運算子與次方(+= 系列)

* ### +=運算

```javascript
 console.log('賦值運算子')
    var x = 1;
    console.log('x=', x); //1
    x += 1;
    console.log('x+=1 等於', x); //2
    //x=x+1
    var y = 10;
    y -= 2;
    //y=y-2
    console.log(y);
    x *= 5;
    console.log('x*=5 等於', x); //10
    x /= 2;
    //x=x/2
    console.log('x/=2等於', x) //5
    ////////////////////////
    var z = 35;
    z %= 10;
    // z = z % 10;
    console.log('z %=10', z);
```
* ### 次方運算

```javascript
   //次方
    /////////
    var a = 2;
    console.log('a **5', a ** 5); //2的5次方＝32
```
* ### 給預設值
```javascript
var b;
    b ||= 10;
    console.log('給預設值 b', b); //回傳10

    var c = 555;
    c ||= 10;
    console.log('給預設值 c', c); //回傳555 ; 因為c有宣告，所以c適用原本給的值
```
