---
title: TypeScript - 註記(Annotation) 與斷言(Assertion)
date: 2022-01-14 11:08:06
tags: ["TypeScript"]
categories: TypeScript
---

從開始學習前端之後，大多的學習資源都是來自線上課程、youtube 影片為主，鮮少透過閱讀書籍來學習相關知識，但自從進入公司之後，因為公司內有些程式書籍能夠借閱，先是從 JavaScript 大全開始，一開始閱讀起來真的滿痛苦，不知道該如何消化內容，直接順著讀過去又怕忘記，但目前還是順順的讀到了第8章～後來，找到的方式就是在筆記軟體上做些註記，工作上遇到相關的再回過頭看一次，加入筆記。

而在此次，發現 TypeScript 即便已經看過介紹型別、基本使用基礎的影片後，仍然覺得不太足夠，所以就開始翻閱書籍 「讓 TypeScript 成為你全端開發的 ACE」，文中介紹滿多之前沒有注意到觀念、也有帶到不少 JavaScript 的觀念來呼應。所以本篇，多是來自於書中資訊，透過整理呈現。

## 註記 Annotation V.S. 斷言 Assertion

* 型別註記：指在告訴 TS 編譯器：「任何被註記到的變數、函式的參數等，都必須遵照被註記過後的變數型別」。
* 所以編譯器會隨時隨地的監測該變數有沒有出現型別衝突的可能---關鍵字為遵照。
* 而，斷言型別則是無視 TS 編譯器分析整個程式碼的型別推論過程，果斷的告訴 TS 編譯器：「被斷言過後的表達式之運算結果就是某某型別」---關鍵意象是「覆蓋」該表達式的型別推論結果。

## 註記與斷言
* 帶有冒號的相關語法

```typescript
let randomNumber:number = Math.random();
const subscribed:boolean = true;
```
* 如果遇到函式，參數(Argument)部分除了可以有類似註記方式標明輸入的參數型別外，在參數宣告結尾也可以註記該函式輸出之型別。

```javascript
function isPositive(input: number):boolean{
	return input > 0
}
```
* 但JS裡，宣告函式的方法有很多種，其中一個是將函式最為值指派到變數。

```javascript
const isPositive:(input:number) => boolean = function(input){
	return input > 0
}
```
* 以上，也能把變數指派的函式，改成ES6箭頭函式
	* 指派運算子(Assignment Operator ，就是指程式裡的等號)左方是函式的型別註記表示法，右方則是普通函式宣告

```typescript
const isPositive:(input:number) => boolean = input => input >0;
```
* 採用邊宣告函式、邊註記型別的方式定義函式

```typescript
const isPositive = function (input:number):boolean{
	return input>0
}
```

```typescript
const isPositive =  (input:number):boolean =>  input>0

```
## 型別斷言語法
* 斷言(Assertion)的語法很簡單，看到有使用關鍵字 as 或者 `<T>(...)` 的格式就是斷言的用法。
* 通常會使用斷言的情境，程式沒辦法推論某表達式的確切運算結果之型別，我們才會用選擇使出斷言來處理這種情境。
* 程式沒辦法推論？
	* 使用第三方的資源（Third-party resources），如使用外來JSON API 獲得的內容之型別格式、讀取檔案轉成 JSON 物件的結果、使用套件提供的功能、呼叫會回傳未知結果的函式。

```typescript
const aNumber = returnsUnknow() as number;
```
或是這樣：
```typescript
const aNumber = <number>(returnsUnkonw());
```
* 請注意：斷言的語法部分，沒有人斷言在變數的名稱宣告部分---也就是是說，如果你這樣寫是錯的：

```typescript
const aNumber as number = returnsUnknow(); //這是錯誤的
```
* 概念有點像，決斷地告訴程式，某表達式的運算結果之型別。
* 變數本身不是被運算，而是被指派某個東西，而斷言應該是斷在被指派的值或表達式的運算結果上。

#### 複雜一點
* 函式宣告表達式有可以被斷言(畢竟函式作為表達式也會被當成值)

```typescript
const isPositive =(input => input > 0) as (input: number) => boolean
```
或者是：

```javascript
const isPositive = <(input: number) => boolean>(input => input >0);
```
* 上面斷言的寫法效果跟之前示範過基礎註記手法：

