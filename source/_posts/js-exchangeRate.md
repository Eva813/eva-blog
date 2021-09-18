---
title: JS實作- exchangeRate匯率轉換器
date: 2021-01-10 09:50:30
tags: ["JS","ajax","input"]
---
## 實作需求分析

![](https://i.imgur.com/cu80NjP.png)

* 抓取api提供的匯率資料：使用jQuery,ajax
* 選擇不同幣別，進行匯率換算
* swap按鈕，使幣別交換

#### 完成後的[成品](https://eva813.github.io/Eva_portfolio/exchangeRate/exchangeRate.html)

## jQuery.ajax 使用

* 參考資料：[jQuery.ajax()
](https://api.jquery.com/jquery.ajax/)

```javascript
$.ajax({
  method: "get",
  url: "https://v6.exchangerate-api.com/v6/06f04b805743079a1966771a/latest/USD",
 
})
  .done(function( some_data ) {
    console.log(data);
    //成功抓到值的話，就會印出抓取到的資料
  }).fail(function(){
  
  })
```

![](https://i.imgur.com/S1kOYmH.png)

* 應用於exchangeRate:
```javascript
$.ajax({
    url: `https://v6.exchangerate-api.com/v6/06f04b805743079a1966771a/latest/${currency_one}`,  //注意符號
    method: 'get',//get,post,put
    dataType: 'json',
    success: function (data) {
      //console.log(data);
      const rate = data.conversion_rates[currency_two];
      //console.log(rate);
      $('#rate').text(`1 ${currency_one} = ${rate} ${currency_two}`);
      $('#amount-two').val((amountOne * rate).toFixed(2));
    }
  })

```
* #### 詳細說明可以參照內文：[計算匯率calculate函式](#計算匯率calculate-函式)



#### 其他參考資料：

[[ Alex 宅開發 ] 👨‍💻從 jQuery 入門到認識 JavaScript #5 Ajax 與非同步功能操作與原始碼探索](https://www.youtube.com/watch?v=z-hN7GY5K7g)

{%note warning%}
- `url`:來源，api的位置在哪裏
- `type`:api的呼叫方式
- `dataType`:它回給你的資料類型
- `data`:你要傳給它什麼
{%endnote%}

```javascript


$.ajax({
  url: data.json,
  type: 'get',//get,post,put,path,delete
  dataType: 'json', //html,xml,text,jsonp
  data: {}

}).then(function (res) {
  //response <-> request
  console.log(res);
  return def.resolve();
}), function (err) {
  console.log(err);
  return def.reject();
}

```





## 計算匯率calculate 函式
* 此函式為抓取匯率api的資料並帶入計算匯率

```javascript
function calculate() {

  var currency_one = $("#currency-one").val();
  var currency_two = $('#currency-two').val();
  var amountOne = $('#amount-one').val();
  var amountTwo = $('#amount-two').val();

  //https://app.exchangerate-api.com/dashboard/confirmed

  $.ajax({
    url: `https://v6.exchangerate-api.com/v6/06f04b805743079a1966771a/latest/${currency_one}`,//注意符號
    method: 'get',//抓取值
    dataType: 'json',
    success: function (data) {
      //console.log(data);  //查看抓到的物件
      
      const rate = data.conversion_rates[currency_two];
      //console.log(rate);
      
      $('#rate').text(`1 ${currency_one} = ${rate} ${currency_two}`);
      
      //將值帶入
      $('#amount-two').val((amountOne * rate).toFixed(2));
    }
  })


}
```
### 函式說明:
#### 1.先將要抓取的值，宣告變數;分別有選擇的國家幣別、兌換的數量

#### 2.串接資料
 (1). 抓取資料的位置
` url: https://v6.exchangerate-api.com/v6/06f04b805743079a1966771a/latest/${currency_one}`

{%note warning%}
`"https://v6.exchangerate-api.com/v6/06f04b805743079a1966771a/latest/USD"`
注意網址後面有修改，要抓取會變動的變數： ${currency_one}
{%endnote%}

 (2) 檢查是否有抓到要的資料：
* 宣告：`const rate = data.conversion_rates[currency_two];`
* console.log(rate); 

![](https://i.imgur.com/LKGiCmN.png)

*  確定抓取的值是否正確：`console.log($('#amount-two').val());`

![](https://i.imgur.com/r9Nx25o.png)


* 將計算匯率帶入
 ` $('#amount-two').val((amountOne * rate).toFixed(2));`

![](https://i.imgur.com/oMBuNFV.png)


      

## 其他輸入值的抓取與連動


* ####  `#currency-one的改變`，會連動上面的函式計算結果

```javascript
$("#currency-one").change(function () {
  $("#rate").html(""); 
  calculate();
})
```

```javascript

//抓取amountone
$("#amount-one").on("input", function () {
  $("#rate").html("");
  calculate();
})

$("#currency-two").change(function () {
  $("#rate").html("");
  calculate();
})

//抓取amountTwo
$("#amount-two").on("input", function () {
  $("#rate").html("");
  calculate();
})
```

* #### 發現使用`.append()`，會出現累積div文字的問題
* (1) 清除函式`.append()`，累積div文字呈現的問題
`$("#rate").html(""); `

* (2) 將原本使用`.append()`的地方，修改為`.text()`, `` $('#rate').text(`1 ${currency_one} = ${rate} ${currency_two}`);``
    * 位置：[計算匯率calculate函式](#計算匯率calculate-函式)

![](https://i.imgur.com/GTMuXY0.png)

![](https://i.imgur.com/TvspVAq.png)




## swap按鈕 （使幣別交換）
* 宣告一個變數，紀錄交換前的幣別`let temp = $("#currency-one").val();`
* 確認是否宣告正確：` console.log(temp);`

![](https://i.imgur.com/gkYsHxQ.png)

![](https://i.imgur.com/fAZmC0Q.png)


```javascript
//swap按鈕
$('#swap').click(function () {
  let temp = $("#currency-one").val();//設一個變數來存放currency-one
  $("#currency-one").val($("#currency-two").val());
  $("#currency-two").val(temp);
  calculate();
})

calculate(); //放於最後
```
* `console.log($("#currency-one").val());`
* `console.log($("#currency-two").val());`



![](https://i.imgur.com/o4JLcIF.png)


---
參考資料：
[重新認識 JavaScript: Day 16 那些你知道與不知道的事件們](https://ithelp.ithome.com.tw/articles/10192175)
