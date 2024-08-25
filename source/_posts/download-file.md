---
title: 如何在前端實現文件下載：以 Excel 文件為例
date: 2024-08-18 13:51:02
tags: ["JS"]
---

在前端開發中，有時需要從後端提供數據並生成文件提供用戶下載。以下將以下載 Excel 為例，介绍如何使用 axios 從後端取得文件，並在前端處理文件的下載過程。我们還會顯示如何從 HTTP header 中提取文件名，以 f 確保下載的文件命名正確。

## Api 設定

要記得設置告诉 axios 期望接收的類型為 blob，若是不設置，則會預設為 json，收到的資料會是亂碼。

```js

  exportFile(searchData) {
    return axios.get("/data/export", {
      params: searchData,
      responseType: "blob"
    });
  }

```

## 前端處理文件下載

獲取到後端返回的文件數據後，我們需要在前端將其轉換為可下載的文件格式。以下是一個完整的示例，展示瞭如何將 blob 數據生成 Excel 文件並觸發下載。

```js
const handleExportFile = async () => {
  try {
    const res = await exportFile(data);

    const blob = new Blob([res.data], {
      type: "application/vnd.openxmlformats-officedocument.spreadsheetml.sheet",
    });

    const downloadUrl = window.URL.createObjectURL(blob);
    const link = document.createElement("a");
    link.href = downloadUrl;

    const fileName = "export.xlsx";
    link.setAttribute("download", fileName);
    document.body.appendChild(link);
    link.click();
    link.remove();

    window.URL.revokeObjectURL(downloadUrl);
  } catch (error) {
    console.error(error);
  }
};
```

### new Blob([res.data], { type: ... }) 做了什麼？

這里，用 new Blob() 創建了一個 Blob 對象。
res.data 是從後端 API 接收到的文件數據。
我們將這個數據放入 Blob 中，同時指定了文件的類型，即 `application/vnd.openxmlformats-officedocument.spreadsheetml.sheet`，這是 Excel 文件的 MIME 類型。這樣瀏覽器就能識別出這是一個 Excel 文件。

### 動態創建一個 `<a>` 標簽，並通過模擬點擊觸發文件下載。

使用 window.URL.createObjectURL(blob) 創建了一個特殊的 URL，它指向我們剛才創建的 Blob 對象。
可以理解為它生成了一個臨時的下載地址，使用者可以透過這個地址下載文件。
接著 document.createElement("a") 創建 `<a>` 標籤，接下來要利用這個標籤來實現文件下載。
也就是 link.href = downloadUrl; 給 `<a>` 標籤賦值，將剛才生成的 downloadUrl 設定為這個 `<a>` 標籤的 link 地址 (href)，也就是說，點擊這個 link 會指向我們的 Blob 物件（即文件資料）。

### 設定文件名並觸發下載

使用 setAttribute 方法給`<a>` 標籤添加一個 download 屬性，並設定文件名為 "export.xlsx"。這樣，當用戶點擊鏈接時，瀏覽器會提示下載文件，並自動將文件保存為 export.xlsx。
document.body.appendChild 將 `<a>` 標籤臨時加到 document 的 body 中。雖然使用者不會看到這個 link，但它在頁面上是存在的。
然後模擬一次使用者點擊這個連結 (link.click())，觸發文件的下載過程。
link.remove();下載操作完成後，我們把這個臨時創建的 `<a>` 標籤從頁面中移除。

<!-- more -->

## 使用 header 的 content-disposition 取得檔名

有時，後端會通過 Content-Disposition 頭信息傳遞文件名。為了確保下載時使用正確的文件名，我們可以從響應頭中解析文件名。
這時候要注意前端， axios 是如何處理 response (你能拿到的回傳內容是否有無 header)
需要從回傳的 response headers 取得檔名，這樣才能下載時有正確的檔名。

```js
headers:{
    "access-control-allow-origin": "http://localhost:5143",
    "access-control-expose-headers": "*",
    "content-disposition": "attachment; filename=123123_202408.xlsx; filename*=UTF-8''123123_202408.xlsx",
    "content-length": "12111",
    "content-type": "application/vnd.openxmlformats-officedocument.spreadsheetml.sheet",
     ...
}
```

```js
const handleExportFile = async () => {
  try {
    $q.loading.show();
    const data = {
      sourceId: Number(route.params.id),
      month: followQuery.month,
    };
    const res = await getFollowMonthlySummaryDownload(data);
    $q.loading.hide();

    const blob = new Blob([res.data], {
      type: "application/vnd.openxmlformats-officedocument.spreadsheetml.sheet",
    });

    const downloadUrl = window.URL.createObjectURL(blob);
    const link = document.createElement("a");
    link.href = downloadUrl;

    // 解析 content-disposition 來取得檔名
    const contentDisposition = res.headers["content-disposition"];
    let fileName = "default-filename.xlsx";

    if (contentDisposition) {
      const fileNameMatch = contentDisposition.match(
        /filename\*=UTF-8''([^;]+)/
      );
      if (fileNameMatch) {
        // 使用 decodeURIComponent 解碼 UTF-8 編碼的檔名
        fileName = decodeURIComponent(fileNameMatch[1]);
      } else {
        // 如果沒有 UTF-8 檔名，嘗試匹配普通檔名
        const fileNameSimpleMatch =
          contentDisposition.match(/filename=([^;]+)/);
        if (fileNameSimpleMatch) {
          fileName = fileNameSimpleMatch[1].trim();
        }
      }
    }
    link.setAttribute("download", fileName);
    document.body.appendChild(link);
    link.click();
    link.remove();

    window.URL.revokeObjectURL(downloadUrl);
  } catch (error) {
    console.error(error);
  }
};
```
