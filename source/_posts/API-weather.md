---
title: 串接API- 天氣預報
date: 2021-02-23 14:42:02
tags: ["Ajax",  "API"]
categories: Ajax 實作練習
---
[成品](https://eva813.github.io/Eva_portfolio/weatherAPI/weatherAPI.html)

![](https://i.imgur.com/odl0KcP.png)
# 觀察功能需求
* 從公開的氣象資料平台取得氣象資料
* 建立下拉式選單，選擇地區，並按下按鈕取得氣象資料
* 呈現當日天氣狀況(包含氣溫、圖示、描述)
* 呈現未來一週的天氣預報(包含日期、星期;氣溫、圖示、描述)

## 了解要串接的資料open data
至中央氣象局-氣象開放資料平台的網站，註冊會員
註冊並登入之後，要取得授權碼
![](https://i.imgur.com/KoRQyNL.png)
到[中央氣象局開放資料平臺之資料擷取API](https://opendata.cwb.gov.tw/dist/opendata-swagger.html)，查看要取用的資料
我的授權碼：CWB-F0145DA5-2539-4333-BAFD-466910C1EECC

參考資料：
[https://www.youtube.com/watch?v=QDdn3yrsyCQ](https://www.youtube.com/watch?v=QDdn3yrsyCQ)
[[Day 13 - 即時天氣] 建立一個即時天氣 App - 前置準備](https://ithelp.ithome.com.tw/articles/10222662)

## 尋找要參考實現畫面呈現的資料
畫面主要樣式是參考：
[Build a Simple Weather App With Vanilla JavaScript](https://webdesign.tutsplus.com/tutorials/build-a-simple-weather-app-with-vanilla-javascript--cms-33893)

## JS部分
### 1.利用Ajax串接資料

* 根據平台提供的url作為取得資料的地方
* 利用sucess/error，來查看是否有成功取得資料
    * 依據console.log()出要串接的資料
![](https://i.imgur.com/Kdm159o.png)


```javascript
function getCity() {
  let url = "https://opendata.cwb.gov.tw/api/v1/rest/datastore/F-D0047-091?Authorization=CWB-F0145DA5-2539-4333-BAFD-466910C1EECC&format=JSON";

  $.ajax({
    type: "get",
    url: url,

    dataType: "json",
    success: function (data) {
      console.log(data);
   
      })

    },
    error: function (err) {
      console.log('oh no')
    }
  });
```

### 2.製作下拉式選單

* 當串到想要的資料後，希望能在下拉選單，呈現出各個地區，以供選擇
![](https://i.imgur.com/I2QTip1.png)
* 因為地區有很多，以迴圈方式來填入資料
    * `i`的長度 `data.records.locations[0].location.length`
     ![](https://i.imgur.com/QaNMU8d.png)
    * `opt = $('<option></option>')`創立選項標籤
    * 利用`attr`,`html`填入該地區的index，以及地區名稱
    ![](https://i.imgur.com/xc7bwaP.png)



```javascript
function getCity() {
  let url = "https://opendata.cwb.gov.tw/api/v1/rest/datastore/F-D0047-091?Authorization=CWB-F0145DA5-2539-4333-BAFD-466910C1EECC&format=JSON";

  $.ajax({
    type: "get",
    url: url,

    dataType: "json",
    success: function (data) {
      //console.log(data);
      let selectCity = $('#selector-list');
      for (let i = 0; i < data.records.locations[0].location.length; i++) {
        let opt = $('<option></option>');
        opt.attr("data-index", i);
        opt.html(data.records.locations[0].location[i].locationName);
        selectCity.append(opt);
      }
     

    },
    error: function (err) {
      console.log('oh no')
    }
  });


};
```

### 3.點選(submit)按鈕時要跳出取得今日天氣以及未來一週的天氣
* 在sucess:中放入取得今日天氣、週天氣的函式
    * 在此要宣告`selectedCityIndex`變數，並取得被選取的city index`$('#selector-list').get(0).selectedIndex`
```javascript
function getCity() {
  let url = "https://opendata.cwb.gov.tw/api/v1/rest/datastore/F-D0047-091?Authorization=CWB-F0145DA5-2539-4333-BAFD-466910C1EECC&format=JSON";

  $.ajax({
    type: "get",
    url: url,

    dataType: "json",
    success: function (data) {
      console.log(data);
      let selectCity = $('#selector-list');
      for (let i = 0; i < data.records.locations[0].location.length; i++) {
        let opt = $('<option></option>');
        opt.attr("data-index", i);
        opt.html(data.records.locations[0].location[i].locationName);
        selectCity.append(opt);
      }
      $('button').click(function (e) {
        e.preventDefault();
        clear();
        let selectedCityIndex = $('#selector-list').get(0).selectedIndex;
        getWeather(data, selectedCityIndex);
        weekWeather(data, selectedCityIndex);
      })

    },
    error: function (err) {
      console.log('oh no')
    }
  });


};
```
[jQuery獲取Select選擇的Text和Value：](https://codertw.com/%E5%89%8D%E7%AB%AF%E9%96%8B%E7%99%BC/287007/)


### 4.取得今日天氣(`getWeather();`)

* 傳入參數分別為data\locationIndex:data為串接到的天氣資料,locationIndex，為放入要取的資料中的index
* 我們想要取得的氣象資料，分別有地區、溫度(最低溫、最高溫)、天氣描述、icon圖示
    * `name`變數就是要取得資料內的地區名稱
![](https://i.imgur.com/SZZSUnh.png)
    * `weather`變數就是取得氣象資料
![](https://i.imgur.com/pVwq3xe.png)
    * `weatherDescription`以及`weatherCode`分別就是氣象描述以及給予氣象描述的編號
 ![](https://i.imgur.com/qeUH1iw.png)
* `let weatherImg = checkImg(weatherCode);`依據天氣描述的分類放入對應圖示
* 抓取到所需要的變數資料後，就是將要填入呈現畫面的html標籤以及變數放入
* `after()`在該區塊後面在插入想要呈現的其他標籤內容

```javascript
function getWeather(data, locationIndex) {

  //console.log(data, locationIndex)
  let name = data.records.locations[0].location[locationIndex].locationName;
  let weather = data.records.locations[0].location[locationIndex].weatherElement;
  //console.log(name);
  //console.log(weather);
  // 天氣描述
  let weatherDescription = weather[6].time[0].elementValue[0].value;
  let weatherCode = weather[6].time[0].elementValue[1].value;
  let minTemp = weather[8].time[0].elementValue[0].value;
  let maxTemp = weather[12].time[0].elementValue[0].value;
  let weatherImg = checkImg(weatherCode);
  // 今天日期
  // let date = getDate().toUTCString();

  console.log(weatherCode);

  let li = $('<li></li>').appendTo('.cities');
  li.addClass("city");
  li.html(`
  <h2 class="city-name" data-name="${name},">
    <span>${name}</span>
    <sup>Today</sup>
  </h2>
  <div class="city-temp">${Math.round(minTemp)}<sup>°C</sup> ~ ${Math.round(maxTemp)}<sup>°C</sup>
  </div>
  <figure class='weather-icon'>
    ${weatherImg}
    <figcaption>${weatherDescription}</figcaption>
  </figure>
`);
  $('.cities').after(`<h2 class="sec-h2">未來一週預報</h2>`)
  $('.cities').append(li);

};

```

### 5.根據weather code分類，分配的圖示(`checkImg()`)
* 將分類的代碼區分到各個天氣狀況
* 依分類代碼下判斷，並回傳要放入的圖示
* `weatherTypes.isThunderstorm.includes(weatherData)`
    * 利用`includes()`取得 weatherTypes中各天氣情形陣列中，是否有符合參數的數值
```javascript
function checkImg(code) {
  let weatherData = +code;
  const weatherTypes = {
    isThunderstorm: [15, 16, 17, 18, 21, 22, 33, 34, 35, 36, 41],
    isClear: [1],
    isCloudyFog: [25, 26, 27, 28],
    isCloudy: [2, 3, 4, 5, 6, 7],
    isFog: [24],
    isPartiallyClearWithRain: [
      8, 9, 10, 11, 12,
      13, 14, 19, 20, 29, 30,
      31, 32, 38, 39,
    ],
    isSnowing: [23, 37, 42],
  };
  // console.log(data)
  // console.log(weatherTypes.isPartiallyClearWithRain)

  // let s = weatherTypes.isThunderstorm.includes(weatherData);
  // console.log(s);



  if (weatherTypes.isThunderstorm.includes(weatherData)) {
    return `<img class="city-icon" src="./img/thunderstorm.png" alt="weather-img">`;
  } else if (weatherTypes.isClear.includes(weatherData)) {
    return `<img class="city-icon" src="./img/clear.png" alt="weather-img">`;
  } else if (weatherTypes.isCloudyFog.includes(weatherData)) {
    return `<img class="city-icon" src="./img/cloudyfog.png" alt="weather-img">`;
  } else if (weatherTypes.isCloudy.includes(weatherData)) {
    return `<img class="city-icon" src="./img/cloud-and-sun.png" alt="weather-img">`;
  } else if (weatherTypes.isFog.includes(weatherData)) {
    return `<img class="city-icon" src="./img/fog.png" alt="weather-img">`;
  } else if (weatherTypes.isPartiallyClearWithRain.includes(weatherData)) {
    return `<img class="city-icon" src="./img/clearwithrainy.png" alt="weather-img">`;
  } else { return `<img class="city-icon" src="./img/snow.png" alt="weather-img">`; }

}

```
[[Day 21 - 即時天氣] 處理天氣圖示以及 useMemo 的使用](https://ithelp.ithome.com.tw/articles/10225927)
[JavaScript Array 陣列操作方法大全 ( 含 ES6 )](https://www.oxxostudio.tw/articles/201908/js-array.html#array_includes)
includes(weatherData):[JavaScript 陣列處理：找東西 - indexOf、$.inArray 與 filter](https://cythilya.github.io/2017/05/08/javascript-find-item-in-an-array/)
[預報因子欄位中文說明表](https://opendata.cwb.gov.tw/opendatadoc/MFC/D0047.pdf)

---
### 6.取得未來一週的天氣
* 此作法與取的當日天氣有些類似，不過因為要取得未來一週6天的資料，所以須以迴圈的方式來取的並填入資料
* `let i = 1; i < 7; i++`從1開始，6天
* `timeIndex= 2 * i`是為了抓取一週時間所設置的index
![](https://i.imgur.com/xSanS8F.png)
* 日期、星期的取得
    * 以算出tomorrow為目標，並帶入i，就會執行出往後六天的日期
 ![](https://i.imgur.com/ZqSmPOy.png)
 * 並從上圖，分別擷取出月份、日期、星期的資料



```javascript
function weekWeather(data, locationIndex) {
  // $('.week-ul').html('');
  var weather = data.records.locations[0].location[locationIndex].weatherElement;
  for (let i = 1; i < 7; i++) {
    let timeIndex = 2 * i;
    let weekday = $('<li></li>').appendTo('.week-ul');
    weekday.addClass('day').attr('id', `day-${i}`);

    let weatherDescription = weather[6].time[timeIndex].elementValue[0].value;
    let weatherCode = weather[6].time[timeIndex].elementValue[1].value;
    let minTemp = weather[8].time[timeIndex].elementValue[0].value;
    let maxTemp = weather[12].time[timeIndex].elementValue[0].value;
    let weatherImg = checkImg(weatherCode);
    //console.log(weatherDescription);

    //////日期、星期的擷取//////
    let today = new Date();
    let tomorrow = new Date(today);
    tomorrow.setDate(tomorrow.getDate() + i);
    let weekDay = tomorrow.getDay();
    let weekMonth = tomorrow.getMonth()
    let date = tomorrow.getDate();
    const dayNamesEn = ['Sun', 'Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat'];
    const monthNamesEn = [
      'Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun',
      'Jul', 'Aug', 'Sep', 'Oct', 'Nov', 'Dec'
    ]
    let inweekDay = dayNamesEn[weekDay]
    let inweekMonth = monthNamesEn[weekMonth];
    let indate = date;
    ///////////


    weekday.html(`<li class="day">
          <h2 class="whichDay" data-name="">
            <span>${indate} ${inweekMonth}</span>
            <sup>${inweekDay}</sup>
          </h2>
          <div class="week-temp">${minTemp}<sup>°C</sup> ~ ${maxTemp}<sup>°C</sup>
          </div>
          <figure class='weather-icon'>
            ${weatherImg}
            <figcaption>${weatherDescription}</figcaption>
          </figure>
        </li>`)
    $('.week-ul').append(weekday);
  }
}
```

[抓取明天日期:How to get tomorrow's date using JavaScript](https://flaviocopes.com/how-to-get-tomorrow-date-javascript/)
[日期與時間](https://eyesofkids.gitbooks.io/javascript-start-from-es6/content/part3/datetime.html)

### 7.當文件準備好

* 先執行要串接的資料
* 於選擇地區時，跳出提醒文字

```javascript
$(document).ready(function (e) {
  getCity();

  $('#selector-list').change(function () {
    $('.msg').text('別忘了按下SUBMIT');
  })


});
```
### 8.清除資料
* 建立在點選submit後，要先執行的步驟，為清除原本頁面的資料
```javascript
function clear() {
$('.cities').html('');
  $('.sec-h2').empty('');
  $('.week-ul').html('');
  $('.msg').text('');
```
[JQuery中html、append、appendTo、after、insertAfter](https://blog.csdn.net/sunpeiyumail/article/details/7665668)

