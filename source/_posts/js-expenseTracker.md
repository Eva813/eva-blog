---
title: JS實作- expense tracker 記帳簿
date: 2021-01-10 10:08:01
tags: ["JS","local storage","forEach","append"]
---
完成後的[成品](https://eva813.github.io/Eva_portfolio/expenseTracker/expenseTracker.html)
![](https://i.imgur.com/VSjpRSa.png)

# 觀察須製作的功能
  1. 重新整理網頁後，保有過去執行的狀態：localstorage
    * 實作內有使用:
    * localstorage:setItem,getItem
    * jason.stringfy/jason.parse

![](https://i.imgur.com/abUVHGM.png)

![](https://i.imgur.com/c53IDMj.png)

 2. 在new transation輸入資料後，會將資料傳入history中
    * add transation之後，頁面不會刷新(spa)(e.preventdefault)
 3. 摸到history中的項目，會出現刪除符號，點擊後刪除

![](https://i.imgur.com/X06EsA4.png)

4. 輸入的金額，會顯示於上方income、expense，並且計算總額
    * 有正負值
    * 計算收入、花費
![](https://i.imgur.com/uofQVuf.png)

---

# 呈現完整語法
```javascript
$(document).ready(function () {
  //預設值的設置，取得存在本地的資料或是空陣列
  var transactions = JSON.parse(localStorage.getItem('Transactions')) || [];

  //陣列初始
  if (transactions.length > 0) {
    initHistory(transactions);
  }
  //點擊按鈕的事件
  $('.btn').click(function (e) {
    e.preventDefault();
    //console.log('click');
    //取得表格中的值
    const text_val = $('#text').val();
    const amount_val = $('#amount').val();
    let id = generateID();

    addTransactions(id, text_val, amount_val, transactions);

    //推入陣列
    transactions.push({
      id: id,
      name: text_val,
      amount: amount_val
    })


    localStorage.setItem('Transactions', JSON.stringify(transactions));


  })
  updateValue(transactions);

});


//在list中插入
function addTransactions(id, name, amount, transactions) {
  console.log('amount:', amount);

  const Transaction_str = $('<li></li>').appendTo('#list');

  Transaction_str.addClass(amount < 0 ? 'minus' : 'plus');
  Transaction_str.html(`${name}<span> ${amount}</span><button class="delete-btn"  data-id="${id}">x</button>`);
  $('#list').append(Transaction_str);


  $('#text').val('');
  $('#amount').val('');

  //刪除鈕 要交易交出後再綁事件
  $('.delete-btn').last().click(function () {
    $(this).parent().remove();
    let id = $(this).data('id');
    //console.log(id);
    deleteFromLocalstorage(transactions, id); //要記得傳入id

  });

}
//從localstorage刪除
//要記得在參數放入id
function deleteFromLocalstorage(transactions, id) {
  transactions.forEach(function (item, index, arr) {
    //console.log('item', item);
    //console.log('index', index);
    //console.log('arr', arr);
    if (item.id === id) {
      arr.splice(index, 1);
    }
  });
  //迴圈刪除後，要儲存到localStorage才有確實刪去
  localStorage.setItem('Transactions', JSON.stringify(transactions));
}


// Generate random ID
function generateID() {
  return Math.floor(Math.random() * 100000000);
}


//數值更新計算
function updateValue(transactions) {
  const amounts_arr = transactions.map(function (transaction) {
    return transaction.amount
  })

  console.log(amounts_arr);

  //計算加總 
  var total = 0;
  $.each(amounts_arr, function () { total += parseFloat((this)) || 0; });
  //傳回YOUR BALANCE
  $('#balance').text(`$${total}`);
  console.log(total);


  //*************************** */
  //從陣列找出>0的值，放置income
  //console.log(amounts)
  var income = amounts_arr.filter(
    function (item) {
      return item > 0
    }
  )
  console.log(income);

  var totalIncome = 0;
  $.each(income, function () { totalIncome += parseFloat((this)) || 0; });

  console.log(totalIncome); //回傳220
  //傳回到income
  $('#money-plus').text(`$${totalIncome}`);
  //***************************** */
  //從陣列找出<0的值，放置income
  var expense = amounts_arr.filter(
    function (item) {
      return item < 0
    }
  )
  console.log(expense);

  var totalExpense = 0;
  $.each(expense, function () { totalExpense += parseFloat((this)) || 0; });

  console.log(totalExpense);
  //傳回到expense
  $('#money-minus').text(`$${totalExpense}`)


}

//init
function initHistory(transactions) {
  transactions.forEach(transaction => {
    addTransactions(transaction.id, transaction.name, transaction.amount, transactions);
  });
  updateValue(transactions);

}


```

# 各項目拆解：

## `add transations`按下去之後，頁面不刷新，直接將資料傳入紀錄中

```javascript
$(document).ready(function () {
  $('.btn').click(function () {
    console.log('click');
  })
});
//點擊後，有印出click，但很快的刷新頁面
```
* 加入`e.preventDefault();` 用來阻止預設動作的發生

![](https://i.imgur.com/4y0J1vA.png)

## 讓transation輸入後顯示

```javascript
$(document).ready(function () {
  $('.btn').click(function (e) {
    e.preventDefault();
    console.log('click');
    addTransactions('cash', '200');
  })
});


function addTransactions(name, amount) {
  var Transaction_str = `<li class='plus' >${name}<span> ${amount}</span><button class="delete-btn">x</button></li>`
  $('#list').append(Transaction_str);
}

//
```

![](https://i.imgur.com/Vh9uPvJ.png)

* 提交之後清空
    * 選擇該輸入框，並利用`val()`，使內部的值清空

```javascript
 $('#text').val('');
  $('#amount').val('');

```

![](https://i.imgur.com/uKnX7fd.png)

## 從表格中取得值
* 製作按鈕的點擊事件，取得表格中的值
* 將值傳入函式的參數`addTransactions(text_val, amount_val);`
```javascript
$(document).ready(function () {
  $('.btn').click(function (e) {
    e.preventDefault();
    console.log('click');
    
    //取得表格中的值
    const text_val = $('#text').val();
    const amount_val = $('#amount').val();
    addTransactions(text_val, amount_val);
  })

});
```

## 製作刪除紐
* 原本的撰寫方式，為選到最後一個刪除鈕，並點擊加以刪除
* 此方式造成，無法確實刪除資料

```javascript
$('.delete-btn').last().click(function () {
    console.log('del');
  })
}
```

![](https://i.imgur.com/v7n8t3L.png)
* 修改方式，只能綁定一筆transatin或是綁定最後一筆
![](https://i.imgur.com/lhqdwYA.png)

* 查看若實現點擊後刪除，是會刪除什麼？
```javascript
 $('.delete-btn').last().click(function () {
    console.log(this);
  })
}
```
![](https://i.imgur.com/9dYre8g.png)

* 要刪除的是整筆資料，所以要去尋找他鄰近的元素
```javascript
$('.delete-btn').last().click(function () {
    $(this).parent().remove();
  })
```

## localstorage 執行

筆記補充：[LocalStorage介紹](https://eva813.github.io/2021/01/10/localstorage/)

從此實作，可以看到它是一個陣列，裡面是物件
* 設置一個空陣列
* 並將物件push進去

```javascript
//預設值的設置，取得存在本地的資料或是空陣列
  var transactions = JSON.parse(localStorage.getItem('Transactions')) || [];
  
   //推入陣列
    transactions.push({
      name: text_val,
      amount: amount_val
    })
    localStorage.setItem('Transactions', JSON.stringify(transactions));
  
```

![](https://i.imgur.com/HC2XCpT.png)

![](https://i.imgur.com/0b4mWW6.png)

* 增加初始function
```javascript
//init
function initHistory(transactions) {
  transactions.forEach(transaction => {
    addTransactions(transaction.name, transaction.amount);
  });

}

//將此函式放於 $(document).ready()之中

//陣列初始
  if (transactions.length > 0) {
    initHistory(transactions);
  }
```

![](https://i.imgur.com/dH3nJ6K.png)

## 將transation刪除後，並沒有完全從localstorage刪除
運用id來刪除
* 從此段落：[localstorage 執行](#localstorage-執行)，圖片中的存取陣列可以看到，沒有設置id

* id是在addTransactions使用
```javascript
// Generate random ID
function generateID() {
  return Math.floor(Math.random() * 100000000);
}

//floor：無條件捨去
//ceil：無條件進位

```
* 在創造的陣列中\物件，都加入id
```javascript
   const id = generateID();
    addTransactions(id, text_val, amount_val);

    //推入陣列
    transactions.push({
      id: id,
      name: text_val,
      amount: amount_val
    })

```
* 在addTransactions的函式中也要加入id
```javascript
function addTransactions(id, name, amount) {

  console.log(id, name, amount);}
  
```


![](https://i.imgur.com/sMtoogJ.png)

* 在button加入data-id

和資料有關的時候：data-原來欄位名稱


```javascript
const Transaction_str = `<li class='plus' >${name}<span> ${amount}</span><button class="delete-btn"  data-id="${id}">x</button></li>`
```

![](https://i.imgur.com/cmamimU.png)

![](https://i.imgur.com/v8TQspA.png)

* 確定點擊刪除鈕的時後，是有抓到該id
```javascript

  $('.delete-btn').last().click(function () {
    $(this).parent().remove();
    let id = $(this).data('id');
    console.log(id);
  })
```
* `let id = $(this).data(); 查看id時` 取得的是物件
 ![](https://i.imgur.com/BeBkXAN.png)
* 應改為
![](https://i.imgur.com/snvF7AX.png)

{%note warning%}
此時還沒有真正刪除id，只是確定有抓到id :point_up_2: 
{%endnote%}

{%note info%}
jquery `data()`： 自定義屬性 (data attributes)，讓我們可以任意讀寫資料在元素上，而且不會影響頁面的 layout
[jQuery Data](https://www.fooish.com/jquery/data.html)
{%endnote%}

* #### 利用forEach、splice()刪除陣列內的資料
```javascript
function deleteFromLocalstorage(transactions, id) {
  transactions.forEach(function (item, index, arr) {
    //console.log('item', item);
    //console.log('index', index);
    //console.log('arr', arr);
    if (item.id === id) {
      arr.splice(index, 1);
    }
  });
  //迴圈刪除後，要儲存到localStorage才有確實刪去
  localStorage.setItem('Transactions', JSON.stringify(transactions));
}
````


* 查看迴圈的資料
![](https://i.imgur.com/FexS5lt.png)


* 確實刪除存在localstorage的資料
```javascript
localStorage.setItem('Transactions', JSON.stringify(transactions));
```

### 判斷輸入的數字大小，並給予class
* 放在addTransactions()函式之中

```javascript
const Transaction_str = $('<li></li>').appendTo('#list');

  Transaction_str.addClass(amount < 0 ? 'minus' : 'plus');
  Transaction_str.html(`${name}<span> ${amount}</span><button class="delete-btn"  data-id="${id}">x</button>`);
  $('#list').append(Transaction_str);
  
```
![](https://i.imgur.com/NYXCDtS.png)

 
### 數值的更新

![](https://i.imgur.com/EdCN1cd.png)


```javascript
function updateValue(transactions) {
  const amounts_arr = transactions.map(function (transaction) {
    return transaction.amount
  })

  console.log(amounts_arr);

  //計算加總 
  var total = 0;
  $.each(amounts_arr, function () { total += parseFloat((this)) || 0; });
  //傳回YOUR BALANCE
  $('#balance').text(`$${total}`);
  console.log(total);


  //*************************** */
  //從陣列找出>0的值，放置income
  //console.log(amounts)
  var income = amounts_arr.filter(
    function (item) {
      return item > 0
    }
  )
  console.log(income);
  var totalIncome = 0;
  $.each(income, function () { totalIncome += parseFloat((this)) || 0; });

  //console.log(totalIncome); //回傳220
  //傳回到income
  $('#money-plus').text(`$${totalIncome}`);
  //***************************** */
  //從陣列找出<0的值，放置income
  var expense = amounts_arr.filter(
    function (item) {
      return item < 0
    }
  )
  console.log(expense);

  var totalExpense = 0;
  $.each(expense, function () { totalExpense += parseFloat((this)) || 0; });

  console.log(totalExpense);
  //傳回到expense
  $('#money-minus').text(`$${totalExpense}`)


}
```
* 創造一個amount的陣列

```javascript
  const amounts_arr = transactions.map(function (transaction) {
    return transaction.amount
  })
  console.log(amounts_arr);

```

![](https://i.imgur.com/sG0YS9J.png)

* 計算加總數值
```javascript
var total = 0;
  $.each(amounts_arr, function () { total += parseFloat((this)) || 0; });
  //傳回YOUR BALANCE
  $('#balance').text(`$${total}`);
  console.log(total);

```

![](https://i.imgur.com/7c2MqFo.png)

![](https://i.imgur.com/CCXAirK.png)

* income 收入加總計算

```javascript
//從陣列找出>0的值，放置income
  //console.log(amounts)
  var income = amounts_arr.filter(
    function (item) {
      return item > 0
    }
  )
  console.log(income);
  var totalIncome = 0;
  $.each(income, function () { totalIncome += parseFloat((this)) || 0; });

  //console.log(totalIncome); //回傳220
  //傳回到income
  $('#money-plus').text(`$${totalIncome}`);
```

![](https://i.imgur.com/9d8Hx3Y.png)

![](https://i.imgur.com/KXRi18A.png)

* expense支出加總

```javascript
//從陣列找出<0的值，放置expense
  var expense = amounts_arr.filter(
    function (item) {
      return item < 0
    }
  )
  console.log(expense);

  var totalExpense = 0;
  $.each(expense, function () { totalExpense += parseFloat((this)) || 0; });

  console.log(totalExpense);
  //傳回到expense
  $('#money-minus').text(`$${totalExpense}`)

```

![](https://i.imgur.com/AaIZORC.png)

![](https://i.imgur.com/TM4EQrH.png)

---
錯誤修正：

* 調換推入陣列以及`addTransactions`的順序
![](https://i.imgur.com/7jdJ7kO.png)

```javascript
//點擊按鈕的事件
  $('.btn').click(function (e) {
    e.preventDefault();
    //console.log('click');
    //取得表格中的值
    const text_val = $('#text').val();
    const amount_val = $('#amount').val();
    let id = generateID();

    //推入陣列
    transactions.push({
      id: id,
      name: text_val,
      amount: amount_val
    });

    addTransactions(id, text_val, amount_val, transactions);




    localStorage.setItem('Transactions', JSON.stringify(transactions));

    updateValue(transactions);
  })
```
* 將updateValue()放入事件中

```javascript
//刪除鈕 要交易交出後再綁事件
  $('.delete-btn').last().click(function () {
    $(this).parent().remove();
    let id = $(this).data('id');
    //console.log(id);
    deleteFromLocalstorage(transactions, id); //要記得傳入id
    updateValue(transactions);
  });
```
