---
title: 部署至 Vercel 遇到「Unexpected token '<'」錯誤：原因與解法
date: 2025-10-04 21:21:52
tags: ["Next.js", "side-project"]
categories: side-project
---

最近在部署專案到 Vercel 時，遇到了一個讓我困擾的錯誤訊息：「Unexpected token '<'」。這個錯誤通常表示我們預期收到的是 JSON 格式的資料，但實際上卻收到了 HTML 頁面。本文將分享我遇到這個問題的背景、原因，以及兩種解決方案，幫助大家在使用 Vercel 部署時能夠順利取得 API 資料。

# 部署至 Vercel 遇到「Unexpected token '<'」錯誤：原因與解法

在將專案部署到 **Vercel** 時，當我透過瀏覽器或 extension background fetch 呼叫 API，卻出現以下錯誤：

```
取得資料夾失敗: Unexpected token '<', "<!doctype "... is not valid JSON
```

那代表請求並沒有真正拿到 JSON，而是得到了 HTML 頁面。本文將說明這個問題的原因、背後的背景，以及兩種完整的解法。

## 問題說明

這次在 **Arc 瀏覽器** 的開發過程中，我嘗試從部署於 Vercel 的 API 端點取得資料，但 Console 顯示：

```
index.iife_dev.js:24922 取得資料夾失敗: Unexpected token '<', "<!doctype "... is not valid JSON
```

進一步到 **Arc 的 Service-Worker DevTools**（`arc://inspect/#service-workers`）中，打開 background worker 的 Console，可以看到請求的回應實際上是一份 HTML 登入頁面，而非 JSON 資料。

---

## 背景補充：Vercel 的 Deployment Protection

這個錯誤並非來自 **CORS**，而是因為 Vercel 的 **Deployment Protection** 機制。
當專案開啟「Vercel Authentication」後，所有 **Preview Deployments** 都需要登入才能訪問。

因此，extension background 在發出請求時，被導向至登入頁面（HTML），導致解析 JSON 時出現：

> `Unexpected token '<'`

<!-- more -->

---

## 解決方案

Vercel 提供了兩種方式可以處理這個情況：

### 一、（建議）關閉 Preview 部署的 Vercel Authentication

這是最簡單、直接的方式，適合開發階段測試使用。

1. 進入你的 **Vercel 專案 Dashboard**。
2. 打開 **Settings → Deployment Protection**。
3. 在 **Vercel Authentication** 區塊中，將 **Preview Deployments** 的開關關閉。
4. 等待 Vercel 自動重新部署完成。

完成後，你的 Preview 連結就不再需要登入。
此時從 extension background 發出的 fetch 請求，會直接拿到正確的 `200 + JSON` 回應。

> 🔗 參考文件：[Vercel Authentication 介紹 – Vercel](https://vercel.com/docs/deployment-protection)

---

### 二、維持保護但使用「Protection Bypass for Automation」

如果你希望保留預覽保護機制，同時又讓自動化流程（例如 extension background）能夠訪問 API，可以使用 **Protection Bypass for Automation**。

設定步驟如下：

1. 前往 **Deployment Protection** 頁面，啟用 **Protection Bypass for Automation**。

2. 系統會產生一組密鑰：`VERCEL_AUTOMATION_BYPASS_SECRET`。

3. 在程式中呼叫 API 時，加上以下 Header：

   ```jsx
   fetch(API_BASE + '/api/v1/folders', {
     headers: {
       'x-vercel-protection-bypass': '<YOUR_BYPASS_SECRET>',
     },
   })
   ```

4. 再次執行 fetch，即可拿到正確的 JSON 回應，不需關掉保護。

> 🔗 參考文件：[Methods to bypass Deployment Protection – Vercel](https://vercel.com/docs/deployment-protection/methods-to-bypass-deployment-protection)

---

## 驗證流程

設定完成後，可依以下步驟驗證是否成功：

1. 在 **Arc 的 Service-Worker DevTools → Network** 面板中觸發一次 fetch。
2. 確認 **Status Code** 為 `200`，且 **Response Body** 為有效的 JSON。
3. 在 Response Headers 中應該包含：

   ```
   Access-Control-Allow-Origin: *
   x-vercel-protection-bypass: true（若使用 bypass header）
   ```

---

## 小結

| 方式                        | 適用情境       | 說明                   |
| ------------------------- | ---------- | -------------------- |
| 關閉 Preview Authentication | 開發階段       | 直接取消保護，方便測試          |
| 使用 Protection Bypass      | 正式開發、自動化流程 | 維持安全機制，同時允許 API 自動訪問 |

我最終選擇了第二種方案——**維持保護但使用「Protection Bypass for Automation」**，這樣既能保持部署安全，又能讓 extension 的 background 正常取得資料。

---

**延伸閱讀：**

* [Vercel: Methods to bypass Deployment Protection](https://vercel.com/docs/deployment-protection/methods-to-bypass-deployment-protection)
* [專案設定頁 – Deployment Protection](https://vercel.com/eva813s-projects/linxly-nextjs/settings/deployment-protection)


