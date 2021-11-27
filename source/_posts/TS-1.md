---
title: TypeScript 基礎介紹 (1)
date: 2021-11-27 08:44:33
tags: ["TypeScript"]
---

因為工作需求，有需多缺乏的技能需要補齊，TypeScript 就是其中一項。
上班看不懂的時候邊查詢，空閒時間再來上課不足不懂的知識點，但還是得透過文章的整理來好好消化一下！
既然要開始介紹 TypeScript 就先來說說使用它的原因吧。

## 為什麼使用 TypeScript
* 程式語言中，有區分強型別和弱型別兩種類型。
* 而 JavaScript 是弱型別語言。
* 在 JavaScript 中，他會自己判斷應該執行的型別。自行做[型別自動轉換]
* 在快速開發時，很有可能引發一些預期之外的錯誤，相當不利於多人協同開發。
  * 優點：寫法彈性。
  * 缺點：無法在開發時期檢查型別。

* 依據上述的問題，所以使用 TypeScript
1. 使程式碼更容易理解 （如函式需傳入的參數可以明確設定型別）
2. 減少程式碼撰寫的錯誤
3. 可以完全兼容JavaScript，此外能與 ES6 完美結合，並提供更多支援寫法。

## 安裝介紹
* 就是直接到官方網站查看，依需求安裝
![](https://i.imgur.com/wfMqk4w.png)

`npm install -g typescript`
* 檢查版本
`tsc -v`

## 型別介紹
從 JavaScript 型別分類開始
### 字串 string
* 和ES6一樣，可以使用[字串樣板 Template Literals]
```TypeScript
let firstName:string = 'NaNa'
let message: string = `Hello,${firstName}`

```
### 布林值 boolean
布林值是最基礎的資料型別，在 TypeScript 中，使用 boolean 定義布林值型別： 
```typescript
let isDonw:boolean = false 
```
注意：使用建構函式 Boolean 建立的物件不是布林值：
* 事實上 new Boolean() 返回的是一個 Boolean 物件
```JavaScript
let createdByNewBoolean: boolean = new Boolean(1);

// Type 'Boolean' is not assignable to type 'boolean'.
//   'boolean' is a primitive, but 'Boolean' is a wrapper object. Prefer using 'boolean' when possible.
```
```
在 TypeScript 中，boolean 是 JavaScript 中的基本型別，而 Boolean 是 JavaScript 中的建構函式。
```

### 數值 number
```TypeScript
let age:number = 10
let notANumber: number = NaN;
let infinityNumber: number = Infinity;
```
### Null 和 Undefined
* 與 void 的區別是，undefined 和 null 是所有型別的子型別
    * unfefined 可以賦值給number類型的變數
    * 陣列被赋值為 undefined 或 null 不会报错
    * `let num:number = undefined`
```TypeScript
let u: undefined = undefined;
let n: null = null;

// 這樣也不會報錯
let u: undefined;
let num: number = u;
```

### void
* 通常用在當函式沒有回傳值時。
![](https://i.imgur.com/OOnpUUH.png)
* 而 void 型別的變數不能賦值給 number 型別的變數：
```JavaScript
let u: void;
let num: number = u;

// Type 'void' is not assignable to type 'number'.
```
### any
* 在任意值上任何属性都是允許的
    * 當有明確型別時，應避免使用 any 
    * 因為 any 可以任意調用方法和屬性，很有可能出現錯誤（就喪失類型檢查的意義）
如果是 any 型別，則允許被賦值為任意型別
```TypeScript
let myFavoriteNumber: any = 'seven';
myFavoriteNumber = 7;
```
#### 未宣告型別的變數
變數如果在宣告的時候，未指定其型別，那麼它會被識別為任意值型別：
```JavaScript
let something;
something = 'seven';
something = 7;

something.setName('Tom');
```

### 陣列 Array 與 元組 Tuple 
#### 陣列
1. 在想宣告的型別後面加上[]，宣告為某個型別的Array
```TypeScript
let idList:number[] = [1,2,3]
```
2. 使用陣列泛型，Array <元素型別>
```TypeScript
let list:Array<number> = [1,2,3]
```
* 注意，當已經宣告這是個數字陣列
  * 要 push 字串進去，會出現錯誤
```TypeScript
let list:Array<number> = [1,2,3]
list.push("4")
```
![](https://i.imgur.com/bzszCFn.png)
### 元組 Tuple
* 剛剛的陣列將同一類型的數據放在一起，但是，想加入不同型別的數據怎麼辦？
* 表示方式與陣列相似，將型別寫在``[]``
* 有固定長度和元素型別的陣列
```typescript
// 表示方式和陣列非常相似，只不过它将型別寫在了里面 這時會對每一項產生了限定的作用
let user: [string, number] = ['viking', 20]
//但是当我们少寫一項 就會報錯，同樣多寫也會報錯
user = ['molly', 20, true]
```
![](https://i.imgur.com/MfhvOd7.png)

[元組](https://willh.gitbook.io/typescript-tutorial/advanced/tuple#can-kao)


### 物件的型別——介面 (Interface)
* Interface 可以用來定義物件，還有由物件所延伸的型別（例如，陣列、函式）
* `age?: number;` 在該屬性後面加上 `?` 表示為可選屬性 ，也就是在宣告新的物件時，可以彈性加入或不加入age （也不會報錯）
```typescript
// 我们定義了一個介面 Person
interface Person {
  name: string;
  age: number;
}
// 接着定义了一个變數 NaNa，它的型別是 Person。
//这样，我们就约束了 NaNa 的形狀必須和介面 Person 一致。
let NaNa: Person ={
  name: 'NaNa',
  age: 20
}

//有时我们希望不要完全匹配一个形狀，那么可以用可選屬性：
interface Person {
    name: string;
    age?: number;
}
let NaNa: Person = {
    name: 'NaNa'
}


```

### 唯讀屬性
* readonly 是用在屬性上面
* 希望物件中的一些欄位只能在建立的時候被賦值，那麼可以用 readonly 定義唯讀屬性
```typescript

interface Person {
  readonly id: number;
  name: string;
  age?: number;
}
// 建立物件
let Vic: Person ={
  id:1,  
  name: 'Vic',
  age: 20
}
//不能去修改id    
Vic.id = 9527
```

### 函式型別
* 函式可以作為參數、可以存入陣列，可以被另外一個函式返回、可以被賦值另外一個變數
* 函式是由兩部分構成，一個是輸入(通過不同參數來實現)，二為輸出(就是函數的返回結果)
#### 例子一
* 設定參數型別、返回的型別
* 若參數設定了 x、y兩個，也就只能放入兩個
```typescript
// 来到我们的第一个例子，约定输入，约定输出
function add(x: number, y: number): number {
  return x + y
}
```
![](https://i.imgur.com/RLOECvw.png)

#### 例子二
* 也可以設定可選參數
* 在 z? 表示 z 可有可無

```typescript
// 可选参数
function add(x: number, y: number, z?: number): number {
  if (typeof z === 'number') {
    return x + y + z
  } else {
    return x + y
  }
}
```
* 而在可選參數後面不可以再添加確定參數
![](https://i.imgur.com/qzQRT0r.png)

#### 例外說明：引數預設值
* 在 ES6 中，我們允許給函式的引數新增預設值，TypeScript 會將添加了預設值的引數識別為可選引數：
```typescript
function buildName(firstName: string, lastName: string = 'Cat') {
    return firstName + ' ' + lastName;
}
let tomcat = buildName('Tom', 'Cat');
let tom = buildName('Tom'); // 因為 lastName 添加了預設值，識別為可選引數
```
此時就不受「可選引數必須接在必需引數後面」的限制了：
```typescript
function buildName(firstName: string = 'Tom', lastName: string) {
    return firstName + ' ' + lastName;
}
let tomcat = buildName('Tom', 'Cat');
let cat = buildName(undefined, 'Cat');
```



#### 例子三
* 函式的表達式
```typescript
const add = (x: number, y: number, z?: number): number => {
  if (typeof z === 'number') {
    return x + y + z
  } else {
    return x + y
  }
}

// 函式本身的类型
const add2: (x: number, y: number, z?:number) => number = add
```
* 函式不只輸入、輸出有類型，本身也有類型
![](https://i.imgur.com/f2Ym2Zb.png)
* 將 add2 賦予 string 會出錯誤
* 所以須聲明一模一樣的 `const add2: (x: number, y: number, z?:number) => number = add`
![](https://i.imgur.com/FvS1OvL.png)

#### 例子四
* 使用 interface (單純定義函式的 Interface)
```typescript
// interface 描述函数类型
const sum = (x: number, y: number) => {
  return x + y
}
interface ISum {
  (x: number, y: number): number
}
const sum2: ISum = sum
```
* 另一個範例
```typescript
interface SearchFunc {
    (source: string, subString: string): boolean;
}

let mySearch: SearchFunc;
mySearch = function(source: string, subString: string) {
    return source.search(subString) !== -1;
}
```

---

## 型別推論（Type Inference）\ 聯合型別（Union Types） \ 型別斷言（Type Assertion）
### 型別推論（Type Inference）
* 可以推論出我們賦值過程中，這個變數應該是什麼類型的
* TS 會在沒有明確指定型別的時候，推測出型別
![](https://i.imgur.com/280zYEs.png)

### 聯合型別（Union Types）
* 用分隔符號
* 當 TypeScript 不确定一个聯合型別的變數到底是哪個型別的时候，我们只能使用此聯合型別的所有型別裡共有的屬性或方法
```TypeScript
// 允許 numberOrString 的型別是 string 或者 number，但是不能是其他型別。
let numberOrString: number | string 
// 而在使用此聯合型別的所有型別里共有的屬性性或方法：
numberOrString.length  //會報錯 （只有 string 適用）
numberOrString.toString() //string 和 number 的共同屬性是沒問題

//若放在函式定義型別
function getLength(something: string | number): number {
    return something.length;
}

// index.ts(2,22): error TS2339: Property 'length' does not exist on type 'string | number'.
//   Property 'length' does not exist on type 'number'.

```
* 聯合型別的變數在被賦值的時候，會根據型別推論的規則推斷出一個型別：
```TypeScript
let numberOrString: string | number;
numberOrString = 'seven';
console.log(numberOrString.length); // 5
numberOrString = 7;
console.log(numberOrString.length); // 編譯時報錯
// index.ts(5,30): error TS2339: Property 'length' does not exist on type 'number'.
```
上例中，第二行的 numberOrString 被推斷成了 string，存取它的 length 屬性不會報錯。
而第四行的 numberOrString 被推斷成了 number，存取它的 length 屬性時就報錯了。

* type guard 
* 當遇到聯合類型，可以使用條件語句，自動幫你縮小型別範圍

```javascript
// typescript 在不同的条件分支里面，智能的缩小了范围，这样我们代码出错的几率就大大的降低了。
function getLength2(input: string | number): number {
  if (typeof input === 'string') {
    return input.length
  } else {
    return input.toString().length
  }
}
```


### 型別斷言（Type Assertion）
* 開發者比 TS 更了解編寫的程式碼。因此，TS 允許開發者覆蓋它的推論，這樣的機制稱為「型別斷言」。
```typescript
// 这里我们可以用 as 關鍵字，告诉typescript 这里我把它看作是一個 string，你可以给他用 string 的方法。
function getLength(input: string | number): number {
  const str = input as string
  if (str.length) {
    return str.length
  } else {
    const number = input as number
    return number.toString().length
  }
}
```
* 型別斷言不是型別轉換，斷言成一個聯合型別中不存在的型別是不允許的：
```TypeScript
function toBoolean(something: string | number): boolean {
    return <boolean>something;
}

// index.ts(2,10): error TS2352: Type 'string | number' cannot be converted to type 'boolean'.
//   Type 'number' is not comparable to type 'boolean'.
```

基礎型別先介紹到這裡，下篇將介紹一些進階用法～

資料來源：
1. 線上課程：實戰 Vue3.0(正式版) + TS 
2. [TypeScript 新手指南](https://willh.gitbook.io/typescript-tutorial/)
