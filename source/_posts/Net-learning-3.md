---
title: 深入淺出 C# LINQ：讓查詢語法變得更簡單
date: 2025-01-18 19:28:54
tags: [dotnet]
categories: dotnet
---

C# 的 LINQ (Language Integrated Query，語言整合查詢) 是一個非常強大的功能，它讓你可以使用類似 SQL 的語法來查詢各種不同的資料來源。這意味著你可以用統一的方式來處理資料，無論資料是來自陣列、列表、資料庫、XML 或其他任何支援 LINQ 的來源。

## LINQ 的兩種語法：
LINQ 有兩種主要的語法形式：
- 查詢語法 (Query Syntax)： 看起來很像 SQL 語法，使用 from、where、select 等關鍵字。這種語法比較易讀，尤其是在處理複雜的查詢時。
- 方法語法 (Method Syntax)： 使用擴充方法 (Extension Methods) 來表示查詢運算子。這種語法更簡潔，也更靈活。簡單的查詢或需要鏈式操作時，通常使用方法語法更為方便。

<!-- more -->

## 基本範例：

所有的 LINQ 查詢作業都包含三個不同的動作：
  取得資料來源。
  建立查詢。
  執行查詢。

```csharp

// 1. 資料來源
int[] numbers = [ 0, 1, 2, 3, 4, 5, 6 ];

// 2. 建立查詢
// numQuery is an IEnumerable<int>
var numQuery = from num in numbers
               where (num % 2) == 0
               select num;
// 建立查詢 (方法語法)
// var numQuery = numbers.Where(num => (num % 2) == 0); 

// 3. 執行查詢
foreach (int num in numQuery)
{
    Console.Write("{0,1} ", num);
} 
```

重點： 建立查詢的步驟並不會實際執行查詢，它只是定義了查詢的邏輯。查詢本身並不會立即從資料來源中提取任何資料。

### 對比範例，不用 LINQ 的情況

```csharp

List<int> numbers = new List<int>() { 0, 1, 2, 3, 4, 5, 6 };
List<int> evenNumbers = new List<int>();
foreach (int num in numbers)
{
    if (num % 2 == 0)
    {
        evenNumbers.Add(num);
    }
}
Console.WriteLine(string.Join(", ", evenNumbers)); // Output: 0, 2, 4, 6

```


## 查詢語法的基本結構

在 LINQ 查詢中，語法的開頭和結尾有著固定的規則：

- 開頭
  LINQ 查詢的開頭必須使用 from 子句。
  from 子句的作用：定義資料來源（數據源）以及範圍變數（即用於遍歷數據的臨時變數）。
  通常，範圍變數會在接下來的子句中使用，例如 where、select 或 orderby。

- 查詢過程中的子句
  LINQ 支援許多類似 SQL 的子句，例如：
  - Where： 根據指定的條件篩選序列中的元素。
  - Select： 將序列中的每個元素轉換成新的形式。你可以選擇元素的某些屬性，或者進行計算。
  - OrderBy： 以遞增順序排序序列中的元素。


### 查詢語法的結尾：group 與 select 子句

在 LINQ 查詢中，每個查詢的結尾必須包含 group 或 select 子句：

1. select 子句

- 作用：將每個範圍變數轉換為新的形式或提取所需的屬性。
- 結果類型：通常是 ``IEnumerable<T>``，其中 T 是返回值的類型。

2. group 子句

- 作用：將範圍變數根據某個鍵值（Key）進行分組。
- 結果類型：會產生一個分組後的序列，每個分組包含一個鍵和一組元素。

使用 group by 分組，以下是使用 group by 子句進行分組的範例：

```csharp
string[] countries = { "France", "Germany", "Finland", "India", "Italy" };

// 根據國家名稱的第一個字母分組
var groupedCountries = from country in countries
                       group country by country[0];

foreach (var group in groupedCountries)
{
    Console.WriteLine($"Key: {group.Key}"); // 群組鍵 (國家名稱的首字母)
    foreach (var country in group)
    {
        Console.WriteLine($" - {country}");
    }
}

```

在前面的章節中，我們已經了解了 LINQ 的兩種語法形式和基本結構：

- 查詢語法提供類似 SQL 的直觀寫法
- 方法語法更加簡潔靈活
- 每個 LINQ 查詢都包含三個關鍵步驟：取得資料來源、建立查詢、執行查詢

## LINQ 的延遲執行 (Deferred Execution)

在 LINQ 中，查詢並不會在宣告時立即執行，而是等到你開始列舉查詢結果時（例如在 foreach 迴圈中）才會實際執行。這種特性被稱為延遲執行（Deferred Execution）。

```csharp
var numbers = new List<int> { 1, 2, 3, 4, 5 };

var query = numbers.Where(n => n > 2); // 定義查詢

numbers.Add(6); // 修改原數據源

foreach (var num in query) // 查詢在這裡執行
{
    Console.WriteLine(num); // 輸出：3, 4, 5, 6
}

```

### 惰性求值（Lazy Evaluation）
惰性求值（Lazy Evaluation）與 LINQ 的延遲執行（Deferred Execution）關係：
在 C# 中，惰性求值（Lazy Evaluation）和延遲執行（Deferred Execution）基本上是同義的，尤其是在 LINQ 的背景下。這兩個術語都描述了查詢在需要時才被執行的行為，而不是在查詢定義時立即執行

- Lazy Evaluation 的靈活性雖然很高，但在某些場景下可能導致效能問題，特別是在多次迭代同一查詢時。以下是常見的陷阱與解決方案：

1. 重複執行查詢
如果對同一個 LINQ 查詢進行多次操作，會導致查詢邏輯被重複執行。

