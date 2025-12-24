---
title: 建立名片自動建檔系統（Google Drive + Sheets + Gemini）
date: 2025-12-24 12:50:09
tags: ["自動化","Google Apps Script","Gemini AI"]
categories: 自動化
---

在實際工作中，我發現同事在拿到名片後，仍然需要經過一連串人工流程：

- 手機拍照、或是掃描名片
- 傳到電腦
- 手動輸入公司、姓名、電話
- 還很容易漏資料或輸錯

因此我開始思考：
能不能在不架設伺服器、不導入複雜系統的情況下，把這件事自動化？

這篇文章記錄我實作的一套 名片自動建檔系統，核心完全建立在 Google 生態系上。


## 系統目標與整體概念

我的目標很單純：
> 只要用手機拍照上傳名片，就能自動解析內容，並寫入 Google Spreadsheet。

整體流程如下：

1.手機直接拍照，上傳到指定的 Google Drive 資料夾

2.由 Google Apps Script 定期掃描新圖片

3.使用 Gemini AI API 辨識名片內容（非單純 OCR），將結構化資料寫入 Google Spreadsheet

4.將處理完成的名片移動到封存資料夾，避免重複處理


### 使用到的工具與技術

- Google Drive：名片圖片的儲存與狀態管理
- Google Sheets：結構化資料表（輕量資料庫）
- Google Apps Script：自動化中樞
- Google Gemini Vision API：影像 + 語意理解

> 這個組合的優點是：
> 不需要額外主機
> 成本低
> 團隊成員幾乎都能上手
> 維護成本非常小


## 資料欄位設計

實際使用時，名片資訊往往比想像中複雜（多支電話、分機、圖示標註），
因此我最後採用以下欄位結構：

- 狀態
- 公司
- 姓名
- 職稱  
- Email
- Mobile（手機）
- Office Phone（公司電話 / 分機）
- 地址
- 建檔日期
- 原始檔案網址

這些欄位會在 Google Spreadsheet 的第一列先設定好，後續 Apps Script 會依照固定順序寫入。

## 第一步：Google Drive 環境準備
先在 Google Drive 建立一個主資料夾，例如：名片管理系統

並在裡面建立兩個子資料夾：

- Inbox（待處理）：
手機拍照後，名片圖片一律上傳到這裡

- Archived（已建檔）：
名片處理完成後會自動移動到這個資料夾

### 重要
請記下這兩個資料夾網址列中 folders/ 後面的 ID，後續 Apps Script 需要使用。

<!-- more -->

## 第二步：Google Spreadsheet 設定

建立一個新的 Google Spreadsheet（試算表），並在第一列設定好所有欄位名稱（順序需與程式碼一致）。

這個 Sheet 將扮演「名片資料庫」的角色。

Apps Script:
![image](https://hackmd.io/_uploads/ryIhRJYX-e.png)



### API Key 與安全性設定

本系統使用 Google Gemini API，需要設定 API Key。

不建議直接把 Key 寫在程式碼中，正確做法是使用 Apps Script 的 Script Properties：

```
PropertiesService
  .getScriptProperties()
  .getProperty('GEMINI_API_KEY')

```

在 sidebar 的設定：
![image](https://hackmd.io/_uploads/BkHIkgt7Wg.png)


## 第三步：部署 Google Apps Script

1.打開 Google Spreadsheet

2.點擊「擴充功能」→「Apps Script」

3.刪除原有程式碼

4.貼上整段 Apps Script 程式

此 Script 的主要工作包含：

- 掃描 Inbox 資料夾中的圖片
- 呼叫 Gemini Vision API 辨識名片
- 將資料寫入 Google Sheet
- 移動檔案至 Archived

程式碼，請參閱：https://gist.github.com/Eva813/7de5cd72e97ff410a38322bb0b3c4d2f

1.主程式入口
```
function processBusinessCards() {
  // 主流程：掃描 Inbox → 呼叫 AI → 寫入 Sheet → 移動檔案
}

```
2.防止電話號碼被誤判為公式
```javascript
const escapeFormula = (text) => {
  if (!text) return '';
  const str = text.toString().trim();
  if (str.startsWith('+') || str.startsWith('=')) {
    return "'" + str;
  }
  return str;
};

```

3.呼叫 Gemini AI API
```javascript
function callGeminiVision(file) {
  // Base64 encode
  // 呼叫 Gemini Vision API
  // 嚴格要求 JSON 回傳
}

```


## 設定自動觸發條件

最後，在 Apps Script 中設定 時間驅動 Trigger：

每小時執行一次、或每日批次處理

這樣只要有新名片上傳，就會自動建檔，不需要人工介入。

## 結語
透過這套系統，我成功將名片建檔流程自動化，大幅減少同事的工作量與錯誤率。
如果你也有類似需求，歡迎參考這篇文章，並根據自己的情況進行調整與擴展！