---
title: C# - class 建立與使用
date: 2025-01-15 20:04:35
tags: [dotnet]
categories: dotnet
---

class 是物件導向程式設計 (OOP) 語言的核心功能。它通過邏輯性地組織方法和屬性，來表示並封裝特定的問題概念。

## 建立 class 與 instance

假設要建立一 person 的 class 

```csharp

public class Person
{

}

public class Program
{
	public static void Main()
	{
		Person person = new Person();
	}
}

```

<!-- more -->

## 新增 Method 與 property

```csharp
using System;

public class Person
{
    public string FirstName { get; set; }
    public string LastName { get; set; }

    public string GetFullName()
    {
        return "John Smith";
    }
}


public class Program
{
    public static void Main()
    {
        // 建立 Person 類別的實例
        Person person = new Person();
        // 呼叫方法
        animal.GetFullName();
    }
}

```

### 屬性(Properties)的 get 和 set 概念
1. get (讀取)
- 允許讀取屬性的值
- 當程式碼存取屬性值時，就會調用 get。
- 例如：當我們寫 `string sound = animal.Sound;` 時，就會調用 Sound 屬性的 get 存取器。
- get 存取器必須返回與屬性相同類型的值
2. set (寫入)
- 允許賦值給屬性的值。
- 當程式碼賦值給屬性時，就會調用 set 存取器。
- 例如：當我們寫 `animal.Sound = "Hiii";` 時，就會調用 Sound 屬性的 set。
-  set 有一個隱含的 value 參數，它代表賦予屬性的新值。在範例 `set { sound = value; }` 中，`value` 就是 `"Hiii"`。

```csharp

public class Person
{
    // 1. 自動實作屬性寫法
    // 適合用在單純需要存取資料,不需要額外邏輯的情況
    public string FirstName { get; set; }

    // 2. 完整實作屬性寫法
    // 允許我們完全控制屬性的讀取和寫入邏輯。透過明確地定義 get 和 set，可以在存取屬性時執行額外的程式碼，例如驗證、轉換、通知等。
    private string firstName; // 背景欄位
    public string FirstName   // 屬性
    {
        get { return firstName; }// 讀取值
        set { firstName = value; }// 設定值
    }
}

// 使用方式：
Person person = new Person();
person.FirstName = "Hiii";
string nameData = person.FirstName;// 調用 get

```

### 在 get 或 set 中加入額外邏輯
實現資料驗證、格式轉換、通知其他物件等功能。

```csharp
using System;

namespace ProductExample
{
    public class Product
    {
        // 私有字段
        private string name;
        private decimal price;
        private int stock;

        // 屬性 - 商品名稱
        public string Name
        {
            get { return name; }
            set
            {
                if (string.IsNullOrWhiteSpace(value))
                {
                    throw new ArgumentException("Product name cannot be null or empty.");
                }
                name = value;
            }
        }

        // 屬性 - 價格
        public decimal Price
        {
            get { return price; }
            set
            {
                if (value < 0)
                {
                    throw new ArgumentException("Price cannot be negative.");
                }
                price = value;
            }
        }

        // 屬性 - 庫存數量
        public int Stock
        {
            get { return stock; }
            set
            {
                if (value < 0)
                {
                    throw new ArgumentException("Stock cannot be negative.");
                }
                stock = value;
            }
        }

        // 只讀屬性 - 總價 (計算屬性)
        public decimal TotalValue
        {
            get { return price * stock; }
        }

        // 方法 - 更新庫存
        public void UpdateStock(int quantity)
        {
            if (quantity < 0 && Math.Abs(quantity) > stock)
            {
                throw new InvalidOperationException("Cannot reduce stock below zero.");
            }
            stock += quantity;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
            try
            {
                // 建立 Product 物件
                Product product = new Product
                {
                    Name = "Laptop",
                    Price = 1200.50m,
                    Stock = 10
                };

                // 顯示初始資料
                Console.WriteLine($"Product: {product.Name}");
                Console.WriteLine($"Price: {product.Price:C}");
                Console.WriteLine($"Stock: {product.Stock}");
                Console.WriteLine($"Total Value: {product.TotalValue:C}");

                // 更新庫存
                product.UpdateStock(-3);
                Console.WriteLine("\nAfter selling 3 units:");
                Console.WriteLine($"Stock: {product.Stock}");
                Console.WriteLine($"Total Value: {product.TotalValue:C}");

                // 嘗試設定無效價格
                product.Price = -100;
            }
            catch (Exception ex)
            {
                Console.WriteLine($"\nError: {ex.Message}");
            }
        }
    }
}


```


