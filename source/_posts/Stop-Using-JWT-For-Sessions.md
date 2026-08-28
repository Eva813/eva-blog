---
title: JWT 做登入這件事：一篇 Gist 引發的整理
date: 2026-08-15
tags: ["JWT", "Session", "Web Security", "後端"]
categories: Web Security
---

在前端 / 後端開發圈，「用 JWT 做登入驗證」幾乎已經是預設選項，很多教學、範例專案打開就是 `jsonwebtoken` 直接簽一包塞進 cookie 或 localStorage。但 [Stop using JWTs!](https://gist.github.com/samsch/0d1f3d3b4745d778f78b230cf6061452) 這篇 Gist 提出了一個很直接的反對意見：

> JWTs should not be used for keeping your user logged in. They are not designed for this purpose, they are not secure, and there is a much better tool which is designed for it: regular cookie sessions.

這篇文章記錄我讀完之後的整理，包含作者的核心論點、對常見反駁的回應，以及我自己在實務上觀察到的紀錄分享。

<!-- more -->

## 先講結論：JWT 不等於「安全的登入機制」

很多人把 JWT 跟「安全」畫上等號，但這其實是規格用途上的誤會。作者的主張很清楚：**JWT 不是為「維持登入狀態」這種長生命週期的場景設計的**，硬拿來用，反而會犧牲安全性跟彈性。

## 作者的三個核心論點

### 1. JWT 適合的場景是短生命週期

作者主張 JWT 適合的使用情境是極短的存活時間，實務上通常建議 5 分鐘或更短——這是把它當「一次性授權憑證」的用法。而一般使用者的登入 session 動輒數小時、數天甚至數週，已經超出這個原本適用的範圍。

（RFC 7519 本身並沒有規定 token 的存活時間，「5 分鐘」是作者跟主流資安實務的建議，不是規格明文規定。）

### 2.「無狀態」認證在安全實踐上不成立

這是整篇文章關鍵的一點。JWT 常被包裝成「無狀態（stateless）」的賣點——伺服器不用查資料庫，直接驗簽章就能信任 token。但作者引用 [joepie91 的文章](http://cryto.net/~joepie91/blog/2016/06/13/stop-using-jwt-for-sessions/) 指出：

- 真正安全的認證機制，仍然需要某種方式維持狀態（例如：token 被撤銷、使用者被登出、密碼被重設後舊 token 要失效）
- 既然最終還是得存一份狀態資料，那不如直接存完整的 session 資料，而不是先簽一個 JWT，之後還要另外處理撤銷、黑名單這些「無狀態」處理不了的問題

換句話說，「無狀態」在話術上很吸引人，但落地到真實系統時，往往只是把狀態管理的複雜度換了個地方藏起來，而不是真的消失。

規格本身在資安圈的評價也不高——[Paragon Initiative 的文章](https://paragonie.com/blog/2017/03/jwt-json-web-tokens-is-bad-standard-that-everyone-should-avoid) 指出，JWT 規格甚至允許偽造 token（例如惡名昭彰的 `alg: none` 問題），這種先天缺陷讓人擔心還有其他沒被發現的坑。

### 3. 效率跟靈活性都不如傳統 session

如果 JWT 裡面存的東西本質上就是一組 session 資訊，那用 JWT 存反而比傳統 session cookie 更沒效率、更不靈活——因為每次都要把整包資料塞進 token 裡傳來傳去，而不是伺服器端存一份、client 只帶一個輕量的 session id。

## 常見反駁，作者怎麼回應

以下這些反駁幾乎就是我自己心裡會出現的疑問。

### 「Google 不是也在用 JWT 嗎？」

作者的回應是：Google 在瀏覽器裡維持使用者登入狀態，用的其實是**傳統的簽章加密 cookie session**（例如 `SID`、`HSID` 這類 cookie，記錄帳號 ID 與最近登入時間），JWT 只出現在單一登入（SSO）情境——把「某伺服器上已登入」這件事轉換成「另一個伺服器也承認你已登入」的傳輸媒介。這正好符合 JWT 原本設計的短生命週期、單次驗證用途，而不是拿來當作日常登入狀態的儲存機制。

### 「無狀態架構比較潮，效能比較好吧？」

作者的回應很直接：完全無狀態的認證在安全上做不到。可以參考另一篇他寫的 [Stateless is a lie](https://gist.github.com/samsch/259517828ab4557c5c8b72ca1252992d)。追求「無狀態」這個目標本身，可能就已經走偏了方向。

### 「我不知道怎麼設定 session，JWT 比較簡單」

這其實是最常見的理由。作者的回應是：session 不是新技術，只是這幾年因為 JWT 太紅，相關教學變少、顯得比較「過時」而已。絕大多數後端框架（Rails、Django、Laravel 等）都內建或很容易整合 session 機制；Node.js 生態因為高度模組化，需要自己組裝，可以搭配 [`express-session`](https://github.com/expressjs/session) 加上 store（例如 `connect-session-knex` 接 PostgreSQL / MySQL）。

流程上跟 JWT 最大的差異在於：伺服器端保有一份「這個 session id 目前是誰、有沒有效」的紀錄，撤銷、登出、強制過期都只是改一筆資料，不需要額外設計黑名單機制。

### 那真的需要短期 token 怎麼辦？

作者建議可以考慮 [PASETO](https://paseto.io/)——一個從設計之初就把安全性放在第一位的規格，用來取代 JWT 在一次性授權、跨服務短暫驗證這類場景的角色。但作者也強調：**PASETO 一樣不該拿來做 session 管理**。這個界線也呼應 [OWASP Vancouver 的簡報](https://owasp.org/www-chapter-vancouver/assets/presentations/2020-01_Attacking_and_Securing_JWT.pdf) 提到的 JWT 合理用途：授權（authorization）而非會話管理、存活時間短、預期只使用一次（驗證完就去換取真正的 session）。

## 換個角度看：JWT 和 Session 未必是二選一

整理完 Gist 的論點之後，我又讀到另一篇角度更中立的文章 [《JWT 跟 Session Cookie 登入認證的差異》](https://www.chia1104.dev/posts/jwt-vs-session-cookie-authentication-differences)（作者 chia1104），是從面試官視角出發，把兩種機制的實務做法拆得很細，剛好補上 Gist 那篇比較強硬立場沒講到的東西。

### JWT 的「refresh」跟 Session 的「refresh」其實是兩件事

這是原文講得最清楚、也是我自己之前最容易搞混的地方：

- **JWT 的 refresh**：短命的 `access_token` 搭配長命的 `refresh_token`。`access_token` 過期後，前端拿 `refresh_token` 去換一顆**全新的** `access_token`，整包內容換掉，兩者通常都存在 `localStorage`。
- **Session Cookie 的「refresh」**：正式名稱是 sliding expiration，伺服器在每次合法請求時，把該筆 Session 在 store（例如 Redis）裡的過期時間往後延，瀏覽器手上的 `sessionId` cookie **整個登入期間通常不會變**。

同樣叫「刷新」，一個是發新證件，一個是幫舊證件延簽，機制完全不是同一個層次的東西。

### 兩者的實務比較

原文附了一張比較表，我把重點摘錄如下（表格整理自原文，完整細節與案例可見[原文連結](https://www.chia1104.dev/posts/jwt-vs-session-cookie-authentication-differences)）：

| 面向 | JWT（Bearer token） | Session Cookie |
|---|---|---|
| 狀態存放 | 無狀態，登入資訊在 token 本身，前端保存 | 有狀態，資訊存在後端 Session store，前端只存 sessionId |
| 擴充性 | 適合多服務、跨網域、微服務架構 | 單機或搭配 Redis 共享即可，適合一般規模系統 |
| 撤銷登出 | 純無狀態下難以即時撤銷，需要黑名單機制 | 伺服器刪 Session 即可即時生效 |
| 安全風險 | 為了塞進 `Authorization` header，通常放 localStorage，JS 讀得到，XSS 得手就能直接冒用 | Cookie 只是隨機 ID，可搭配 HttpOnly／Secure／SameSite，JS 完全讀不到 |
| 適用情境 | SPA、行動 App、對外 API、微服務 | 傳統 Web、後台系統、單體架構 |

安全風險那一列是重點：JWT 存 localStorage 之所以危險，正是因為它「必須」被 JS 讀到才能塞進 header；而 session cookie 之所以能相對安全，是因為它可以走 HttpOnly，瀏覽器會自動帶、JS 完全碰不到。

### 跟 Gist 文章的差異

Gist 作者是資安背景，結論偏向「乾脆別在 session 場景用 JWT」；chia1104 這篇則更貼近多數團隊的實際狀況——他認為兩者很少是純二選一，常見做法反而是**混搭**：Web 前台用 Session Cookie 管登入狀態，服務與服務之間、或對外開放的 API，再用 JWT 做授權資訊傳遞。專案規模小、單一後端時，Session Cookie 通常更好維護；一旦拆成微服務或要對外開 API，JWT「自我描述、可跨服務驗證」的特性才真正派上用場。

這點我覺得比 Gist 的立場更貼近現實：兩篇合在一起讀反而更完整——一篇提醒你 JWT 的代價常被低估，一篇提醒你選型該看架構規模。

## 一個實際的案例：寫好的 refresh 機制，不代表真的有在跑

這是我自己在實務上觀察到、覺得值得記錄下來的一個狀況：

一個 Vue 3 + Pinia 的前端專案，登入機制是典型的 JWT 模式。拆開來看每個角色的分工，會比條列規格更容易懂：

- **後端**：登入成功時，決定「這 token 到哪個時間點為止有效」，把這個 Unix 時間戳當一個獨立欄位（`expiration`）連同 `token / type` 一起回傳；之後每次請求，只驗證 token 的簽章沒被竄改、再拿 `expiration` 跟伺服器自己當下的時間比對，完全不查任何「這個人現在是否登入中」的紀錄——這就是「無狀態」的具體樣子
- **localStorage**：單純的持久化儲存，本身不會主動變化，只有在登入成功、refresh 成功、登出這三個時機，才會被程式碼整包覆寫或清除
- **Pinia store**：把 localStorage 的內容鏡射一份到記憶體，讓畫面能用 `isLogin` 這種 computed 直接判斷登入狀態；這份鏡像**不會自動**跟 localStorage 同步，而是同一個 `setToken()` 動作裡手動把兩邊各寫一次
- **axios request interceptor**：每次送出請求前，都重新從 localStorage 讀一次最新 token 塞進 `Authorization` header——不是快取住舊值，所以 localStorage 一被更新，下一個請求就自動吃到新 token
- **過期判斷**：前端自己拿 `expiration` 這個時間戳跟現在時間比對。要注意這不是「前端 token 跟後端比」，而是前後端**各自拿同一份 `expiration`，各自跟自己手上的時間比**——兩邊互不通訊，只是剛好比的是同一個數字。前端這一比只是「別浪費一次注定失敗的請求」的 UX 優化，不是安全機制；真正決定放不放行的，永遠是後端驗證簽章跟過期時間那一關

> 附帶一個技術細節：標準 JWT 會把過期時間放在 payload 的 `exp` claim 裡，後端解簽章時直接讀出來就好；這個專案額外再回一個 `expiration` 欄位，是為了讓前端完全不用解 JWT payload。這是一個刻意的封裝設計——後端保留隨時調整 JWT 內部結構的自由，前端也不會因為讀了 payload 而對它產生隱形依賴：`expiration` 對前端來說只是 API 回傳的一個欄位，不是「前端懂了 JWT 裡面裝什麼」。

### 這樣算不算「保持登入」？算，但只有一半

拆開角色之後，會發現「保持登入」其實可以分成兩種完全不同的能力，這個專案只做到第一種：

- **做到的（session persistence，工作階段持久化）**：使用者重新整理頁面、關掉分頁、隔天再打開網站，只要 token 還沒過期，App 啟動時的檢查邏輯就會直接沿用 localStorage 裡的舊 token，讓使用者維持登入狀態——這是靠「一顆效期夠長的 token ＋ 持久化儲存」撐出來的
- **沒做到的（silent renewal，無感續期）**：一旦 `expiration` 那個時間點真正到了，不管使用者當下多活躍、正在操作到一半，下一次打 API 就是直接被判定過期。**沒有任何機制會在背景默默換一新 token 延續下去**——這種「無感延續」才是一般人講「保持登入」時真正期待的效果，而它需要額外的 refresh 機制才做得到

這兩個詞值得刻意分開記：session persistence 靠儲存就能達成，silent renewal 才需要 refresh 機制——這個專案有前者、沒有後者。

這個落差看程式碼會更清楚：仔細看 response interceptor 處理 401 的地方，會發現一個很寫實的細節——**當初確實把「用 refresh_token 換新 access_token、排隊重試失敗請求」這整套邏輯寫出來過**，寫法跟前面 chia1104 文章那張流程圖幾乎一致，但這段程式碼**整段被註解掉**，實際運作的行為只是「401 就直接登出、導回登入頁」。理論上想過、也寫過，只是沒有真正跑在線上——通常不是設計錯了，而是 refresh 排隊邏輯的邊界情況（多個請求同時 401、refresh token 也過期）比想像中難測乾淨，時程壓力下先上「401 直接登出」這個穩定版本，剩下的就無限期擱置。

> 順帶一提一個容易誤導人的小細節：登入畫面上的「記住我」核取方塊，其實跟 token 有沒有存起來完全無關——不管有沒有勾選，`setToken()` 都無條件會把 token 寫進 localStorage。這個核取方塊真正影響的，只是下次打開登入頁時帳號欄位要不要幫你預填。如果只看 UI，很容易誤以為「保持登入」是靠這個核取方塊控制的，但實際上它完全不參與這件事。

這剛好印證了 Gist 那篇文章的核心論點：**JWT 的「無狀態」優勢，需要額外的狀態管理機制才能安全撤銷／刷新，而這套機制的實作與維護成本，很容易在真實專案的時程壓力下被犧牲掉**，最後留下的是一個「效期內免重新登入，但效期一到就得整個重來」的系統——理論上有 refresh 設計，實際上從未真正運作過。

說到底，這個系統的登入壽命就是 token 的效期，一秒不多不少：在時間內做任何事（reload、關分頁、隔天再打開）都能保持登入，壽命一到，不管使用者在做什麼，就是結束。

## 結論與觀察

這篇 Gist 的價值不在於「JWT 是不好東西」這種一刀切的結論，而在於提醒我們：**技術選型要對應到它原本被設計要解決的問題**。JWT 被設計來處理短生命週期、一次性、跨服務的信任傳遞問題；而「讓使用者維持登入」這件事，傳統的 cookie session 其實一直都做得比較好、也比較好維護。

讀完兩篇之後，補充兩個比較落地的觀察：

- **選 JWT 的理由如果是「session 設定太麻煩」，那多半是被 Node.js 生態的模組化嚇到，而不是 session 本身真的難用**。其他主流框架的 session 大多是開箱即用等級。
- 如果未來要做無密碼登入或更現代的驗證方式，[Passkeys / WebAuthn](https://webauthn.io/) 反而是更值得研究的方向，跟這篇文章談的「session vs JWT」是互補而非互斥的議題。

下次在專案裡看到「要不要用 JWT 做登入」這個選擇時，值得先停下來想一句：我需要的到底是「驗證身份的短期憑證」，還是「使用者的登入狀態」？這兩者用同一個工具解決，很可能就是問題的起點。