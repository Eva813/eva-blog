---
title: TypeScript - interface 使用
date: 2023-01-25 13:34:04
tags: ["TypeScript"]
categories: TypeScript
---

![](https://i.imgur.com/xGK5aDO.png)

最近在複習使用 TypeScript 發現還有些部分可以做紀錄並且重新回憶的區塊，藉此再紀錄於部落格。

## TS interface的基礎宣告
* 將複雜系統簡化的結果叫做介面
* 介面一般首字母大寫

```typescript
interface Person {
  name: string;
  age: number;
}

let Eva: Person = {
    name: 'Eva',
    age: 25
};
```


## 函式與介面
* 以下範例宣告 AddFunction 的介面
* 並將此型別應用於 add 這個變數的方法中

```typescript
interface AddFunction {
  (a: number, b: number): number;
}

let add: AddFunction = function(a, b) { return a + b; };

```

## class與介面

```typescript
interface CatInterface {
	//普通成員變數的規格
	name: string;
	breed: string;
	noise: string;
	
	//普通成員方法的規格，使用函式型別格式
	makeNoise(): void;
	feed(something: string): void
}
```

* 若類別想要實踐此介面，必須用 `implements` 這個關鍵字
* 如果類別實踐介面的規格中，任何一個成員不見或沒有實踐時，會出現提醒訊息。

```typescript
class Cat implements CatInterface{
	public name: string;
	public breed: string;
	public noise: string = 'Meow meow!';

  constructor(name:string, breed:string){
		this.name = name;
		this.breed = breed;
	}

  public makeNoise(){
		console.log(this.noise);
	}

   public feed(something: string){
		 console.log(`${this.name} is eating ${something}...`)
	 }

}
```

## 搭配 interface 常用的方法
1. extends
* 以 `PersonalInfo` 為例

```typescript
interface PersonalInfo{
	name: string;
	age: number;
	interesting: string[];
}
```

* 假設今天想宣告一個新的使用者帳戶的介面為 `UserAccount`
	* 除了有一些和客戶相關的規格屬性外，還要包含介面 `PersonalInfo` 裡的規格


```typescript
interface UserAccount extends PersonalInfo{
	email: string;
	password: string;
	subscribed: boolean;
	
}
```
* 上面的撰寫方式，等同於
* 透過使用 extends 延展可以將功用的屬性、型別整理一起

```typescript
interface UserAccount{
	email: string;
	password: string;
	subscribed: boolean;
	
	//從PersonalInfo延展而來
	name: string;
	age: number;
	interesting: string[];
	
}
```
另外若需要再合併新的型別，也可以透過逗號方式加以延伸
* 此外還能再多新增 `SocialLinks`

```typescript
interface SocialLinks {
	facebook?: string;
	twitter?: string;
	linkedin?: string;
	website?: ({name:string; url:string})[]
	
}
```

* 同時延展:透過逗號再加入新的

```typescript
interface UserAccount extends PersonalInfo, SocialLinks{
	//...
}
```

## pick
Picking Items with `Pick<Type, Keys>`
* 挑選想要的屬性(key)做使用
* 注意 pick 只能在 type 宣告使用
```typescript
interface Todo {
  title: string;
  description: string;
  completed: boolean;
}
 
type TodoPreview = Pick<Todo, "title" | "completed">;
 
const todo: TodoPreview = {
  title: "Clean room",
  completed: false,
};

console.log(todo.title); // "Clean room"
console.log(todo.description); // undefined


```
* 注意 pick 只能在 type 宣告使用
```typescript
interface todo = Pick<Todo, "title" | "completed">;
// 'Pick' only refers to a type, but is being used as a value here.
```


## omit
`Omit<Type, Keys>`

```typeScript
interface Todo {
  title: string;
  description: string;
  completed: boolean;
  createdAt: number;
}
 
type TodoPreview = Omit<Todo, "description">;
 
const todo: TodoPreview = {
  title: "Clean room",
  completed: false,
  createdAt: 1615544252770,
};
 
```



參考資料
[A Detailed Guide on TypeScript Pick Type](https://refine.dev/blog/typescript-pick-utility-type/)
