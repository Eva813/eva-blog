---
title: SOLID 物件導向
date: 2025-02-24 15:28:09
tags: [dotnet]
categories: dotnet
---

- S 指的是 Single responsibility principle(SRP) 單一職責原則
- O 指的是 Open/close principle(OCP) 開放/封閉原則
- L 指的是 Liskov substitution principle(LSP) Liskov 替換原則
- I 指的是 Interface Segregation Principle(ISP) 介面隔離原則
- D 指的是 Dependency Inversion Principle(DIP) 依賴反轉原則


## Single responsibility principle(SRP) 單一職責原則

一個 class 別應該只有一個職責，並且只應該因為一個理由而改變。


C# 程式碼範例：

以違反 SRP 的範例：
在這個範例中，User 類別同時負責使用者資料的管理、資料庫儲存、發送歡迎信和使用者驗證等多個職責。這違反了 SRP。

```csharp

public class User
{
    public string Username { get; set; }
    public string Password { get; set; }
    public string Email { get; set; }

    public void SaveUserToDatabase() { /* 資料庫儲存邏輯 */ }
    public void SendWelcomeEmail() { /* 發送歡迎信邏輯 */ }
    public bool ValidateUser() { /* 使用者驗證邏輯 */ return true; }
}
```

符合 SRP 的範例：

```csharp

public class User
{
    public string Username { get; set; }
    public string Password { get; set; }
    public string Email { get; set; }
}

public class UserRepository
{
    public void SaveUser(User user) { /* 資料庫儲存邏輯 */ }
}

public class EmailService
{
    public void SendWelcomeEmail(User user) { /* 發送歡迎信邏輯 */ }
}

public class UserValidator
{
    public bool ValidateUser(User user) { /* 使用者驗證邏輯 */ return true; }
}
```

在這個範例中，我們將不同的職責分離到不同的類別中：
- User 類別只負責使用者資料的管理。
- UserRepository 類別負責資料庫儲存。
- EmailService 類別負責發送歡迎信。
- UserValidator 類別負責使用者驗證。
這樣做使得每個類別都只有一個職責，並且只因為一個理由而改變，符合了 SRP。


### 從前端的解度來理解

在前端開發中，我們經常使用元件 (Component) 的概念來構建使用者介面。一個元件通常負責渲染一部分的 UI，並處理與該部分 UI 相關的互動。這與 SRP 的概念非常相似。一個好的前端元件應該只負責單一的職責，例如：

顯示資料： 只負責接收資料並將其渲染到 UI 上。
處理使用者輸入： 只負責處理使用者在介面上的輸入事件，例如按鈕點擊、表單提交等。
發送 API 請求： 只負責向後端發送 API 請求並處理回應。

<!-- more -->

#### 這個範例中，ProductList 元件同時負責：

- 渲染產品列表： products.map 部分。
- 處理加入購物車的邏輯： handleAddToCart 函數。
- 本地儲存購物車資料： localStorage.setItem。
- 追蹤加入購物車的事件： trackAddToCartEvent 函數。


```JavaScript
// 違反 SRP 的元件
function ProductList({ products }) {
  const [cart, setCart] = useState([]);

  const handleAddToCart = (product) => {
    // 同時處理加入購物車和本地儲存
    setCart([...cart, product]);
    localStorage.setItem('cart', JSON.stringify([...cart, product]));

    // 同時處理追蹤事件
    trackAddToCartEvent(product);
  };

  const trackAddToCartEvent = (product) => {
    // 追蹤加入購物車的事件，例如發送到 GA
    // ... 追蹤程式碼 ...
    console.log(`Product ${product.name} added to cart.`);
  };

  return (
    <ul>
      {products.map((product) => (
        <li key={product.id}>
          {product.name} - ${product.price}
          <button onClick={() => handleAddToCart(product)}>Add to Cart</button>
        </li>
      ))}
    </ul>
  );
}

```

可以調整，將不同的職責分離到不同的函數或元件中：
ProductList 元件只負責渲染產品列表和處理按鈕點擊事件，並透過 onAddToCart prop 將加入購物車的事件傳遞給父元件。
CartManager 負責管理購物車的狀態、本地儲存和追蹤事件。

