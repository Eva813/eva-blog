---
title: JS實作- 表單驗證
date: 2021-01-13 09:17:11
tags: ["JS"]
categories: JS實作練習
---
![](https://i.imgur.com/Vhxlk8D.png)

## 觀察功能
1. 製作一個會員申請的表單
  * 帳號
  * email
  * 密碼
2. 輸入框的顏色變化
3. 判斷條件的設置

## 任務拆解

### 抓出要互動的元素

```javascript
const form = document.querySelector('#form');
const username = document.querySelector('#username');
const email = document.querySelector('#email');
const password = document.querySelector('#password');
const password2 = document.querySelector('#password2');
```
* 獲取物件層次中的父物件
* 針對指定元素取得或設置指定的class

```javascript
//show input error message
function showError(input, message) {
  const formControl = input.parentElement;
  formControl.className = 'form-control error';
}


form.addEventListener('submit', function (e) {
  e.preventDefault();
  if (username.value === '') {
    showError(username, 'Username is required');
  } else {
    showSuccess(username);
  }
})

```

```javascript
const small = formControl.querySelector('small');
  small.innerText = 'Username is required';
```
* 修改顯示文字

![](https://i.imgur.com/NvhXJk8.png)

#### 成功輸入

```javascript
//show success outline
function showSuccess(input) {
  const formControl = input.parentElement;
  formControl.className = 'form-control success';
}
```
### 複製相同條件判斷
* 事件為點擊送出之後，會進行條件判斷

```javascript
//event listener
form.addEventListener('submit', function (e) {
  e.preventDefault();

  if (username.value === '') {
    showError(username, 'Username is required');
  } else {
    showSuccess(username);
  };

  if (email.value === '') {
    showError(email, 'Email is required');
  } else {
    showSuccess(email);
  };

  if (password.value === '') {
    showError(password, 'Password is required');
  } else {
    showSuccess(password);
  };

  if (password2.value === '') {
    showError(password2, 'Password 2 is required');
  } else {
    showSuccess(password2);
  };
})

```

![](https://i.imgur.com/shKtSSS.png)

### 設定符合的驗證
* email的輸入應有規範樣式
* 使用正則表達式

![](https://i.imgur.com/vVVWyGG.png)

```javascript
//check email is valid
//js email regex
//https://ithelp.ithome.com.tw/articles/10094951

function isValiEmail(email) {
  const re = /^(([^<>()[\]\\.,;:\s@"]+(\.[^<>()[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/;
  return re.test(String(email).toLowerCase());
}

//
 if (email.value === '') {
    showError(email, 'Email is required');
  } else if (!isValiEmail(email.value)) {
    showError(email, 'Email is not valid');
  }
  else {
    showSuccess(email);
  };

```

![](https://i.imgur.com/gdkHO9O.png)

#### 整合簡化 =>forEach

```javascript
//Check required fields
function checkRequired(inputArr) {
  inputArr.forEach(function (input) {
    if (input.value.trim() === '') {
      showError(input, 'is required');
    } else {
      showSuccess(input);
    }
  });

}
```
* 修改showError，套入名稱

```javascript
if (input.value.trim() === '') {
      //console.log(input.id);//會顯示input的所有id
      showError(input, `${input.id}is required`);
```
### 將getFieldName() function拉出

* 製作訊息的第一個字母大寫

```javascript
//Check required fields
function checkRequired(inputArr) {
  inputArr.forEach(function (input) {
    if (input.value.trim() === '') {
      //console.log(input.id);//會顯示input的所有id
      showError(input, `${getFieldName(input)} is required`);
    } else {
      showSuccess(input);
    }
  });

}


//getFieldName
function getFieldName(input) {
  return input.id.charAt(0).toUpperCase() + input.id.slice(1);
  //將第一個字charAt(0)，變大寫，並再接續後面的字slice(1)
}

```

* charAt()从一个字符串中返回指定的字符,[String.prototype.charAt()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/String/charAt)


## 長度檢查

```javascript

// Check input length
function checkLength(input, min, max) {
  if (input.value.length < min) {
    showError(
      input,
      `${getFieldName(input)} must be at least ${min} characters`
    );
  } else if (input.value.length > max) {
    showError(
      input,
      `${getFieldName(input)} must be less than ${max} characters`
    );
  } else {
    showSuccess(input);
  }
}
```



![](https://i.imgur.com/sAvU95T.png)

### 密碼檢查

```javascript
// check password match
function checkPasswordMatch(input1, input2) {
  if (input1.value !== input2.value) {
    showError(input2, 'Password do not match')
  }
}
```

![](https://i.imgur.com/62UwLS7.png)






參考資料：
[JavaScript初學：DOM常用屬性與方法](https://medium.com/change-or-die/javascript%E5%88%9D%E5%AD%B8-dom%E5%B8%B8%E7%94%A8%E5%B1%AC%E6%80%A7%E8%88%87%E6%96%B9%E6%B3%95-ef851afdb65a)

