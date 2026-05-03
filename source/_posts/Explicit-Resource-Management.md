---
title: JavaScript 的 Explicit Resource Management 筆記
date: 2026-05-03 16:03:31
tags: ["JavaScript", "Resource Management", "TC39", "Frontend"]
categories: JavaScript
---

這篇文章在談的是 JavaScript 的 Explicit Resource Management，也就是讓程式碼更一致地處理「資源的建立與清理」。

對前端工程師來說，這類問題很常出現在 WebSocket、fetch abort、Streams、IntersectionObserver、檔案處理、計時器或任何需要明確釋放的 API 上。文章的重點不是「控制垃圾回收」，而是讓我們用更一致、可預期的方式，告訴 JavaScript：這個資源什麼時候該收尾。

## 核心概念

作者把這件事拆成兩層：

1. Implicit resource management
2. Explicit resource management

前者是 JavaScript 既有機制，後者是新的語法與協定。

<!-- more -->

### 1. Implicit resource management

文章先用 WeakSet 和 WeakMap 當例子。

這兩個資料結構的關鍵在於 weakly held references。當物件沒有其他強引用時，JavaScript 可以把它回收，WeakSet 和 WeakMap 裡對應的項目也會一起失去存在意義。

這代表它們很適合拿來做「不想阻止 garbage collection」的暫存資料，但用途很特定，不是一般集合的替代品。

簡單理解：

- WeakMap 適合把額外資料綁在 object 上，但不想影響回收
- WeakSet 適合追蹤 object 是否曾被處理過，但不想強迫它一直留在記憶體裡

這是 JavaScript 已經有的 implicit 行為，不需要我們手動清理。

### 2. Explicit resource management

作者強調，explicit resource management 不是直接管理 memory，而是管理 resource lifecycle。也就是說，我們不是去控制 garbage collector，而是明確定義「這個物件結束使用時要做什麼」。

這個提案的核心是統一 cleanup contract：`[Symbol.dispose]()`。

只要某個物件有這個方法，就表示它知道自己怎麼收尾。這讓不同 API 的關閉操作可以有一致介面，例如：

- `close()`
- `abort()`
- `disconnect()`
- `return()`

都可以被包裝成同一種可預期的 cleanup 語意。

## 關鍵機制

### `[Symbol.dispose]()`

文章的重點是，`[Symbol.dispose]()` 讓「清理」這件事有了標準入口。

以 generator 為例，generator object 原本就有 `return()`，而 `return()` 會讓 generator 提前結束，並觸發 `finally`。

```javascript
function* generatorFunction() {
	try {
		yield true;
		yield false;
	} finally {
		console.log("All done.");
	}
}

const generatorObject = generatorFunction();

console.log(generatorObject.next());
console.log(generatorObject[Symbol.dispose]());
```

這裡的重點不是 generator 本身，而是它展示了一個可預期的 cleanup 模式：

- 物件知道自己何時結束
- 結束時會執行 cleanup
- cleanup 可以被標準化

### `using`

`using` 是提案裡另一個重要語法。

它不是新的 `const`，而是「生命週期綁定」宣告。被 `using` 宣告的變數，會在離開作用域時自動呼叫 `[Symbol.dispose]()`。

```javascript
{
	using resource = {
		[Symbol.dispose]() {
			console.log("All done.");
		}
	};
}
```

概念上可以把它理解成：

- `const` 負責不可重新指定 binding
- `using` 負責在 scope 結束時自動 cleanup

文章也提醒，`using` 的值不是隨便什麼都可以，必須是：

- `null`
- `undefined`
- 有 `[Symbol.dispose]()` 的物件

也就是說，它的用途很專一，就是拿來管理 disposable resource。

### 自動清理的效果

最實用的地方在這裡。

如果你把一個 resource 包成 `using`，那麼離開 block scope 時，JavaScript 就會幫你呼叫 disposer。這意味著你不必每次都手動記得在 `finally`、callback 結束前、或各種分支尾端做清理。

文章用 class 示範了這個流程：

```javascript
class TheClass {
	constructor(theFile) {
		this.theFile = theFile;
		console.log(`Open ${theFile}`);
	}

	[Symbol.dispose]() {
		console.log(`Close ${this.theFile}`);
	}
}

{
	using fileOpener = new TheClass("./some-file");
	console.log(`Do things with ${fileOpener.constructor.name}`);
}
```

這種寫法的好處很明顯，因為它把「建立」和「清理」放進同一個 lifecycle 裡。

## 對前端工程師的實務意義

