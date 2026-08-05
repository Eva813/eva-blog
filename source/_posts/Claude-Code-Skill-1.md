---
title: Claude Code Skill 撰寫指南：Anthropic 工程師親授實戰心得
date: 2026-08-05
tags:
  - AI
  - Claude
  - Skill
  - 開發工具
categories:
  - AI
---

# Claude Code Skill 撰寫指南：Anthropic 工程師親授實戰心得

> 本文整理自 Anthropic 技術成員 Thariq Shihipar 的實戰分享，探討如何有效撰寫 Claude Code Skills。
> 截至 2026 年 6 月，Anthropic 內部已有「數百個」skill 在活躍使用。

## 核心概念

### Skill 是什麼？

**Skill 可以理解成一種「脈絡工程」（context engineering）**：在對的時機，把對的資訊提供給 AI。

## 一、為什麼資料夾比單一檔案好用？

### 問題點
- 如果把所有東西塞進一個檔案，等於每次都把整本說明書攤在 Claude 面前

### 解決方案
- 改用**資料夾結構**，搭配**「漸進揭露」（progressive disclosure）**
- 需要時才讀對應檔案，Claude 才不會被無關內容干擾

### 官方建議
把詳細 API 文件、範例、腳本等 supporting files 放在 skill 資料夾裡，並由 `SKILL.md` 說明何時讀取。

---

## 二、9 大 Skill 分類

Anthropic 盤點內部 skill 後，發現它們大致聚成 9 種類型。**最好的 skill 通常只做一類**；想做太多事的會橫跨多類，反而把 agent 搞混。

### 1. 函式庫與 API 參考
- 教 Claude 正確使用某個函式庫、CLI 或 SDK
- 附上常踩的雷

### 2. 產品驗證
- 描述怎麼測試、驗證程式碼有沒有真的動
- Anthropic 說這類對產出品質「**可量測的影響最大**」
- 值得花一週做到極好

### 3. 資料抓取與分析
- 接上你的資料與監控系統
- 內含抓資料的腳本、儀表板代號

### 4. 業務流程與團隊自動化
- 把重複工作流收成一個指令
- 例如：每日 standup、開票

### 5. 程式碼鷹架與範本
- 自動生出新服務、新模組的樣板

### 6. 程式碼品質與審查
- 強制團隊的程式風格
- 協助 code review

### 7. CI/CD 與部署
- 顧 PR、跑測試、漸進部署
- 出問題自動回滾

### 8. Runbooks（故障排除手冊）
- 接到一個症狀，走完多工具調查
- 產出結構化報告

### 9. 基礎設施操作
- 執行例行維運
- 對破壞性動作加上護欄

---

## 三、最高價值的「Gotchas（陷阱）」區塊

### 核心觀念
> **任何 skill 裡訊號最高的內容，就是 Gotchas 區塊。**

### 什麼是 Gotchas？
- Claude 使用這個 skill 時**最常踩的失敗點**
- 要隨時間累積、持續補充
- 往往是「**文件不會寫、但你踩過坑才知道**」的細節

### 實例（以請假系統為例）

#### 範例一：版本問題
「同一張假單可能改過好幾次，要抓『最後核准』那一版，不是最早送出的那張。」

#### 範例二：術語對應
「業務部口中的『出差』，在人資系統裡叫『公假』，其實是同一件事，只是部門叫法不同。」

#### 範例三：狀態判斷
「系統顯示『已送出』不代表假請成功了，還要看主管那關有沒有按下核准。」

### 重點
- 把「**平常沒人會特別告訴你、有出錯過而學到**」的細節寫下來
- skill 才會越用越準

---

## 四、描述（description）要寫給模型看

### 關鍵認知
**description 欄位不是給人看的摘要，而是「什麼情況下該觸發我」的說明。**

### 運作機制
- Claude Code 啟動 session 時，會建立一份可用 skill 與 description 的清單
- 用它判斷「使用者這個請求，有沒有對應的 skill 可以用」

### 實務技巧
**把觸發詞直接寫進描述，命中率會更高**

例如：
```yaml
description: 彙整這週完成的工作，產生給主管看的週報。當使用者說「跑週報」「整理這週進度」時觸發。
```

---

## 五、給 Claude 腳本與檔案，讓它「組合」而不是「重造」

### 核心概念
把可重複的動作寫成腳本交給 Claude，它就能把每一回合花在「決定下一步做什麼」，而不是重打一次樣板。

### 進階技巧：隨選 hooks（on-demand hooks）
只在這個 skill 被呼叫時才生效

#### 範例 1：`/careful`
- 動正式環境時才開
- 自動擋掉 `rm -rf`、`DROP TABLE`、force-push 等危險指令

#### 範例 2：`/freeze`
- debug 時只准改特定資料夾
- 避免手滑「修好」無關的程式碼

---

## 六、SKILL.md 完整範本

```markdown
---
name: weekly-report
description: 彙整這週完成的工作，產生給主管看的週報。當使用者說「跑週報」「整理這週進度」時觸發。
---

# 週報產生器

## 怎麼做

1. 讀 `config.json` 取得週報要寄給誰、用什麼格式；若還沒設定，先問使用者。
2. 彙整這週完成的事項，只列「跟上週相比的新進度」，不要把舊的重講一遍。
3. 把這次的週報存進 `reports.log`（只新增、不覆蓋），下次執行時讀自己的歷史，自動判斷哪些是新的。

## Gotchas（踩過的坑）

- 「標記完成」不等於真的結案，有些項目會被退回重做，狀態要看最新的那一版。
- 同一件事這週可能改過好幾次，只取最後定案的版本，別把過程中的草稿都列進去。

## 參考

- 欄位與格式範本見 `references/format.md`（需要時再讀，不必一開始全載入）。
```

---

## 七、動手前要知道的兩個前提

### 前提一：Skill 不是免費的

- 一般 session 中，skill 的 **description 會進入 context**
- 完整 `SKILL.md` 則是在使用者或 Claude 呼叫後才載入
- 每多一個放進專案的 skill，都會增加一點模型需要掃描的脈絡

#### 團隊規模建議
改用 **plugin marketplace**，讓成員自己挑要裝哪些，而不是全部塞進每個專案。

### 前提二：別想一次寫到完美

> **Anthropic 最好用的 skill 多半是從「寥寥幾行字加一個 gotcha」起步**

#### 迭代思維
- 先做一個小的
- 撞到坑就補一條 gotcha
- 慢慢變強

#### 核心價值
**Skill 的價值不在你寫了多少，而在你有沒有告訴 Claude「它原本不知道、或老是做錯的那件事」。**

---

## 八、總結與行動建議

### 撰寫 Skill 的黃金法則

1. **用資料夾結構，不要單一檔案**
2. **一個 Skill 只做一類事**
3. **Gotchas 區塊是最高價值內容**
4. **description 要寫觸發詞，給模型看**
5. **提供腳本讓 Claude 組合，不是重造**
6. **從小開始，持續迭代**

### 優先順序

**產品驗證類 Skill** 對品質影響最大，值得優先投入。

---

## 參考資料

- [Anthropic 官方部落格：Lessons from building Claude Code: how we use skills](https://claude.com/blog/lessons-from-building-claude-code-how-we-use-skills)
- [數位時代](https://fc.bnext.com.tw/articles/view/4687?utm_source=bn_daily&utm_medium=email&utm_campaign=06-16-2026&bx_heid=8475539909)