```javaScript
// 符合 SRP 的元件
function ProductList({ products, onAddToCart }) {
  return (
    <ul>
      {products.map((product) => (
        <li key={product.id}>
          {product.name} - ${product.price}
          <button onClick={() => onAddToCart(product)}>Add to Cart</button>
        </li>
      ))}
    </ul>
  );
}

function CartManager() {
    const [cart, setCart] = useState([]);

    const handleAddToCart = (product) => {
        setCart([...cart, product]);
        localStorage.setItem('cart', JSON.stringify([...cart, product]));
        trackAddToCartEvent(product); // 呼叫追蹤函數
    };

    const trackAddToCartEvent = (product) => {
        // 追蹤加入購物車的事件
        console.log(`Product ${product.name} added to cart.`);
    };

    return (
        <div>
            <ProductList products={products} onAddToCart={handleAddToCart} />
        </div>
    );
}
```



## Open/close principle(OCP) 開放/封閉原則

- 對擴展開放 (Open for extension)： 允許新增功能或行為。
- 對修改封閉 (Closed for modification)： 不應該修改既有的程式碼

以生物分類學為背景，將「雞」和「火龍」視為不同的生物種類。它們之間有一些共同的特性（例如都會啼叫、用兩隻腳走路），也有各自獨特的特性（例如火龍會吐火、會飛）。

這個例子要說明的是，當我們需要新增「火龍」這個角色時，如果直接修改「雞」的類別，就會違反 OCP，並且造成程式碼的混亂。正確的做法是透過抽象化，建立更通用的概念（例如「雉科」、「飛龍種」），然後讓「雞」和「火龍」分別繼承這些概念，並實作各自獨特的行為。

### 初始設計（未應用 OCP）：

```csharp
public class Chicken
{
    public void MakeSound()
    {
        Console.WriteLine("Cock-a-doodle-doo!");
    }

    public void Walk()
    {
        Console.WriteLine("Walking on two legs.");
    }
}

// 如果要新增 Dragon，直接修改 Chicken 類別，違反 OCP
// public class Chicken
// {
//     ...
//     public void Fly() // 這樣做不合理
//     {
//         // ...
//     }
// }
```

應用 OCP 的設計：

```csharp
// 定義一個抽象的生物介面
public interface IAnimal
{
    void MakeSound();
    void Walk();
}

// 雉科
public class Phasianidae : IAnimal
{
    public virtual void MakeSound() // 使用 virtual 允許子類別覆寫
    {
        Console.WriteLine("Generic bird sound.");
    }

    public void Walk()
    {
        Console.WriteLine("Walking on two legs.");
    }
}

// 雞
public class Chicken : Phasianidae
{
    public override void MakeSound() // 覆寫 MakeSound 方法
    {
        Console.WriteLine("Cock-a-doodle-doo!");
    }
}

// 飛龍
public class Wyvern : IAnimal
{
    public virtual void MakeSound()
    {
        Console.WriteLine("Roar!");
    }

    public void Walk()
    {
        Console.WriteLine("Running on two legs.");
    }

    public void Fly()
    {
        Console.WriteLine("Flying in the sky.");
    }

    public void FireBreath()
    {
        Console.WriteLine("Breathing fire!");
    }
}

// 火龍
public class Dragon : Wyvern
{
    public override void MakeSound()
    {
        Console.WriteLine("ROAR!!!"); // 更大的叫聲
    }
}

public class Program
{
    public static void Main(string[] args)
    {
        Chicken chicken = new Chicken();
        chicken.MakeSound(); // Cock-a-doodle-doo!
        chicken.Walk();      // Walking on two legs.

        Dragon dragon = new Dragon();
        dragon.MakeSound();  // ROAR!!!
        dragon.Walk();       // Running on two legs.
        dragon.Fly();        // Flying in the sky.
        dragon.FireBreath(); // Breathing fire!

        Console.ReadKey();
    }
}
```

透過 IAnimal 介面和抽象類別 Phasianidae 和 Wyvern，成功地將「雞」和「火龍」的共同行為和特有行為分離開來。新增「火龍」的功能不需要修改 Chicken 類別。

### 以前端角度來認識

能夠添加新功能而不需要修改現有的代碼。以下以 React 元件設計方式來舉例。

將書籍類型，有區分Premium、Free 2種，Book 元件同時負責渲染書籍的基本信息（如 title 和 image）以及處理不同類型的邏輯（如 type 和按鈕行為）。這種設計將多個責任耦合在一起，降低了元件的可維護性。

```csharp
const Book = ({ title, image, type, onClickFree, onClickPremium }: IBook) => {
  const handleReadPremium = () => {
    // Some logic
    onClickPremium();
  };

  const handleReadFree = () => {
    // Some logic
    onClickFree();
  };

  return (
    <div>
      <img src={image} />
      <p>{title}</p>
      {type === "Premium" && (
        <button onClick={handleReadPremium}>Add to cart +</button>
      )}
      {type === "Free" && <button onClick={handleReadFree}>Read</button>}
    </div>
  );
};

```

