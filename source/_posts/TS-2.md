---
title: TypeScript 基礎介紹 (2)
date: 2021-11-27 09:44:33
tags: ["TypeScript"]
categories: TypeScript
---

![](https://i.imgur.com/xGK5aDO.png)


要熟悉 TypeScript 的型別訂定，並不容易，而且撰寫時也會花費較多的時間，但好處是減少程式碼的錯誤，也讓協作者更容易解讀對方撰寫的程式碼。
萬事起頭難，不同的地方就是多讀幾次，或是尋找相關的範例來相呼應！

## Class

傳統方法中，JavaScript 透過建構函式實現類別的概念，透過原型鏈實現繼承。而在 ES6 中，我們終於迎來了 class。
* 定義一切事務的抽象特點
* object  ：class 的實例  new Class 生成
* 物件導向 OOP(Object Oriented Programming) ：三大特性 封裝、繼承、多型
    * 封裝（Encapsulation）：將對資料的操作細節隱藏起來，只暴露對外的介面。外界呼叫端不需要（也不可能）知道細節，就能透過對外提供的介面來訪問該物件，同時也保證了外界無法任意更改物件內部的資料
    * 繼承（Inheritance）：子類別繼承父類別，子類別除了擁有父類別的所有特性外，還有一些更具體的特性
    * 多型（Polymorphism）：由繼承而產生了相關的不同的類別，對同一個方法可以有不同的響應
### 複習下 ES6 撰寫方式

* 使用 class 定義類別，使用 constructor 定義建構函式。

```javascript
class Animal{
    constructor(name){
        this.name = name;
    } 
    run () {
       return `${this.name} is running` 
    }
}
const snake = new Animal('lily')
console.log(snake.run()); // lily is running

// 繼承的特性（繼承父類的屬性及方法）， 繼承 run 的方法
class Dog extends Animal {
  bark() {
    return `${this.name} is barking`
  }
}

const bao = new Dog('bao')
console.log(bao.run()) // bao is running
console.log(bao.bark()) // bao is  barking
```
* 使用 extends 關鍵字實現繼承
* 这里我们重寫構造函式，注意在子類的構造函式中，必須使用 super 調用父類的方法，否則會報錯。

```typescript
// 
class Cat extends Animal {
  constructor(name) {
    super(name)
    console.log(this.name) //maomao
  }
  run() {
      // run 方法重寫
    return 'Meow, ' + super.run()
  }
}
const maomao = new Cat('maomao')
console.log(maomao.run()) //Meow, maomao is runing
```

#### TypeScript 中的 class
TypeScript 可以使用三種訪問修飾符（Access Modifiers）
* public 修飾的屬性或方法是公有的，可以在任何地方被調用到，預設所有的屬性和方法都是 public 的
* private 修飾的屬性或方法是私有的，不能在声明它的类的外部調用
* protected 修飾的屬性或方法是受保护的，它和 private 類似，區别是它在子類中也是允许被訪問的
例子說明：
* name 被設定為了 public，所以直接訪問實例的 name 屬性是允許的。

```typescript
class Animal {
    public name;
    public constructor(name) {
        this.name = name;
    }
}

let a = new Animal('Jack');
console.log(a.name); // Jack
a.name = 'Tom';
console.log(a.name); // Tom
```

* 希望有的屬性是無法直接存取的，這時候就可以用 private 了：
* name 設為 private

```typescript
class Animal {
    private name;
    public constructor(name) {
        this.name = name;
    }
}

let a = new Animal('Jack');
console.log(a.name); // Jack
a.name = 'Tom';

// index.ts(9,13): error TS2341: Property 'name' is private and only accessible within class 'Animal'.
// index.ts(10,1): error TS2341: Property 'name' is private and only accessible within class 'Animal'.
```

* 使用 private 修飾的屬性或方法，在子類別中也是不允許訪問的：

```typescript
class Animal {
    private name;
    public constructor(name) {
        this.name = name;
    }
}

class Cat extends Animal {
    constructor(name) {
        super(name);
        console.log(this.name); 
    }
}

// index.ts(11,17): error TS2341: Property 'name' is private and only accessible within class 'Animal'.
```
* 而如果是用 protected 修飾，則允許在子類別中訪問：
* 將上述程式碼修改一下

```typescript
class Animal {
    protected name;
    public constructor(name) {
        this.name = name;
    }
}
```

### 類別 class 的型別
給類別加上 TypeScript 的型別很簡單，與介面類似：

```typescript
class Animal {
    name: string;
    constructor(name: string) {
        this.name = name;
    }
    sayHi(): string {
      return `My name is ${this.name}`;
    }
}

let a: Animal = new Animal('Jack');
console.log(a.sayHi()); // My name is Jack
```

[類別](https://willh.gitbook.io/typescript-tutorial/advanced/class)

### class 與 interface
* 介面（Interfaces）：不同類別之間公有的屬性或方法，可以抽象成一個介面。介面可以被類別實現（implements）。一個類別只能繼承自另一個類別，但是可以實現多個介面
* 在 物件導向 世界中，一個 class 只能繼承自另外一個class
* 有時候不同 class 之前，可以有共同的特性，使用子類繼承父類的方法很難來完成
* class 可以使用 implements 來實現 interface （提高 OOP 的靈活性）

#### 範例一：
* 有 車子和手機，都有打開radio的功能
* 可以將兩個共有的抽取為一個 interface

```typescript
class Car  {
  switchRadio(trigger: boolean) {
    
  }
}

class Cellphone {
  switchRadio(trigger: boolean) {
    
  }
}
```
#### 可以將兩個共有的抽取為一個 interface
*  void => 代表什麼都不返回
*  在class 後面放入  implements 讓類去實現它

```typescript
interface Radio {
  switchRadio(trigger: boolean): void;
}

class Car implements Radio {
  switchRadio(trigger) {
    return 123
  }
}
class Cellphone implements Radio {
  switchRadio() {
  }
}
```

#### 範例二
* 新特性為 檢查電池的容量
* 是手機有，但汽車沒有的
* 所以新建立一個 interface （檢查battery）
    * 此功能只有手機有，所以放在手機的class

```typescript
interface Battery {
  checkBatteryStatus(): void;
}
// 要實現多个介面，我们只需要中間用 都好 隔开即可。
class Cellphone implements Radio, Battery {
  switchRadio() {
  }
  checkBatteryStatus() {

  }
}
```
#### 此外 interface 之間有繼承關係
* 建立 radioWithBattery 繼承 Radio ，裡面再放入檢查電量的設定

```typescript
interface radioWithBattery extends Radio {
  checkBatteryStatus(): void;
}


class Cellphone implements radioWithBattery {
  switchRadio() {
  }
  checkBatteryStatus() {

  }
}
```
[類別與介面](https://willh.gitbook.io/typescript-tutorial/advanced/class-and-interfaces)

## enum 列舉
* 常數指執行程序中不會被改變的值，在 JS 中我們一般會用 const 來宣告
* 但有些取值是在一定範圍內的一系列常數。
  * 如：一周內七天、三原色(紅、黃、藍)、方向(上、下、左、右)
### 範例一
* 數字列舉
* 列舉成員會被賦值為從0，開始遞增

```typescript
// 數字列舉，一个數字列舉可以用 enum 这个關鍵詞來定義，我们定義一系列的方向，然后这里面的值，列舉成员会被赋值为從 0 开始遞增的數字,
enum Direction {
  Up,
  Down,
  Left,
  Right,
}
console.log(Direction.Up) //0
// 還有一个神奇的點是這個列舉还做了反向映射
console.log(Direction[0]) //up

```
![](https://i.imgur.com/VMRV8oM.png)

* 可以手動賦予值
* 未手動賦值的列舉項會接著上一個列舉項遞增。

```typescript

  Up = 10,
  Down,
  Left,
  Right,
}
console.log(Direction.Down) // 11 ，後面的項目數值會遞增

console.log(Direction[0]) //up

```

### 範例二

```typescript
// 字符串列舉
enum Direction {
  Up = 'UP',
  Down = 'DOWN',
  Left = 'LEFT',
  Right = 'RIGHT',
}
const value = 'UP'
if (value === Direction.Up) {
  console.log('go up!')
}
```

### 範例三
* 常數列舉
    * 編譯後的邏輯變少了
    * 使用常數列舉會內連列舉的用法並且不會將設定的列舉編譯成 JS 程式碼

```typescript
const  enum Direction {
  Up = 'UP',
  Down = 'DOWN',
  Left = 'LEFT',
  Right = 'RIGHT',
}
const value = 'UP'
if (value === Direction.Up) {
  console.log('go up!')
}
```
![](https://i.imgur.com/Fp2XF0y.png)

---
## 泛型 Generics
* 要解決什麼問題
    * 建立函式 echo 參數為 arg ，接著宣告變數傳入參數
    * 但其中的型別沒有設定
    * 傳入和返回的型別無法統一

```typescript
function echo(arg) {
  return arg
}
const result = echo(123)
// 這時候我們發現了一個問題，我们傳入了數字，但是返回了 any
```
![](https://i.imgur.com/lo6XFH8.png)

* 當在建構 function、internet及Class 時，你會希望這些component都能被重複運用的 Generic(泛型)提供了一個彈性的作法。
* 語法是: ``<T>``

### 範例一
* 泛型（Generics）是指在定義function、internet及Class的时候，不预先指定具體的型別，而在使用的时候再指定型別的一種特性。

```typescript
function echo<T>(arg: T): T {
  return arg
}

const result = echo(123)
```
![](https://i.imgur.com/X4quRIj.png)

### 範例二 傳入多個

```typescript
function swap<T, U>(tuple: [T, U]): [U, T] {
  return [tuple[1], tuple[0]]
}

const result = swap(['string', 123])

```
![](https://i.imgur.com/hJ5IqSY.png)

### 泛型第二部分 - 约束泛型
* 帶有「限制」的泛型

```typescript
function echoWithArr<T>(arg: T): T {
  console.log(arg.length)
  return arg
}

// 上例中，泛型 T 不一定包含属性 length，我们可以给他傳入任意型別，當然有些不包括 length 属性，那样就會報錯

// 我們使用了 extends 約束了泛型 T 必須符合介面 IWithLength 的形狀，也就是必須包含 length 屬性。
interface IWithLength {
  length: number;
}
function echoWithLength<T extends IWithLength>(arg: T): T {
  console.log(arg.length)
  return arg
}

echoWithLength('str')
const result3 = echoWithLength({length: 10})
const result4 = echoWithLength([1, 2, 3])

//此時如果呼叫 loggingIdentity 的時候，傳入的 arg 不包含 length，那麼在編譯階段就會報錯了
echoWithLength(7);
// index.ts(10,17): error TS2345: Argument of type '7' is not assignable to parameter of type 'Lengthwise'


```

### 泛型第三部分 - 泛型在 class 和 interface 中的使用
1. 泛型在 class 的使用
* 在第一個程式中存在一个问题，它允許你向 Queue 中添加任何型別的數據，當然，當數據被彈出隊列时，也可以是任意類型
* 在下方的範例中，看起来可以向隊列中添加 string 型別的數據，但是那麼在使用的過程中，就會出現無法捕捉的錯誤

```typescript
class Queue {
  private data = [];
  push(item) {
    return this.data.push(item)
  }
  pop() {
    return this.data.shift()
  }
}

const queue = new Queue()
queue.push(1)
queue.push('str')
console.log(queue.pop().toFixed())
console.log(queue.pop().toFixed())


```
```typescript
class Queue<T> {
  private data = [];
  push(item: T) {
    return this.data.push(item)
  }
  pop(): T {
    return this.data.shift()
  }
}
const queue = new Queue<number>()


```

2. 泛型在  interface 中的使用
* 之前提過可以使用介面的方式來定義一個函式需要符合的形狀
* 當然也可以使用含有泛型的介面來定義函式的形狀：

```typescript
//泛型和 interface
interface KeyPair<T, U> {
  key: T;
  value: U;
}

let kp1: KeyPair<number, string> = { key: 1, value: "str"}
let kp2: KeyPair<string, number> = { key: "str", value: 123}

let arr:number[] = [1,2,3];
let arrTwo:Array<number> = [1,2,3]
```
![](https://i.imgur.com/6GXBwCI.png)

interface 搭配泛型之后，可以靈活的返回不同的型別
* 創建一个拥有特定型別的容器，class 和 泛型 仿佛给一个容器貼上標籤一样
* 泛型就好像一个可變的參數，在用的时候傳入，生成这個不同型別的一个容器，
* 上個部分的用它来靈活的约束参数的型別，不需要參數是一个特别死板的型別，不希望他是一个特定 string、number 型別，我要傳入的参數必须有某某屬性、某某方法，否則就會報錯。
* 在函式使用的时候，函式的这个型別推斷，不會進入到函式中，所以使用表達式，没法明確建立型別的绑定，用泛型可以让我们打破這個鴻溝，這個时候就可以返回它傳入的類型。
## Type Aliases

* 就是给型別起一個别名，讓它可以更方便的被重用。

```typescript
let sum: (x: number, y: number) => number
const result = sum(1,2)
type PlusType = (x: number, y: number) => number
let sum2: PlusType

// 支持聯合
type StrOrNumber = string | number
let result2: StrOrNumber = '123'
result2 = 123 //沒有錯

// 字符串字面量 ，類似在列舉的常數變量
type Directions = 'Up' | 'Down' | 'Left' | 'Right'
// 使toWhere 這個變量為 Direction 類型
let toWhere: Directions = 'Up'

```
* 我們使用 type 定了一個字串字面量型別 EventNames，它只能取三種字串中的一種。
注意，型別別名與字串字面量型別都是使用 type 進行定義。

```typescript
type EventNames = 'click' | 'scroll' | 'mousemove';
function handleEvent(ele: Element, event: EventNames) {
    // do something
}

handleEvent(document.getElementById('hello'), 'scroll');  // 沒問題
handleEvent(document.getElementById('world'), 'dbclick'); // 報錯，event 不能為 'dbclick'

// index.ts(7,47): error TS2345: Argument of type '"dbclick"' is not assignable to parameter of type 'EventNames'.
```

##  Intersection Types

* 使用 `&` 符號
* 經過上面的 type 使 IName 就有了 name 和 age兩個屬性在裡面
    * 和interface 的 extends 有點類似，都是為了實現物件形狀組合和擴展

```typescript
interface IName  {
  name: string
}
type IPerson = IName & { age: number }
// 經過上面的 type 使 IName 就有了 name 和 age兩個屬性在裡面
let person: IPerson = { name: 'hello', age: 12}
```
什麼时候用介面，什麼时候用 Type Aliases：
* interface 是 docker typing 的實現方式，是一種獨特類型，和extends class  implememts 有關的用interface ，
* 和交叉，聯合型別的有關的用 Type Aliases；

## 內建物件
### DOM 和 BOM 的內建物件
DOM 和 BOM 提供的內建物件有：
Document、HTMLElement、Event、NodeList 等。
TypeScript 中會經常用到這些型別：

```typescript
let body: HTMLElement = document.body;
let allDiv: NodeList = document.querySelectorAll('div');
document.addEventListener('click', function(e: MouseEvent) {
  // Do something
});
```
### ECMAScript 的內建物件

* Boolean、Error、Date、RegExp 等。

```typescript
let b: Boolean = new Boolean(1);
let e: Error = new Error('Error occurred');
let d: Date = new Date();
let r: RegExp = /[a-z]/;
```

[TypeScript 內置](https://github.com/Microsoft/TypeScript/tree/main/src/lib)

## Utility Types
[官方](https://www.typescriptlang.org/docs/handbook/utility-types.html)
Typescript 還提供了一些功能性，帮助性的型別，這些型別，大家在 JS 的世界是看不到的，這些型別叫做 utility types，提供一些簡潔明快而且非常方便的功能。
* 使用  Partial
* Omit

```typescript
// partial，它可以把傳入的型別都變成可選
interface IPerson {
  name: string
  age: number
}

let viking: IPerson = { name: 'viking', age: 20 }
type IPartial = Partial<IPerson>
let viking2: IPartial = { } //可以都不傳，也不會報錯

// Omit，它返回的型別可以忽略傳入型別的某个屬性
// 以下範例 將 name 忽略掉
type IOmit = Omit<IPerson, 'name'>
let viking3: IOmit = { age: 20 }
```

資料來源：
1. 線上課程：實戰 Vue3.0(正式版) + TS 
2. [TypeScript 新手指南](https://willh.gitbook.io/typescript-tutorial/)