這篇文章最值得記住的不是語法本身，而是設計思維。

當你在前端處理以下情境時，就很適合思考是否能做成 disposable resource：

- WebSocket 連線
- `AbortController`
- `IntersectionObserver`
- `ReadableStream` / `WritableStream`
- 計時器與長生命週期 callback
- 開啟中的 file handle 或類似外部資源

如果你的物件有清楚的「開始」和「結束」，那它就很適合納入這種模式。

這帶來幾個好處：

- cleanup 規則一致
- code review 更容易檢查是否有釋放資源
- 降低 memory leak 與資源懸掛的風險
- 讓資源管理從 scattered imperative code 變成 declarative pattern

## 實作筆記

如果之後你要在專案裡實作類似概念，可以這樣想：

1. 先找出有哪些物件需要明確 cleanup
2. 為這些物件定義一致的 disposal method
3. 若環境支援 `using`，就讓 scope 管理生命週期
4. 若還不支援，就先用 `try...finally` 保證收尾

### 什麼情況適合用 `using`

如果一個物件同時滿足下面幾件事，就很適合考慮 `using`：

- 它會先被建立，再在某個明確時間點結束
- 結束時需要固定做 cleanup
- 你不想把 cleanup 分散在很多分支裡
- 這個資源的生命週期剛好可以被 block scope 表達

前端最常見的例子，就是「啟動時要註冊，結束時要解除註冊」的 API，例如 observer、connection、stream、timer 之類。

### 什麼情況不能直接用 `using`

如果你遇到下面情況，就不適合直接依賴 `using`：

- 執行環境還不支援這個提案
- 你所在的 scope 不是可以結束的 block、function、module scope
- 你拿到的物件本身沒有 `[Symbol.dispose]()`
- 這個資源的 cleanup 邏輯不是單純「離開作用域就結束」

這時候不要硬套 `using`，而是改成手動管理生命週期。

### fallback 怎麼做

fallback 的核心不是「換另一個神奇語法」，而是保留同樣的設計：

1. 先建立資源
2. 使用資源
3. 不管中間有沒有錯誤，都保證 cleanup

最穩定的寫法仍然是 `try...finally`，而且最好讓物件也保留同一個 cleanup 介面，例如 `[Symbol.dispose]()`：

```javascript
class Resource {
	constructor(name) {
		this.name = name;
		console.log(`Open ${name}`);
	}

	[Symbol.dispose]() {
		console.log(`Close ${this.name}`);
	}
}

const resource = new Resource("socket");

try {
	// use resource
} finally {
	resource[Symbol.dispose]();
}
```

這樣做的好處是，你之後如果有一天可以改成 `using`，幾乎不用重寫物件本身，只要把外層的生命週期控制換掉就好。

### 你可以怎麼判斷要不要改成這種模式

如果你現在的程式碼裡常看到下面這些寫法，就值得考慮整理成 disposable resource：

- 到處都是 `cleanup()`、`destroy()`、`close()`、`disconnect()` 的呼叫
- 同一個資源需要在很多地方重複寫解除註冊流程
- 一旦中間 throw error，就很容易漏掉收尾
- 你的團隊很常忘記在 component unmount、request 結束、頁面切換時做清理

這種情況下，把 cleanup 集中成一個明確的 contract，通常會比把清理邏輯散落在各處更穩定。

### 最小 fallback 範例

如果你只想看最精簡的做法，在還不能使用 `using` 的環境，最穩的寫法仍然是 `try...finally`。

```javascript
const resource = createResource();

try {
	// use resource
} finally {
	resource.dispose?.();
}
```

這不是新東西，但它跟文章的方向一致：把 cleanup 規則明確化。

## 我從這篇文章記下來的重點

- WeakMap 和 WeakSet 是 implicit resource management 的例子，重點在不阻止 garbage collection
- `[Symbol.dispose]()` 是統一的 cleanup contract
- `using` 讓資源在離開 scope 時自動 cleanup
- 這個提案不是在控制 garbage collection，而是在管理 resource lifecycle
- 對前端來說，凡是有 open/close、subscribe/unsubscribe、observe/disconnect、start/stop 的 API，都值得思考是否能套用這種模式

## 結論

如果你的前端工作常常碰到需要手動清理的資源，這篇文章的價值很高。

它傳達的核心不是「JavaScript 多了一個新關鍵字」，而是「JavaScript 終於有更一致的方式描述 resource cleanup」。對實務開發來說，這會讓 code 更可讀，也更不容易留下忘記收尾的 bug。
