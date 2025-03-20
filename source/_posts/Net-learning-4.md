---
title: LINQ 進階運算子：拓展你的查詢能力
date: 2025-01-24 14:22:20
tags: [dotnet]
categories: dotnet
---

除了基礎運算子（如 Where、Select 和 OrderBy），LINQ 還提供了許多進階運算子，用於解決更複雜的查詢需求。以下是一些使用頻率較高的進階運算子，以及它們的功能與應用場景。

## Projection Operators 投影

### Select

範例：投影字串清單中每個字串的第一個字母。
```csharp
List<string> words = ["an", "apple", "a", "day"];

var query = words.Select(word => word.Substring(0, 1));

foreach (string s in query)
{
    Console.WriteLine(s);
}

/* This code produces the following output:

    a
    a
    a
    d
*/

```


### SelectMany
SelectMany 將一個集合中的子集合展平（Flatten），生成單一層級的結果序列。它非常適合處理嵌套集合。

適用場景：需要從多個子集合中提取元素，並將它們組合為單一集合。

```csharp
List<string> phrases = ["an apple a day", "the quick brown fox"];

var query = from phrase in phrases
            from word in phrase.Split(' ')
            select word;

foreach (string s in query)
{
    Console.WriteLine(s);
}

/* This code produces the following output:

    an
    apple
    a
    day
    the
    quick
    brown
    fox
*/
```


```csharp
List<string> phrases = ["an apple a day", "the quick brown fox"];

var query = phrases.SelectMany(phrases => phrases.Split(' '));

foreach (string s in query)
{
    Console.WriteLine(s);
}

/* This code produces the following output:

    an
    apple
    a
    day
    the
    quick
    brown
    fox
*/
```


在提供一個，處理多對多關係數據（如學生和他們的選修課程）。
範例：攤平學生的課程清單

```csharp

var students = new[]
{
    new { Name = "Alice", Courses = new[] { "Math", "Physics" } },
    new { Name = "Bob", Courses = new[] { "Biology", "Chemistry" } }
};

// 展平成單一課程清單
var allCourses = students.SelectMany(s => s.Courses);

Console.WriteLine("所有課程：");
foreach (var course in allCourses)
{
    Console.WriteLine(course);
}
```

輸出結果

```javascript

所有課程：
Math
Physics
Biology
Chemistry
```
<!-- more -->

## 集合運算子：Distinct、Union、Intersect、Except

這些運算子用於對集合執行集合操作（如去重、取交集、聯集和差集）。

Distinct: 從一個集合中移除所有的重複元素，只保留唯一值。
union: 功能是將兩個集合合併，並移除重複的元素，返回一個唯一值集合。
Intersect: 方法來找出兩個陣列中共同的元素
Except: 快速列出兩個集合的差異項目

```csharp
var setA = new[] { 1, 2, 3 };
var setB = new[] { 3, 4, 5 };

var union = setA.Union(setB); // 聯集
var intersect = setA.Intersect(setB); // 交集

Console.WriteLine("聯集：");
Console.WriteLine(string.Join(", ", union));

Console.WriteLine("交集：");
Console.WriteLine(string.Join(", ", intersect));

```

## 量詞運算子：Any 和 All

- Any：判斷集合中是否有任意一個元素滿足條件。
- All：判斷集合中的所有元素是否都滿足條件。

可以適用於：
驗證數據的完整性或一致性。
篩選集合中是否包含特定類型的元素。

```csharp
var products = new[]
{
    new Product { Name = "Laptop", Price = 160 },
    new Product { Name = "Mouse", Price = 50 },
    new Product { Name = "Keyboard", Price = 75 }
};

bool hasExpensiveProduct = products.Any(p => p.Price > 150); 
bool allProductsHaveName = products.All(p => !string.IsNullOrEmpty(p.Name)); // 是否所有商品都有名稱

Console.WriteLine($"有價格超過 150 的商品嗎？{hasExpensiveProduct}");
Console.WriteLine($"所有商品都有名稱嗎？{allProductsHaveName}");

// 有價格超過 150 的商品嗎？True
// 所有商品都有名稱嗎？True

```

## 分組運算子：GroupBy
GroupBy 根據鍵（Key）將集合中的元素分組，每個分組包含鍵和值集合。

```csharp
List<Product> products = GetProductList();
	
var result = from p in  products
             group p by p.Category into c
             select (Category: c.Key , Products: c);

  foreach(var orderGroup in result)
    {
        Console.WriteLine($"Products in {orderGroup.Category} category:");
        foreach(var product in orderGroup.Products)
        {
            Console.WriteLine($"\t{product}");
        }
    }

```

[learn.microsoft](https://learn.microsoft.com/zh-tw/dotnet/csharp/linq/standard-query-operators/projection-operations)
[101 LINQ Samples](https://github.com/dotnet/try-samples/blob/main/101-linq-samples/index.md)