嘗試遵循 OCP：
Book 元件的核心邏輯（如渲染書籍的基本信息）保持不變，無需因新增書籍類型而修改。
新的書籍類型（如 PremiumBook 或 FreeBook）可以通過組合和擴展來實現，而不需要改動 Book 元件的代碼。

```javaScript
const Book = ({ title, image, children }: IBook) => {
  return (
    <div>
      <img src={image} />
      <p>{title}</p>
      {children}
    </div>
  );
};

const PremiumBook = ({ title, image, onClick }: IBook) => {
  return (
    <Book title={title} image={image}>
      <button onClick={onClick}>Add to cart +</button>
    </Book>
  );
};

const FreeBook = ({ title, image, onClick }: IBook) => {
  return (
    <Book title={title} image={image}>
      <button onClick={onClick}>Read</button>
    </Book>
  );
};

```

新的書籍類型可以通過創建新的子元件（如 PremiumBook 或 FreeBook）來實現，這些子元件可以自由定義自己的行為和內容。

例如，若需要新增一個 ExclusiveBook 類型，只需創建一個新的元件：

```JavaScript
const ExclusiveBook = ({ title, image, onClick }: IBook) => {
  return (
    <Book title={title} image={image}>
      <button onClick={onClick}>Exclusive Access</button>
    </Book>
  );
};
```

##  Liskov substitution principle(LSP) Liskov 替換原則

LSP 強調的是繼承關係中的「可替換性」。也就是說，任何使用父類別的地方，都應該可以毫無問題地替換成子類別的實例，而不需要修改任何程式碼。

```csharp
public class Bird
{
    public virtual void Fly()
    {
        Console.WriteLine("Flying!");
    }
}

public class Penguin : Bird
{
    public override void Fly()
    {
        throw new Exception("Penguins can't fly!"); // 企鵝無法飛行，拋出例外
    }
}

public class Program
{
    public static void Main(string[] args)
    {
        List<Bird> birds = new List<Bird>
        {
            new Bird(),
            new Penguin()
        };

        foreach (Bird bird in birds)
        {
            try
            {
                bird.Fly(); // 迴圈中呼叫 Fly 方法，企鵝會拋出例外
            }
            catch (Exception ex)
            {
                Console.WriteLine(ex.Message); // 捕捉例外並印出訊息
            }
        }

        Console.ReadKey();
    }
}
```

Penguin 繼承了 Bird，並覆寫了 Fly 方法，使其拋出例外。這違反了 LSP，因為我們無法在不修改程式碼的情況下，用 Penguin 的實例替換 Bird 的實例。
- 需要重新設計繼承關係，區分會飛的鳥類和不會飛的鳥類。

```csharp
// 定義一個會飛的鳥類的介面
public interface IFlyingBird
{
    void Fly();
}

// 鳥類基底類別 (可以不用是 abstract class)
public class Bird {
  // 通用方法
    public virtual void MakeSound()
    {
        Console.WriteLine("Generic bird sound.");
    }
     
    public virtual string Eat()
    {
        return $"{Name} is eating.";
    }
}

// 會飛的鳥類
public class Sparrow : Bird, IFlyingBird
{
    public void Fly()
    {
        Console.WriteLine("Sparrow is flying!");
    }
    public override void MakeSound()
    {
        Console.WriteLine("Chirp!");
    }
}

// 不會飛的鳥類
public class Penguin : Bird
{
    public override void MakeSound()
    {
        Console.WriteLine("Squawk!");
    }
}

public class Program
{
    public static void Main(string[] args)
    {
        List<Bird> birds = new List<Bird>
        {
            new Sparrow(),
            new Penguin()
        };

        foreach (Bird bird in birds)
        {
            bird.MakeSound(); // 所有鳥類都會發出叫聲，符合預期
            if(bird is IFlyingBird flyingBird) {
                flyingBird.Fly(); // 只有會飛的鳥類才會執行 Fly 方法
            }
        }

        Console.ReadKey();
    }
}

```

符合 LSP 的範例中：

