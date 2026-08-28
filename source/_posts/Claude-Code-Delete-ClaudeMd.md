---
title: 刪掉你的 CLAUDE.md！別再堆指令了，定期重建才是正解
date: 2026-08-28
tags:
  - AI
  - Claude
  - CLAUDE.md
  - 開發工具
categories:
  - AI
---

# 刪掉你的 CLAUDE.md！別再堆指令了，定期重建才是正解

> 本文整理自工程師 Charlie Hills 的部落格文章《Delete your CLAUDE.md》。文章源頭是 Anthropic Claude Code 負責人 Boris Cherny 曾透露：Anthropic 把系統提示砍掉超過 80%，模型反而表現更好（詳見前篇〈[狠砍80%系統指令！Claude Code之父揭AI心法](/2026/08/07/Claude-Code-Prompt-Reduction/)〉）。Charlie Hills 把這個反直覺的建議，變成一套自己也真的跑過一遍的具體操作流程。

## 核心論點：CLAUDE.md 不是越寫越好

大多數人維護 CLAUDE.md 的方式是「加法」——踩到一個坑，就補一條規則；AI 犯了一次錯，就多寫一段叮嚀。半年下來，檔案只會愈疊愈厚，卻很少有人回頭刪東西。

Charlie Hills 的主張很直接：**這份文件應該定期整個調整重建，而不是無限累加**。主要是三個主要的代價。

## 文件臃腫的三重代價

