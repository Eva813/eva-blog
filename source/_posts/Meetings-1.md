---
title: 刷題練習 - Determine If a Person Could Attend All Meetings
date: 2025-10-04 19:16:52
tags: ["LeetCode"]
categories: LeetCode
---

Given an array of meeting time `intervals` where `intervals[i] = [startᵢ, endᵢ]`, determine if a person could attend all meetings.

給定一個會議時間 `intervals` 陣列，其中 `intervals[i] = [startᵢ, endᵢ]`，判斷一個人是否能夠參加所有會議。

### Example 1:

**Input:**

`intervals = [[0,30],[5,10],[15,20]]`

**Output:**

`false`


### Example 2:

**Input:**

`intervals = [[7,10],[2,4]]`

**Output:**

`true`

### Constraints:

- `0 <= intervals.length <= 10⁴`
- `intervals[i].length == 2`
- `0 <= startᵢ < endᵢ <= 10⁶`


### 解題思路
要判斷一個人是否能夠參加所有會議，關鍵在於檢查會議時間是否有重疊。若有任何兩個會議的時間區間重疊，則無法參加所有會議。

#### 重疊情況分析
以 `[1,5]` 和 `[2,4]` 為範例：

1. **假設會議時間按開始時間排序**：將所有會議按照開始時間由早到晚排列
2. **檢查相鄰會議**：排序後，只需檢查相鄰的會議是否重疊
3. **重疊條件**：前一個會議的結束時間 > 下一個會議的開始時間

#### 步驟
1. 將所有會議按開始時間排序
2. 遍歷排序後的會議陣列
3. 檢查每個會議的結束時間是否晚於下一個會議的開始時間
4. 若發現重疊，回傳 `false`；否則回傳 `true`

這樣的方法時間複雜度為 O(n log n)（主要是排序的時間），空間複雜度為 O(1)。

<!-- more -->

#### 範例程式碼

```javascript
var canAttendMeetings = function (intervals) {
  // 按開始時間排序
  intervals.sort((a, b) => a[0] - b[0]);
  const lastMeetingEnd = intervals[0][1];
  for (let i = 1; i < intervals.length; i++) {
    // 檢查當前會議的開始時間是否早於上個會議的結束時間
    if(intervals[i][0] < lastMeetingEnd) {
      return false; // 有重疊，無法參加所有會議
    }
    lastMeetingEnd = intervals[i][1];
  }

  return true;
}
```

## 前端應用場景
這類問題在前端應用中極為常見，特別是涉及時間管理的系統：

### 1. 日曆與排程系統
- 會議排程：檢查新會議是否與現有會議時間衝突
- 預訂系統：確保時間段不重疊，避免雙重預訂
- 資源管理：防止同一資源在同一時間被多次分配

### 2. 表單驗證 (Form Validation)
- 營業時間設定：驗證多個營業時段是否重疊
- 配送時間管理：確保配送時間區間邏輯正確
- 客戶端驗證：在提交前即時檢測衝突，提升用戶體驗

---

## React 互動範例

以下範例模擬**會議室預訂系統**，展示如何使用本題演算法檢查新會議是否與已預訂時段衝突：

```tsx
import React, { useState, useMemo } from "react";

type Interval = { start: number; end: number };

export default function MeetingChecker() {
  // 已預訂的會議（實際應用中來自 API）
  const existingMeetings: Interval[] = [
    { start: 9, end: 11 },
    { start: 14, end: 16 },
  ];

  // 用戶想新增的會議
  const [newMeeting, setNewMeeting] = useState({ start: 10, end: 12 });

  // 使用本題演算法：排序後檢查相鄰區間
  const hasConflict = useMemo(() => {
    const all = [...existingMeetings, newMeeting];
    const sorted = all.sort((a, b) => a.start - b.start);

    for (let i = 1; i < sorted.length; i++) {
      if (sorted[i].start < sorted[i - 1].end) return true;
    }
    return false;
  }, [newMeeting]);

  return (
    <div className="p-6 max-w-md mx-auto">
      <h2 className="text-lg font-bold mb-3">會議室預訂系統</h2>

      {/* 已預訂會議 */}
      <div className="mb-4 text-sm text-gray-600">
        <div>已預訂：09:00-11:00</div>
        <div>已預訂：14:00-16:00</div>
      </div>

      {/* 新增會議 */}
      <div className="flex gap-2 items-center mb-3">
        <span>新增會議：</span>
        <input
          type="number"
          value={newMeeting.start}
          onChange={(e) => setNewMeeting({ ...newMeeting, start: +e.target.value })}
          className="border p-1 rounded w-16"
        />
        <span>~</span>
        <input
          type="number"
          value={newMeeting.end}
          onChange={(e) => setNewMeeting({ ...newMeeting, end: +e.target.value })}
          className="border p-1 rounded w-16"
        />
      </div>

      {/* 結果顯示 */}
      <div className={`p-2 rounded text-sm ${
        hasConflict ? "bg-red-100 text-red-700" : "bg-green-100 text-green-700"
      }`}>
        {hasConflict ? "❌ 時間衝突，無法預訂" : "✅ 可以預訂"}
      </div>
    </div>
  );
}
```

**使用情境**：模擬會議室預訂系統，當用戶嘗試新增會議時，即時檢查是否與已預訂時段衝突。試著調整時間（例如改為 `12-13`）觀察結果變化。