## constructor 建構子
在建立類別的新實例 (instance) 時自動執行。它的主要目的是初始化物件的狀態，確保物件在建立後立即處於可用狀態。
 **建構子的宣告：**
    - **名稱：** `Person`（必須與類別名稱相同）。
    - **關鍵字：** 使用 `public` 讓建構子可以從外部訪問。
    - **參數：** 在小括號內定義建構子所需的參數（此例中為 `string firstName` 和 `string lastName`）。


```csharp
using System;

public class Person
{
    // 屬性定義
    public string FirstName { get; set; }
    public string LastName { get; set; }

    // 建構子定義
    public Person(string firstName, string lastName)
    {
        // 將傳入的參數值賦值給屬性
        FirstName = firstName;
        LastName = lastName;
    }

    // 方法定義
    public void Introduce()
    {
        Console.WriteLine($"Hi, my name is {FirstName} {LastName}.");
    }
}

public class Program
{
    public static void Main()
    {
        // 使用建構子創建 Person 類別的實例
        Person person = new Person("John", "Doe");

        // 呼叫方法
        person.Introduce();
    }
}
```

### 建構子多載 (Constructor Overloading)
這些建構子可以接受不同數量的參數或不同類型的參數，以提供不同的初始化方式。

另外，如果類別沒有明確定義任何建構子，C# 編譯器會自動產生一個預設建構子 (Default Constructor) 或無參數建構子 (Parameterless Constructor)。 這個預設建構子不接受任何參數，並且會將物件的欄位初始化為其預設值 (例如，int 為 0，string 為 null)。

```csharp
public class Person
{
    public string Name { get; set; }
    public int Age { get; set; }

    // 建構子 1：接受姓名和年齡
    public Person(string name, int age)
    {
        Name = name;
        Age = age;
    }

    // 建構子 2：只接受姓名 (使用建構子鏈結呼叫建構子 1)
     public Person(string personName)
    {
        Name = personName;
        Age = 0; // 明確設定年齡為 0
    }

    // 建構子 3：無參數建構子
    public Person()
    {
        Name = "Unknown";
        Age = -1;
    }
}

public class Program
{
    public static void Main(string[] args)
    {
        Person person1 = new Person("John", 30); // 使用建構子 1
        Person person2 = new Person("Jane");    // 使用建構子 2
        Person person3 = new Person();        // 使用建構子 3

        Console.WriteLine($"{person1.Name}, {person1.Age}"); // 輸出：John, 30
        Console.WriteLine($"{person2.Name}, {person2.Age}"); // 輸出：Jane, 0
        Console.WriteLine($"{person3.Name}, {person3.Age}"); // 輸出：Unknown, -1
    }
}
```
說明：
在這個範例中，Person 類別定義了三個建構子：一個接受姓名和年齡，一個只接受姓名，還有一個無參數建構子。
- 注意，Constructor Overloading 是根據參數的 數量 和 類型 來區分的，而不是參數名稱。因此，若建構子具有相同的參數類型和數量，編譯器會認為它們是相同的，這就會導致編譯錯誤，因為它無法選擇其中一個構造函數來調用。


參考資料：
[dotnetcademy](https://dotnetcademy.net/Learn/2036/Pages/13)
[適用於初學者的 C# 基礎知識](https://learn.microsoft.com/zh-tw/shows/c-fundamentals-for-absolute-beginners/17)