```typescript
const isPositive :(input: number) => boolean = input => input >0;
```
或者是

```typescript
const isPositive ＝(input: number)：boolean => input >0;
```

## 說下，敘述式與表達式的定義與差別

1. 敘述式：程式運行的流程，例如：JS 裡的判斷敘述式(if...else)以及迴圈敘述式(for 或者是 while 迴圈)。
2. 表達式代表的則是程式碼運算的流程，並且會將運算結果回傳。其中兩者最關鍵差異為：敘述式不會回傳值，表達式則會。

* 以下為常見的表達式範例：

```typescript
 //運算表達式 Arithmetic expression
 1+2*3 ; //回傳解果為7
//邏輯表達式 Logical Expression
true && (something === null || myAge < 18) //回傳結果為 true 或 false

//函式（或方法的呼叫） Function / Method Invocation Expression
Math.pow(2,10) //回傳結果為 1024

//三元運算子 Ternary Operator
myAge < 18 ? 'Youngster' : 'Adult'; //回傳結果為 'Youngster' 因為 myAge < 18 
```
而敘述式：

```javascript
if(/*expression 1*/){
	//若 expression1 為 true 則執行
	
}else if(/*expression 2*/){
	//若 expression2 為 true 則執行
} else{
	//若 expression1、expression2 都不為 true 則執行此
}

//迴圈敘述式 Looping Statement
while (/*expression */){
	//若 expression 為 true， 則重複執行直到 expression 為 false 時跳脫
}
```

* 按照上面敘述式的定義 --- 由於敘述式是在敘述運行流程，而不會回傳值，所以你才不會在 JavaScript 裡面看到這樣的寫法：

```javascript
// JS 並有提供這寫法
const status = if(Age <18 ){
	return 'Young'
}else{
	return 'Adult'
}
```

* 注意：敘述式不一定是多行式（或區塊式）地呈現的最佳案例：變數宣告的指派敘述式(Variable Declaration Assignment Statement)
```javascript
//變數宣告的 指派敘述式
const foo =123;
```
* 請問上面指派式會回傳什麼結果 =>數字 123 或 udefined。正解為 udefined
	* 指派式的回傳結果為 undefined
* 所以可以歸納出，javaScript 的變數宣告的指派式屬於敘述式，非表達式。

#### 表達式什麼時候會以非單行的程式碼，也就是區塊的方式？
* 立即呼叫函式表達式(IIFE)

```javascript
const status = (funcrion(myAge){
								 if (myAge <18){ return 'Young'}
									return 'Adult'
								})(16) //假設填入參數為16，呼叫該函式的結果回傳 'Young'
```
* 當可以分清楚表達式與敘述式差別後，回歸型別斷言
* 斷言的基礎語法
	* 斷言的語法只能用在表達式上，因為表達式具備回傳值，敘述式則沒有
	* 因此，可以「斷言該表達式所運算結果之代表型別」

寫法如下：
```javascript
<expression> as T assertion

<T assertion >(<expression>)
```

```typescript
//運算表達式
(foo+bar*baz) as number;

// 邏輯表達式
(isPositive(num) && idEven(num)) as boolean;

//function
Math.pow(2, 10) as number

//
(myAge <18 ? 'Young' : 'Adult') as string;

//IIFE
(funcrion(myAge){
					 if (myAge <18){ return 'Young'}
						return 'Adult'
					})(16) as number

//只要是表達式，就算在其他敘述式、表達式內也能使用
somefunction(foo as number , bar as string) as boolean
					
```

換種寫法：

```typescript
<number>(foo+bar*baz);

// 邏輯表達式
<boolean> (isPositive(num) && idEven(num)) ;

//function
<number> Math.pow(2, 10) ;

//
<string>(myAge <18 ? 'Young' : 'Adult') ;

//IIFE
 <number>(
	 funcrion(myAge){
					 if (myAge <18){ return 'Young'}
						return 'Adult'
					})(16)
   );
//只要是表達式，就算在其他敘述式、表達式內也能使用
<boolean>somefunction(foo as number , bar as string) 

```

以上針對註記 Annotation V.S. 斷言 Assertion 做個簡單整理～
