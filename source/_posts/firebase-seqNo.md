---
title: 如何用 SeqNo 管理機制提升列表插入效能，解決排序衝突問題
date: 2025-09-07 19:02:27
tags: ["firebase", "side-project"]
categories: side-project
---

列表的動態排序和插入為常見的需求。然而，當面對大量資料時，傳統的「刪除重建」策略容易會成為效能瓶頸。本文將分享我在專案中如何透過 SeqNo 管理機制，將列表插入效能提升，同時保持資料一致性和系統穩定性。

在專案內有一下拉列表的功能，當使用者選擇某個選項後，會將該選項插入到列表中，並且會依據使用者的操作順序來進行排序。最初的實作方式是每次插入新選項時，都會重新計算整個列表的排序，這在資料量較大時，導致效能明顯下降。為了解決這個問題，引入了 SeqNo 管理機制。


## 原有問題

當使用者要在列表中間插入新的資料時，使系統面臨挑戰，並且在沒有加入 seqNo 管理機制前，容易遇到排序衝突的問題。

```js
  // 原有狀態
  [
    { id: 'A', seqNo: 1 },
    { id: 'B', seqNo: 2 },  // 要在 B 後面插入新項目
    { id: 'C', seqNo: 3 },
    { id: 'D', seqNo: 4 }
  ]
```
如果直接將新項目設為 seqNo: 3，會與現有的項目 C 發生衝突。

### 傳統解決方案的困境

直觀的解決方案是重新排序所有項目：
  
```js
// 刪除重建
  async function insertListData(afterId: string, 
  newListData: ListData) {
    // 1. 刪除所有後續項目 (N-M 次刪除)
    await deleteListDataAfter(afterId);

    // 2. 重新建立所有項目 (N-M+1 次建立)
    await recreateListDataWithNewSeqNo(newListData,
  subsequentListData);
  }
```
  - 操作複雜度: O(2N+1) - 其中 N 為列表總長度
  - 資料庫壓力: 大量刪除和建立操作
  - 交易風險: 操作步驟過多，失敗機率高
  - 併發問題: 長時間鎖定，容易產生競爭條件

## SeqNo 管理機制 
 只更新真正需要變動的項目，將複雜度進行改善，是插入點之後的項目數量。

1. 排序基礎函式
確保所有項目能依照 seqNo 正確排序。

```javaScript
// 排序工具
export function sortListDataBySeqNo(listData: ListData[]): ListData[] {
  return [...listData].sort((a, b) => {
    const aSeqNo = a.seqNo || 0;
    const bSeqNo = b.seqNo || 0;
    return aSeqNo - bSeqNo;
  });
}
```


2. 插入策略計算

只計算「插入點之後」需要調整的項目，並產生更新操作清單。

```javaScript
 export function calculateInsertStrategy(
  existingListData: ListData[],
  afterListDataId: string
): {
  insertSeqNo: number;
  affectedListData: ListData[];
  updateOperations: Array<{ listDataId: string; newSeqNo: number }>;
} {
  const sortedListData = sortListDataBySeqNo(existingListData);
  const afterIndex = sortedListData.findIndex(data => data.id === afterListDataId);

  if (afterIndex === -1) {
    throw new Error('afterListDataId not found');
  }

  // 關鍵：插入點的 seqNo + 1
  const insertSeqNo = sortedListData[afterIndex].seqNo! + 1;
  // 只影響插入點之後的項目
  const affectedListData = sortedListData.slice(afterIndex + 1);

  // 產生更新操作清單
  const updateOperations = affectedListData.map(data => ({
    listDataId: data.id,
    newSeqNo: data.seqNo! + 1
  }));

  return { insertSeqNo, affectedListData, updateOperations };
}

```

3. 交易執行
確保所有更新操作能在同一個 Transaction 中完成。

```javaScript
  export async function executeSeqNoUpdates(
    transaction: Transaction,
    operations: Array<{ listDataId: string; newSeqNo: number }>
  ): Promise<void> {
    for (const operation of operations) {
      // 更新每個受影響的項目
      const listDataRef = adminDb.collection('listData').doc(operation.listDataId);
      transaction.update(listDataRef, {
        seqNo: operation.newSeqNo,
        updatedAt: new Date()
      });
    }
  }

```

API 實現範例

```javaScript
  // POST /api/v1/listData
 export async function POST(req: Request) {
  // ... 驗證邏輯

  if (afterListDataId) {
    try {
      // 步驟1: 計算最小更新策略
      const { updateOperations, insertSeqNo } =
        calculateInsertStrategy(existingListData, afterListDataId);

      // 步驟2: Transaction 執行
      const result = await adminDb.runTransaction(async (transaction) => {
        await executeSeqNoUpdates(transaction, updateOperations);

        // 插入新項目
        const listDataRef = adminDb.collection('listData').doc();
        transaction.set(listDataRef, {
          ...newListData,
          seqNo: insertSeqNo
        });

        return { id: listDataRef.id, seqNo: insertSeqNo };
      });

      return NextResponse.json(result, { status: 201 });
    } catch (error) {
      if (error.message === 'afterListDataId not found') {
        return NextResponse.json({ message: 'afterListDataId not found' }, { status: 404 });
      }
      throw error;
    }
  }
}

```

## 此專案的應用回顧與心得

這個問題最初是在「插入列表中間」時被發現的。
當時經常出現排序錯亂，追查後才發現是因為缺乏 SeqNo 管理機制，導致插入時 seqNo 發生衝突。

一開始的解法是 刪除重建，但這帶來兩個明顯的問題：
1.當資料量大時，對後端資料庫造成極大負擔。
2.使用者操作無法預測，若頻繁在列表中間插入或刪除，效能會快速下降。

引入 SeqNo 管理機制 後：
  - 插入效能明顯提升
  - 保持了資料一致性
  - 系統穩定性也大幅改善
這樣的設計不僅解決了排序衝突，也大幅減少了資料庫的操作次數，讓整體效能更加穩定可