範例：
程式中，query 在每次 foreach 時都會重新執行過濾邏輯，對於大型集合來說可能導致效能下降。

```csharp
var numbers = new List<int> { 1, 2, 3, 4, 5 };

// 定義查詢
var query = numbers.Where(n => n > 2);

// 重複遍歷查詢結果
foreach (var num in query) { Console.WriteLine(num); }
Console.WriteLine("---");
foreach (var num in query) { Console.WriteLine(num); }

```

可以改使用，即時執行

如果查詢結果不需要動態更新，可以使用 ``ToList()`` 或 ``ToArray()`` 等方法，將查詢結果緩存到集合中：

- 使用 `ToList()` 來觸發 **eager query execution**（急切查詢執行），也就是查詢會立即執行並且結果會被快取下來。
- 當使用 `ToList()` 時，LINQ 查詢會在 `ToList()` 調用的那一刻就立刻執行，並且將查詢結果儲存到一個列表中。


```csharp
var cachedQuery = query.ToList(); // 將結果存入清單

foreach (var num in cachedQuery) { Console.WriteLine(num); }
foreach (var num in cachedQuery) { Console.WriteLine(num); }

```

### 如何選擇 Lazy Execution 或 Eager Execution?
1. Lazy Execution
懶性執行是 LINQ 的預設行為。查詢定義時不會執行，只有在真正需要查詢結果時（如遍歷或調用終結操作）才執行。這種方式的優勢是：

- 高效處理大數據源：僅在需要時才進行查詢計算。
- 支持動態數據源：查詢結果會隨著數據源的變化而更新。

適用情境：
- 查詢結果不需要頻繁重複使用。
- 數據源可能在查詢執行前發生變化（例如數據被添加、刪除或修改）。

2. Eager Execution
急切執行會立即執行查詢並將結果存儲到記憶體中（如使用 ``ToList()`` 或 ``ToArray()``）。這種方式的優勢是：

- 避免重複執行查詢邏輯：特別是當需要多次使用查詢結果時，可以提升效能。
- 結果穩定：查詢結果不受數據源變更影響，適合處理不可預測的數據變化或並發情境。
適用情境：
- 需要多次使用查詢結果，或避免重複計算開銷。
- 數據源可能因其他操作發生變化（例如多線程環境）。


#### 進一步，詢問 AI 工具來提供範例區分 2 者使用情境

1. 商品篩選功能（Lazy Evaluation）

功能描述：
假設我們在開發一個電子商務平台，頁面上有商品清單，並且允許用戶使用篩選器（例如「價格高於某個門檻」）動態查看符合條件的商品。
在這種場景中：
需求的重點：數據源可能隨時變更（例如新增、刪除商品），篩選結果需要即時反映最新的狀態。
適用解決方案：選擇「懶性執行」，讓查詢邏輯在每次迭代結果時重新評估數據源，確保結果始終是最新的。

- 數據變更頻繁：例如，後端通過 API 新增商品，或者某些商品的價格被更新。
- 結果不需要重複使用：查詢邏輯只需要執行一次，為用戶提供實時的篩選結果即可。


```csharp
var products = new List<Product>
{
    new Product { Name = "Laptop", Price = 150 },
    new Product { Name = "Mouse", Price = 50 },
    new Product { Name = "Keyboard", Price = 75 }
};

// 定義篩選條件查詢
var expensiveProducts = products.Where(p => p.Price > 100);

// 用戶動態新增商品
products.Add(new Product { Name = "Monitor", Price = 200 });

// 在前端頁面看到的結果
Console.WriteLine("篩選結果：");

foreach (var product in expensiveProducts)
{
    Console.WriteLine($"{product.Name} - ${product.Price}");
}

```

2. 生成商品報表功能（ Eager Execution）

功能描述：
另一個需求是生成商品的銷售報表，例如篩選出價格高於某個門檻的商品，並將結果存儲為報表文件或傳遞到其他業務模組進行處理。
在這種場景中：
需求的重點：查詢結果應保持穩定，不受數據源後續變更影響。
適用解決方案：選擇「急切執行」，將查詢結果存儲到集合中（如 ``List<T>``），避免查詢邏輯重複執行，並確保報表數據的穩定性。
- 數據變更不可預測：例如，商品清單可能會因用戶操作（新增、刪除）或外部系統影響（同步失敗）而改變。
- 結果需要重複使用：生成報表後，數據可能會被多次輸出或傳遞，存入集合可以避免多次執行查詢邏輯。

```csharp
var products = new List<Product>
{
    new Product { Name = "Laptop", Price = 150 },
    new Product { Name = "Mouse", Price = 50 },
    new Product { Name = "Keyboard", Price = 75 }
};

// 定義並執行查詢
var reportData = products.Where(p => p.Price > 100).ToList(); 

// 模擬後續新增商品
products.Add(new Product { Name = "Monitor", Price = 200 });

// 生成報表時使用緩存結果
Console.WriteLine("\n報表篩選結果：");
foreach (var product in reportData)
{
    Console.WriteLine($"{product.Name} - ${product.Price}");
}

```


LINQ 提供了一種簡潔、強大的方式來查詢和操作各種資料來源。透過本篇文章，學習了 LINQ 的基本語法、查詢結構和延遲執行的概念。在後續的文章中，我們將會深入探討 LINQ 的進階運算子和更複雜的應用場景。

[C# 的 LINQ 查詢簡介](https://learn.microsoft.com/zh-tw/dotnet/csharp/linq/get-started/introduction-to-linq-queries)
[101 LINQ Samples](https://github.com/dotnet/try-samples/blob/main/101-linq-samples/index.md)