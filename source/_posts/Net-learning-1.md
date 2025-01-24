---
title: C# 變數與型別
date: 2025-01-12 14:48:50
tags: [dotnet]
categories: dotnet
---

新的一年有機會在公司內部接觸一些些後端語言，公司的後端是 ASP.Net，所以開啟了 C# 學習之路
最最初階的學習資源，目前是來自：
[dotnetcademy](https://dotnetcademy.net/Learn/2036/Pages/13)
[適用於初學者的 C# 基礎知識](https://learn.microsoft.com/zh-tw/shows/c-fundamentals-for-absolute-beginners/17)


## 宣告變數，需指定其類型並提供名稱
C# 是 強型別語言 (Strongly Typed Language)，這表示每個變數 (Variable) 或常數 (Constant) 在使用之前都必須先明確定義其型別 (Type)。

```csharp
string greeting;
int a, b, c;
List<double> xs;

```
<!-- more -->

## Value Types 與 Reference Types 

### Value Types

- 直接存放值本身於變數的記憶體中。
- 當將 value type 變數的值指派給另一個變數時，是將該值**複製**一份，兩個變數之間完全獨立。

```csharp
int a = 1;
int b = a;  // b 複製了 a 的值，此時 b = 1
b = 2;      // 修改 b 的值，不影響 a
Console.WriteLine(a); // 輸出 1
Console.WriteLine(b); // 輸出 2

```
| 型別 | 說明 |
| --- | --- |
| `bool` | 布林值（`true` 或 `false`） |
| `byte` | 8 位元整數 |
| `int` | 32 位元整數 |
| `decimal` | 高精度小數 |
| `double` | 雙精度浮點數 |
| `char` | 單一 Unicode 字元 |
| `struct` | 結構 |
| `enum` | 列舉 |

### Reference Types

- 存放的是記憶體中值的位址，而非值本身。
- 如果將 Reference Types 的變數指派給另一個變數，兩者會**指向同一個記憶體位址**，因此修改一個變數會影響另一個。

```csharp

class Person {
    public string Name { get; set; }
}

Person person1 = new Person { Name = "Amy" };
Person person2 = person1; // person2 指向與 person1 相同的記憶體位置
person2.Name = "Nina";    // 修改 person2 的 Name
Console.WriteLine(person1.Name); // 輸出 Nina，因為兩者指向相同的物件

```

| 型別 | 說明 |
| --- | --- |
| `string` | 字串（特例：不可變） |
| `array` | 陣列 |
| `object` | 所有型別的基底型別 |
| `class` | 類別 |
| `interface` | 介面 |
| `delegate` | 委派 |
| `dynamic` | 動態型別 |

### **字串的特殊性**

雖然 `string` 是參考型別，但它有一個特性：**不可變性 (Immutable)**。這表示：

- 字串的值一旦建立就無法改變。
- 當改變字串的值時，其實是建立了一個新字串，並在新的記憶體位置中儲存。

```csharp

string a = "Amy";
string b = a;  // b 指向與 a 相同的值 "Amy"
b = "Nina";    // b 創建了一個新字串 "Nina"，與 a 無關
Console.WriteLine(a); // 輸出 Amy
Console.WriteLine(b); // 輸出 Nina

```

## Primitive Types

- Value Types vs Reference Types 告訴我們數據在內存中如何存儲和傳遞
- Primitive Types 則表示這些是語言內建的基本類型

| 基本型別 | 別名型別 |
| --- | --- |
| sbyte | System.SByte |
| byte | System.Byte |
| short | System.Int16 |
| ushort | System.UInt16 |
| int | System.Int32 |
| uint | System.UInt32 |
| long | System.Int64 |
| ulong | System.UInt64 |
| char | System.Char |
| float | System.Single |
| double | System.Double |
| bool | System.Boolean |
| decimal | System.Decimal |

- 注意：字串在 C# .NET 中並非基本型別，而是 System.String 類別的物件，屬於參考型別。而字串變數儲存的是字串在記憶體中的參考，並具有不可變性，此設計主要考量記憶體效率和彈性。

## const 與 var
### const
常數是編譯期間已知且在程式執行期間不會改變的不可變值。常數透過 const 關鍵字來宣告，並且必須在宣告時進行初始化。
- 類型限制：C# 只允許內建型別（如數字類型、字串）作為常數。自定義型別（如類別、結構和陣列）無法作為常數，這些型別必須使用 readonly 修飾符來達到不可變效果。


```csharp

class Calendar
{
    public const int Months = 12; // 常數初始化為 12
}

```

#### const 不能賦予 class 實例？

```csharp
onst Person person = new Person(); // 編譯錯誤
```
因為 new Person() 是一個執行時創建的對象，無法在編譯期間確定。因此，無法將它賦值給 const。

如果想要保存某個物件或類別的實例，但又希望它不可變，應該使用 readonly，而不是 const。
readonly 變數在物件初始化時（例如在建構函數中）可以賦值，但一旦設定後便無法更改。

```csharp
public class Person
{
    public string Name { get; set; }
    public Person(string name)
    {
        Name = name;
    }
}

public class Program
{
    public readonly Person person = new Person("John"); // 使用 readonly

    public void DisplayPerson()
    {
        Console.WriteLine(person.Name); // 這個值是不可變的
    }
}



```

### var
主要用途是語言特性中的類型推斷。這意味著，當你使用 var 宣告變數時，編譯器會根據變數的初始值自動推斷出其類型，而不需要明確地指定變數的類型。
### 1. **使用 `var` 的基本示例**

假設你有以下程式碼，這是傳統的變數宣告方式：

```csharp
int one = 1;
string two = "two";

```

在這裡，我們已經明確指定了 `one` 的類型為 `int`，`two` 的類型為 `string`。如果你使用 `var`，則可以省略顯式指定類型，讓編譯器自動推斷：

```csharp

var one = 1;   // 編譯器會推斷 one 是 int
var two = "two"; // 編譯器會推斷 two 是 string

```

這仍然是有效的 C# 程式碼，因為編譯器會根據賦值來推斷變數的類型。在這個例子中，`one` 的值是 `1`，編譯器推斷它是 `int`；`two` 的值是字串 `"two"`，所以編譯器推斷它是 `string`。

1. 使用 `var` 可以簡化程式碼，減少重複寫類型名稱的次數，特別是在處理泛型類型時。例如，以下是兩種不同寫法的比較：

```csharp

ConcurrentDictionary<string, int> dictionary = new ConcurrentDictionary<string, int>(); 
var dictionary = new ConcurrentDictionary<string, int>(); 

```
在這裡，`var` 使得宣告變數時類型名稱變得簡潔，避免了重複書寫 `ConcurrentDictionary<string, int>`。

2. 支援匿名型別: 允許在不事先定義類別的情況下建立物件。匿名型別的宣告必須使用 var 關鍵字。
```csharp
var person = new { Name = "John Smith" };
```
- 建立了一個匿名型別的物件，其具有一個名為 Name 的屬性，值為 "John Smith"。由於匿名型別沒有明確的名稱，因此必須使用 var 來宣告變數。

要注意，var 關鍵字在 C# 中提供了簡化程式碼和支援匿名型別的功能，使程式碼更簡潔、更具彈性。但需要注意的是，var 並非萬能，只有在編譯器可以明確推斷出類型的情況下才能使用。過度使用 var 可能會降低程式碼的可讀性，因此應謹慎使用。

參考資料：
[dotnetcademy](https://dotnetcademy.net/Learn/2036/Pages/13)
[適用於初學者的 C# 基礎知識](https://learn.microsoft.com/zh-tw/shows/c-fundamentals-for-absolute-beginners/17)