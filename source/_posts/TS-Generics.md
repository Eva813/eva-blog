---
title: TS - Generics 泛型
date: 2023-01-25 14:27:13
tags: ["TypeScript"]
categories: TypeScript
---

![](https://i.imgur.com/xGK5aDO.png)

## 資料整理
例如有兩個 interface 屬性一樣，差異在於 型別內容

```typeScript
interface DataA {
  key:string
  key2:number
}

interface DataB {
  key:string
  key2:boolean
}

```
* 使用 泛型 整理
* 將 T 部分，設為傳入的變數，可以依據宣告得變數所符合的型別進行設定
```typeScript

type GenericsObj<T> = {
  key:string
  key2:T
}
type DataA = GenericsObj<number>;
type DataB = GenericsObj<boolean>;
//以上的 DataA、DataB 的型別會與剛剛 interface 的設定一樣
```

範例2：
```typeScript
interface KeyPair<T, U> {
  key: T;
  value: U;
}

let kp1: KeyPair<number, string> = { key: 1, value: "str"}
let kp2: KeyPair<string, number> = { key: "str", value: 123}

let arr:number[] = [1,2,3];
let arrTwo:Array<number> = [1,2,3]
```

## payload 應用

* 這邊以 redux 中整理的 action 為範例

```typeScript
type userDataPayload = {
  userId: string,
  data:any
}
//負責生成 action 的函式
const setUserDataAction = (payload:userDataPayload )=>({
  type: 'SET_USER_DATA',
  payload
})
const resetUserDataAction =()=>({
  type: 'RESET_USER_DATA',
})

type CartDataPayload = {
  product: string
}
const setCartDataAction =(payload:CartDataPayload)=>({
  type: 'SET_CART_DATA',
  payload
})

type SetUserDataAction = {
  type: 'SET_USER_DATA',
  payload:userDataPayload
}
type ResetUserDataAction = {
  type: 'RESET_USER_DATA'
}
type SetCartDataAction = {
  type: 'SET_CART_DATA',
  payload:CartDataPayload
}

```
* 由上可以觀察到 SetUserDataAction、ResetUserDataAction、SetCartDataAction 整理為一個
* 將 action 的型別整理，其key為type 與 payload

```typescript
type Action<T,P> = {
  type: T,
  payload:P
}
type ActionWithoutPayload<T> = {
  type: T
}

type SetUserDataAction = Action<'SET_USER_DATA',userDataPayload>
type SetCartDataAction = Action<'SET_CART_DATA',CartDataPayload>
type ResetUserDataAction = ActionWithoutPayload<'RESET_USER_DATA'>

//可以將action 中的 type 進行聚合
type Actions = SET_USER_DATA | SET_CART_DATA | RESET_USER_DATA;
```
* 以下，進一步再將 Action 和 ActionWithoutPayload 進行整合，他們差異在與 payload 

```typeScript
type Action<T, P = null> = p extends {} ? {type: T, payload: P} : {type: T};
```

型別參數動態生成不同型別

## 函式的泛型

```typeScript
const fn = (param: string | number):(string|number)[]=>[param];
fn(1);//number 陣列 number[];
fn('hello') //string[]
```
![](https://i.imgur.com/QZCK4Em.png)

```typeScript
const fn = <T>(param: T):(T)[]=>[param];
fn(1);//number 陣列 number[];
fn('hello') //string[]
```
![](https://i.imgur.com/Yeeuum6.png)

* function 改寫方式

```typeScript
function fn<T>(param:T):T[]{
  return [param]
}
```


## type 的泛型
```typeScript
//共用模板
type GenericList<T> = T[];

type StrList = GenericList<string>;
type BoolList = GenericList<boolean>;

type GenericValObj<T> = {[key:string]:T} 
type NumValObj = GenericValObj<number>

const numValObj:NumValObj = {
  img_res_200:200
}

```

![](https://i.imgur.com/sJFzEUg.png)


![](https://i.imgur.com/7P1MnFL.png)

* 嘗試聚合資料

```typeScript
type GenericUnion<T,U> = T | U | T[] | U[];
type strNumUnion = GenericUnion<string,number>

const val:strNumUnion = "123"
const val2:strNumUnion = ["123"]
```

![](https://i.imgur.com/y5Vl8M9.png)

## 將函式改寫為 共用的泛型型別
* 將type 拉出來獨立撰寫，可以重複使用

```typeScript
const fn = <T>(param: T):(T)[]=>{
  return [param]
};

//上面的函式也可以用於
interface Props {
  onFn: typeof fn
}

//rewrite
type GenericFn = <T>(param: T) => T[]
const fn:GenericFn = (param) => {
  return [param]
}

//共用，如下有個 interface 其中有個屬性的型別與 GenericFn 一樣，則可以共用
interface Props {
  onFn:GenericFn
}

```


```typeScript
function fn<T>(param:T):T[]{
  return [param]
}

//產生型別泛型
type FnType = typeof fn ;
```

![](https://i.imgur.com/yxsVm1s.png)

![](https://i.imgur.com/zYtwh7H.png)
![](https://i.imgur.com/tPHEDZ7.png)

### 比較`<T>`位置差異
```typeScript
type FnTypeA = <T>(param: T) => T[]
type FnTypeB<T> = (param: T) => T[]

const fn1:FnTypeA = (param) => {
  return [param]
}

//必須指定好型別的 argument
const fn2:FnTypeB<string> = (param) => {
  return [param]
}

//兩個函式在使用上會有差異：
fn1();

fn2('Hi')

```

![](https://i.imgur.com/J3zJs2Y.png)

![](https://i.imgur.com/0wxPZk9.png)

* fn1 可以填入任何內容、可以動態填入型別
![](https://i.imgur.com/pwfnIjO.png)
![](https://i.imgur.com/6B8gtAc.png)
![](https://i.imgur.com/OgPatHZ.png)
* fn2 只能填入 string 內容
![](https://i.imgur.com/cwmVA5b.png)


```typeScript
export type ClickFn = <Event>(e:Event) => any

//使用 function 會無法重複使用寫好的型別
// 引用
const handleClick:ClickFn = (e)=>{

}
```

## interface 的泛型

```typeScript
export interface GenericI<T>{
  [key:string]: T
} 
type NumValObj =  GenericI<number>;
const numberValObj:NumValObj = {
  res:123
}

//type寫法也可以一樣
export type GenericT<T> = {
  [key:string]: T
}

//API 的應用！！！
//注意：in 寫法只用於 type 中
export type GenericKeyValObj<T extends  keyof any,P> = {
  [key in T ]: P
}

//可以應用於 API 

type ApiKey = 'user' | 'id' | 'password'; //將欄位值設定好
type UserApiData = GenericKeyValObj<ApiKey, string>

```
![](https://i.imgur.com/dceV8io.png)

* 可以很快的生成 API 的資料型別
![](https://i.imgur.com/Vv3lVDC.png)

* 其他範例

```typeScript
//類似呼叫API的函式
//Params : 參數
//Res result : 回傳值
type GenericApiFn<Params, Res> = (params:Params)=> Res

//組合其他泛型使用，也可以將原本泛型參數自己使用
interface ApiContainerProps<Params, Res> {
  initData: Res;
  onAsyncCb: GenericApiFn<Params, Res>
}

```
* interface 與 type 的泛型寫法

```typeScript
//interface 寫函式
interface FnI {
  <T>(param: T): T[]
}
//同等於
type FnT = <T>(param:T)=>T[]

const fn:FnT = (param)=>[param]
```

* `<T>`位置的調動

```typeScript
//<T> 會影響整個 interface
interface GenericFnI<T> {
  (param: T): T[]
}

const fn2: GenericFnI<string>= (param)=>[param]
```

過去都未曾注意到泛型可以抽成共用還有 `<T>` 的位置所放的位置，會影響宣告，透過此次的教學，讓我對泛型可以應用於實際情況的案例，同樣未來在訂定 type 或 interface 時可以加以注意，思考是否可以改寫為泛型加以使用。

參考資料
[成為進階TS開發者的第一哩路 — 泛型簡介與基礎(1)](https://www.youtube.com/watch?v=9hZbfX_Atpc&t=44s)
[成為進階TS開發者的第一哩路 — 泛型的函式, type和interface寫法一次說清楚！](https://www.youtube.com/watch?v=7yA4ALLoSxY)