1. **成本** — CLAUDE.md 是全局系統提示，每一行都會在**每一次**對話中被讀入、被計費，不管這次任務用不用得到。
2. **衝突** — 規則堆多了難免互相矛盾，模型只能自己「隨便選一條」來遵守，行為變得不可預期。
3. **注意力衰減** — 對應「[Lost in the Middle](https://arxiv.org/abs/2307.03172)」這類研究：長上下文中間段落的內容，模型的注意力本來就會下降。塞進 CLAUDE.md 中段的規則，很可能根本沒被認真「看到」。

## 作者的真實案例

Charlie Hills 攤開自己的 CLAUDE.md：**305 行**，遠超過 Anthropic 官方建議的 **200 行上限**（官方原話：「目標控制在 200 行以下，更長的文件會消耗更多上下文，降低遵守率」）。

裡面裝的是六個月份、東拼西湊的修正，還有 **195 個 skill**——但實際被呼叫過的只有 **65 個**，等於三分之二是根本沒人用的死代碼。

這不是特例，是每個長期用 Claude Code 的人都會自然走到的狀態。

<!-- more -->

## 先搞懂五層架構，再談要刪什麼

在動手清理前，得先分清楚哪些東西該放哪一層，不然刪錯地方只會更亂：

| 層級 | 作用 | 載入時機 |
|---|---|---|
| **CLAUDE.md** | 全局系統提示 | 每次對話開始就讀取 |
| **記憶文件** | 持久化的學習記錄 | 依相關性載入 |
| **Skills** | 已保存的工作流程 | 按需載入（description 先進 context，內文用到才讀） |
| **Hooks** | 自動觸發機制 | 事件觸發時執行 |
| **專案文件** | 特定專案的細節指令 | 按需載入 |

核心原則就一句話：**只有真的「每次都要用」的東西，才配得上放進 CLAUDE.md**。其他的都該下放到 skill、hook 或專案文件，讓它們「按需出現」而不是「常駐佔位」。

## 還沒有 CLAUDE.md？先用這個 prompt 生一份

如果你根本還沒開始寫 CLAUDE.md，Charlie Hills 的建議是別自己空手生字，直接請 Claude 用 Boris Cherny 的版本當起手範本，反過來訪談你：

```
Help me build my CLAUDE.md from scratch. Use Boris Cherny's CLAUDE.md as a starting template. Ask me about my business, voice, banned words, output defaults, and how I want you to work. Save the final file to ~/CLAUDE.md.
```

## 三個可以直接複製的檢視 Prompt

已經有 CLAUDE.md 的話，就進入下一步：把內容重新檢視一遍。以下三段是原文「Paste this」要讀者逐字貼上的內容，這裡刻意保留英文原文——這幾段是作者實際測試過、要 Claude 照步驟執行的指令，翻成中文容易在轉譯過程中弄丟語氣裡的強制性（例如 FLAG、NEVER 這類全大寫的強調）。

### 一、CLAUDE.md 逐行檢視

這段的核心是「先查證、再判斷」：拿 Anthropic 當下最新的官方指引當標準，一條一條比對，而不是憑印象覺得哪句該留哪句該刪。

```
My Claude instructions were written for older models. Audit them against Anthropic's current guidance and tell me what to delete.

1. FETCH THE LIVE RULES FIRST. Read Anthropic's prompting best practices page and the page for the exact model I am on. Use what is published today, never what you remember.

2. GO LINE BY LINE. One verdict per instruction: DELETE, KEEP or REWRITE, with the reason in plain English.

3. QUOTE YOUR SOURCE. Every DELETE quotes the sentence from Anthropic's guidance that justifies it. Cannot find one? The verdict is KEEP. Never invent a source.

4. FLAG EVERY VERIFY-TWICE RULE. "Always verify your work", "double-check before answering". The model already self-corrects. These make it do the work twice, and I pay for both.

5. FLAG "ONLY FLAG THE BIG ISSUES". Taken literally, so I get told less than I wanted. Replace with: report everything, I do the filtering.

6. FLAG ANY "DON'T OVERTHINK" RULE. Rules against thinking make internal tags leak into the answer. Cut them.

7. FLAG ROLE PADDING. "You are an expert with 20 years of experience" was once good advice. Now optional. Keep a role only where it genuinely changes the output.

8. FLAG STALE EXAMPLES. Examples written for an old model teach old habits. One current example beats ten stale ones.

9. NEVER TOUCH A TRUTH RULE. "Only claim what you verified" is not a severity filter, it stops made-up facts. Rules like that stay, whatever else goes.

10. TELL ME WHAT IS MISSING. The new models need five instructions the old ones did not: keep answers short, cap document length, say how to update me while you work, hold the task scope, limit the helpers you spawn.

THEN GIVE ME:
- A table, one row per instruction: my line, the verdict, the why, and Anthropic's line.
- The exact wording to paste in for each of the five missing instructions.
- The honest count. "307 lines in. 4 deletes, 4 rewrites, 5 additions."
- What you could not check, marked NOT RUN. Never a clean bill you did not earn.

AND BE HONEST:
- If most of my file should go, say so plainly. The scaffolding I am proudest of is the likeliest casualty.

My instructions are below. Go.
```

### 二、Skill 去重

這段的重點不是比對名稱，而是看兩個 skill 做的是不是同一件「工作」——名字取得再不一樣，邏輯重複就算數。

```
Audit my skills folder for overlap and dead weight. Do not delete anything.

1. List every skill with its name and one line on what it does.
2. Group them by the JOB they do, not by their name. Skills doing the same job in different words are the same skill.
3. For each group of two or more, tell me which one to keep and why, and exactly what the others have that it is missing.
4. Flag every skill I have never actually used.
5. Flag every skill that only works because of a file or folder that no longer exists.

Then give me ONE merged version of the biggest group, written out in full, ready to save. Tell me what I lose by merging.
```

### 三、Hooks 清查（把重複請求變成自動化）

這段不是憑空猜你可能需要哪些自動化，而是回頭翻對話紀錄，抓出你真的一講再講、或是它一錯再錯的地方，再從中挑出來當 hook。

```
Help me work out what I should turn into a hook.

Look back over our recent conversations and find:

1. Things I ask for over and over, in roughly the same words.
2. Times I had to tell you something you should already have known.
3. Times you did something I had to correct more than once.

For each one, tell me:
- the word or phrase I actually say when I want it
- what you should go and read, or do, the moment I say it
- whether that word is unique enough that it won't fire by accident

Give me your top three, ranked, with the best one first. Do not build anything yet.
```

這四段 prompt 的共通點是：**都要求 Claude 給理由、給清單，而不是直接動手改檔案**（"Do not delete anything"、"Do not build anything yet" 都明講了這點）。檢查和動手分開，才不會一次盤點就把還在用的規則也一起砍掉。

## 建議的執行順序

0. **如果還沒有 CLAUDE.md**，先跑「從零開始」那段 prompt，用 Boris Cherny 的版本當範本生一份，再進入下面的檢視循環。
1. **先切到最新模型版本**（`/model`），舊模型可能還需要那些「補足能力」的規則，換了新模型再重新檢查才準。
2. **備份現有的 CLAUDE.md、skills、hooks**，這一步不能省。
3. **照第一段 prompt 檢查 CLAUDE.md**，拿到變更清單後再逐條批准套用。
4. **跑 skill 去重**，合併掉那些名字不同、做的事一樣的 skill。
5. **跑 hooks 清查**，把真正高頻、機械式的重複請求收斂成自動化。
6. **設一個週期提醒**（例如每六個月），重複整套流程，這呼應 Boris Cherny 原本的建議：定期清空，觀察模型在沒有這些規則時會怎麼做，只把它真的還會踩的坑補回去。

## 實務上的一點提醒

這套流程好用,但有兩個地方值得留意:

- **重新檢視不代表清空**。Charlie Hills 的標題聳動,實際做法是「重新評估後保留該保留的」,不是無腦全刪。真正的重點是**別讓文件只增不減**,而不是追求一份空白的 CLAUDE.md。
- **這整套流程要花實際的人力去看清單、做決定**。這不是一個丟給 Claude 就能全自動跑完的流程——最後那份「可批准的變更清單」,還是得靠你自己的經驗判斷哪些真的該留。這也呼應了 Boris Cherny 那句話:真正不會過時的資產,是「看得出哪裡該優化」的判斷力,不是規則本身。

## 總結

CLAUDE.md、skills、hooks 都會隨著模型進化而「折舊」——今天為了補足模型缺陷寫下的規則，很可能半年後就變成多餘的雜訊。與其把它們當一次性寫完就不管的資產，不如當成需要定期體檢、汰舊換新的東西。

**精即是強，不是多即是好。**

這套邏輯也不是 Claude Code 專屬的。如果你用的是 ChatGPT／OpenAI Codex CLI、GitHub Copilot、Cursor 這類工具，對應的檔案是 **`AGENTS.md`**（複數，不是 `AGENT.md`）——這是由 OpenAI 發起、現由 Linux Foundation 治理的跨工具標準，目前已有超過 30 個 agent 工具支援讀取，包含 Codex、Copilot、Cursor、Gemini CLI、Aider、Windsurf 等。就連 Claude Code 自己現在也讀得懂 `AGENTS.md`，常見做法是在 `CLAUDE.md` 第一行寫 `@AGENTS.md` 把兩者串起來：共用規則放進 `AGENTS.md`，工具專屬的細節才留在各自的原生檔案裡。這篇文章的審計邏輯放到 `AGENTS.md` 上一樣成立，甚至更該做，因為它同時提供給更多工具讀，內容臃腫的代價會被工具數量放大，不是只影響單一個 agent。

## 參考資料

- [Charlie Hills：Delete your CLAUDE.md](https://charliehills.substack.com/p/delete-your-claudemd)
- [前篇：狠砍80%系統指令！Claude Code之父揭AI心法](/2026/08/07/Claude-Code-Prompt-Reduction/)