我們定義了一個 IFlyingBird 介面，只有會飛的鳥類才會實作這個介面。
Sparrow 繼承了 Bird 並且實作了 IFlyingBird 介面。
Penguin 只繼承了 Bird，沒有實作 IFlyingBird 介面。

 **Liskov 替換原則的遵守**：
    - 所有 `Bird` 子類別都可以在需要 `Bird` 的地方被替換，且行為符合預期。
    - 當需要特定行為（如飛行）時，可以使用介面進行額外約束，而不影響父類別的正確性。


### 以前端角度來認識

[Examples of Liskov Substitution Principle in React and Typescript (React Advanced Concepts)]( https://www.youtube.com/watch?v=JkDg4-TPzL8&t=143s)

LSP 的核心在於「行為子型別」（Behavioral Subtyping），即子型別應該能夠完全取代超型別，並且行為保持一致。以下是影片中如何實現 LSP 的步驟：

一個基本的對話框元件，具有標題 (header)、內容 (content) 和按鈕 (footer) 部分。
它被視為一個 superType。

```javascript
// 假設的 Dialog 元件程式碼
import React from 'react';

interface DialogProps {
  header: React.ReactNode;
  content: React.ReactNode;
  footer: React.ReactNode;
}
export type DialogType<T = {}> = React.FC<DialogProps & T>;
export type RegularDialogType = DialogType<{
  className?:string
}>

const Dialog: Dialog: RegularDialogTyp = (props) => {
  const { header, content, footer, className= 'dialog' } = props;

    return (
        <div className={`dialog ${className}`}>
            <div className={`${className}-header`}>{header}</div>
            <div className={`${className}-content`}>{content}</div>
            <div className={`${className}-footer`}>{footer}</div>
        </div>
    )
}
export default Dialog;
```

聖誕節對話框 (XmasDialog) 元件

```javascript
// 假設的 XmasDialog 元件程式碼
import React from 'react';

import { RegularDialogTyp, Dialog } from './dialog';

export const XmasDialog: RegularDialogTyp = (props) => {
  const { header, content, footer } = props;
  return (
    <Dialog
      className="xmas-dialog"
      header={header}
      content={content}
      footer={footer}
    />
  );
};


```


以上範例，
- Dialog 作為 SuperType：定義了標題 (header)、內容 (content)、頁腳 (footer) 等基本屬性，所有對話框都應該符合這些屬性。
- XmasDialog 作為子型別：它與 Dialog 使用相同的 props，確保 可替換性，但透過 className 改變外觀。
- 不改變行為： LSP 不僅要求子類型和 SuperType 具有相同的介面，還要求它們的行為一致，當使用子類型替換 SuperType 時，所有屬性 (props) 都被傳遞，且元件的功能應該保持不變 （如，XmasDialog 必須像 Dialog 一樣運作，不能缺少任何部分或停止運作）


### 組合 (Composition) 而非繼承 (Inheritance)

在傳統 OOP 設計中，我們可能會讓 XmasDialog 繼承 Dialog，但這樣做會帶來以下問題：

❌ 耦合度高：如果 Dialog 內部發生變化，XmasDialog 可能會受影響，導致潛在的錯誤。
❌ 可維護性差：新增其他節日主題時，需要一直擴展 Dialog，導致程式碼複雜度增加。

因此，在這範例選擇 組合 (Composition) 來設計 XmasDialog，而不是直接繼承 Dialog：

XmasDialog 內部使用 Dialog，但只改變 className，避免邏輯耦合。
這種方式可以輕鬆擴展其他節日版本，例如 HalloweenDialog 或 NewYearDialog，而不影響 Dialog 的核心實作。

### 條件渲染 (Conditional Rendering)

以下根據應用狀態決定要顯示一般對話框 (Dialog) 或聖誕節對話框 (XmasDialog)。可以使用以下方式：
- 透過 appStore.hasSales('xmas') 判斷是否為聖誕節，並動態選擇 XmasDialog 或 Dialog。
- 確保在不同情境下應用的行為仍然一致，不會影響 UI 的運作。

```javascript
import React from 'react';
import { Dialog } from './dialog';
import { XmasDialog } from './xmasDialog';
import { appStore } from './helpers';

export const App: React.FC<{}> = () => {
  const Component = appStore.hasSales('xmas') ? XmasDialog : Dialog;

  return (
    <div className="app">
      <Component
        header={<h3>Profile Details</h3>}
        content={
          <>
            <input type="text" placeholder="Your name..." />
            <input type="text" placeholder="Your surname..." />
          </>
        }
        footer={<button>Save</button>}
      />
    </div>
  );
};

```

在這邊，保持 Liskov 替換原則 (LSP) 一致性：
- Component 可以指向 Dialog 或 XmasDialog，但它們的介面完全相同，因此可以互換。
- 無論顯示哪個對話框，應用的邏輯不會受到影響。

---

## Interface Segregation Principle(ISP) 介面隔離原則

不應該強迫客戶端依賴它們不需要的介面。將大型介面拆分成小型、更具體的介面，可以提高系統的靈活性和可維護性。


IMultiFunctionPrinter 介面包含了列印、掃描和傳真等多個職責。SimplePrinter 類別只需要列印功能，但它被迫實作了 Scan 和 Fax 方法，這違反了 ISP。

```csharp
public interface IMultiFunctionPrinter
{
    void Print();
    void Scan();
    void Fax();
}

public class OldPrinter : IMultiFunctionPrinter
{
    public void Print() { /* 實作列印 */ }
    public void Scan() { /* 實作掃描 */ }
    public void Fax() { /* 實作傳真 */ }
}

public class SimplePrinter : IMultiFunctionPrinter // 只需要列印功能
{
    public void Print() { /* 實作列印 */ }
    public void Scan() { throw new NotImplementedException(); } // 不需要掃描，被迫實作
    public void Fax() { throw new NotImplementedException(); } // 不需要傳真，被迫實作
}
```


```csharp
public interface IPrinter
{
    void Print();
}

public interface IScanner
{
    void Scan();
}

public interface IFax
{
    void Fax();
}

public class GoodPrinter : IPrinter, IScanner, IFax
{
    public void Print() { /* 實作列印 */ }
    public void Scan() { /* 實作掃描 */ }
    public void Fax() { /* 實作傳真 */ }
}

public class SimplePrinter : IPrinter // 只實作需要的介面
{
    public void Print() { /* 實作列印 */ }
}
```

## 前端 JavaScript 範例：

在前端 JavaScript 中，雖然沒有像 C# 的介面，但我們可以使用物件或函數來模擬介面的效果。


違反 ISP 的範例：

```JavaScript

const userActions = {
    login: () => { /* 登入邏輯 */ },
    logout: () => { /* 登出邏輯 */ },
    createUser: () => { /* 建立使用者邏輯 */ },
    deleteUser: () => { /* 刪除使用者邏輯 */ }
};

// 只需要登入和登出的元件被迫依賴不需要的 createUser 和 deleteUser
function AuthComponent() {
    const handleLogin = () => userActions.login();
    const handleLogout = () => userActions.logout();
    return (
        <div>
            <button onClick={handleLogin}>Login</button>
            <button onClick={handleLogout}>Logout</button>
        </div>
    );
}
```

符合 ISP 的範例：

```JavaScript

const authActions = {
    login: () => { /* 登入邏輯 */ },
    logout: () => { /* 登出邏輯 */ }
};

const adminActions = {
    createUser: () => { /* 建立使用者邏輯 */ },
    deleteUser: () => { /* 刪除使用者邏輯 */ }
};

function AuthComponent() {
    const handleLogin = () => authActions.login();
    const handleLogout = () => authActions.logout();
    return (
        <div>
            <button onClick={handleLogin}>Login</button>
            <button onClick={handleLogout}>Logout</button>
        </div>
    );
}

function AdminComponent() {
    const handleCreateUser = () => adminActions.createUser();
    const handleDeleteUser = () => adminActions.deleteUser();
    return (
        <div>
            <button onClick={handleCreateUser}>Create User</button>
            <button onClick={handleDeleteUser}>Delete User</button>
        </div>
    );
}
```

在這個範例中，我們將使用者操作拆分為 authActions 和 adminActions 兩個物件，分別包含不同的職責。AuthComponent 只需要依賴 authActions，AdminComponent 只需要依賴 adminActions，避免了不必要的依賴。
一個元件不應該接收過多的 props 或提供過多的方法，而是應該只接收它需要的 props 和提供它需要的方法。這可以提高元件的重用性和可維護性。


## Dependency Inversion Principle(DIP) 依賴反轉原則

### 什麼是高階模組和低階模組？
高階模組不應依賴於低階模組，兩者皆應依賴於抽象實體

1. 高階模組 (High-level Modules)： 這些模組包含應用程式的核心業務邏輯和控制流程。它們定義了應用程式的整體行為，通常比較抽象，關注的是「做什麼」，而不是「怎麼做」。例如：
訂單處理模組 (Order Processing Module)
使用者驗證模組 (User Authentication Module)
報表產生模組 (Report Generation Module)
2. 低階模組 (Low-level Modules)： 這些模組負責具體的實作細節，例如資料庫存取、檔案操作、網路通訊等。它們關注的是「怎麼做」，而不是「做什麼」。例如：
資料庫存取模組 (Database Access Module)
檔案系統模組 (File System Module)
郵件發送模組 (Email Sending Module)

以假設 情境： 

```csharp
// 低階模組：PayPal 付款
public class PayPalPayment
{
    public void ProcessPayment(decimal amount)
    {
        Console.WriteLine($"Processing payment of {amount} via PayPal.");
        // 實際的 PayPal 付款邏輯
    }
}

// 高階模組：購物車
public class ShoppingCart
{
    private readonly PayPalPayment _payPalPayment; // 直接依賴 PayPalPayment

    public ShoppingCart()
    {
        _payPalPayment = new PayPalPayment();
    }

    public void Checkout(decimal totalAmount)
    {
        _payPalPayment.ProcessPayment(totalAmount);
        Console.WriteLine("Order completed.");
    }
}

public class Program
{
    public static void Main(string[] args)
    {
        ShoppingCart cart = new ShoppingCart();
        cart.Checkout(100);

        Console.ReadKey();
    }
}
```

符合 DIP 的範例：

```csharp
// 抽象介面：付款方式
public interface IPaymentProcessor
{
    void ProcessPayment(decimal amount);
}

// 低階模組：PayPal 付款
public class PayPalPayment : IPaymentProcessor
{
    public void ProcessPayment(decimal amount)
    {
        Console.WriteLine($"Processing payment of {amount} via PayPal.");
        // 實際的 PayPal 付款邏輯
    }
}

// 低階模組：Stripe 付款
public class StripePayment : IPaymentProcessor
{
    public void ProcessPayment(decimal amount)
    {
        Console.WriteLine($"Processing payment of {amount} via Stripe.");
        // 實際的 Stripe 付款邏輯
    }
}

// 高階模組：購物車
public class ShoppingCart
{
    private readonly IPaymentProcessor _paymentProcessor; // 依賴介面

    public ShoppingCart(IPaymentProcessor paymentProcessor) // 建構子注入
    {
        _paymentProcessor = paymentProcessor;
    }

    public void Checkout(decimal totalAmount)
    {
        _paymentProcessor.ProcessPayment(totalAmount);
        Console.WriteLine("Order completed.");
    }
}

public class Program
{
    public static void Main(string[] args)
    {
        // 使用 PayPal 付款
        IPaymentProcessor payPalPayment = new PayPalPayment();
        ShoppingCart cart1 = new ShoppingCart(payPalPayment);
        cart1.Checkout(100);

        Console.WriteLine();

        IPaymentProcessor stripePayment = new StripePayment();
        ShoppingCart cart2 = new ShoppingCart(stripePayment);
        cart2.Checkout(200);

        Console.ReadKey();
    }
}
```


### 以前端角度來認識
以表單元件的重用，當有一個表單元件需要在不同情境下重用時，例如創建和編輯書籍的表單，它們的 UI 相同但邏輯不同。
解決方案: 抽象出一個接口（如onSubmit函數），讓表單元件依賴於這個抽象接口，而不是直接依賴於API請求的實現。
抽象出一個接口（如onSubmit函數），讓表單元件依賴於這個抽象接口，而不是直接依賴於API請求的實現。

```javascript
// 抽象接口
const BookForm = ({ onSubmit }) => {
  return (
    <form onSubmit={onSubmit}>
      <input name="title" />
      <input name="author" />
      <input name="bookType" />
    </form>
  );
};

// 創建書籍的邏輯
const CreateBookForm = () => {
  const handleCreateBook = async (e) => {
    try {
      const formData = new FormData(e.currentTarget);
      await axios.post("https://myapi.com/books", formData);
    } catch (err) {
      console.error(err.message);
    }
  };

  return <BookForm onSubmit={handleCreateBook} />;
};

// 編輯書籍的邏輯
const EditBookForm = () => {
  const handleEditBook = async (e) => {
    try {
      // 編輯書籍的邏輯
    } catch (err) {
      console.error(err.message);
    }
  };

  return <BookForm onSubmit={handleEditBook} />;
};

```



[Using the Dependency Inversion Principle (DIP) in React](https://adeesh.hashnode.dev/using-the-dependency-inversion-principle-dip-in-react)
[Dependency Inversion Principle in React](https://dev.to/mikhaelesa/dependency-inversion-principle-in-react-5287)