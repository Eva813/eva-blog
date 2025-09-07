---
title: 實現前端自動儲存系統：Next.js + Zustand 
date: 2025-06-29 12:44:01
tags: ["Next.js", "side-project"]
categories: side-project
description: "Implementing auto-save functionality with Zustand in Next.js 14 to enhance user experience and data security."
---

## 前言

在現代 Web 應用開發中，自動儲存功能已成為用戶體驗的重要組成部分。想像一下，用戶正在編輯重要文件時還需要手動點擊儲存按鈕，如果沒有自動儲存機制、或是遺忘點擊儲存，所有辛苦編輯的內容都會瞬間消失。這種糟糕的體驗促使我思考：如何設計一個既智能又穩定的自動儲存系統？

本文將提供我在專案內內實作如何使用 Next.js 14 + Zustand 架構實現一個完整的自動儲存系統。

## 項目背景與需求分析

### 應用場景概述

我們面對的是一個 prompt 管理平台，類似於代碼編輯器或筆記應用。用戶可以：
- 創建和編輯 prompt
- 設置快捷鍵進行快速訪問
- 組織提示詞到不同文件夾
- 進行富文本編輯

### 核心技術挑戰

在這樣的編輯環境中，面臨以下挑戰：

1. **頻繁的內容變更**：用戶持續輸入和修改內容
2. **多字段同步**：標題、內容、快捷鍵需要同時追蹤
3. **性能考量**：避免過度頻繁的 API 調用
4. **用戶反饋**：清晰的保存狀態指示

### 技術選型理由

我們選擇以下技術棧：

```
技術棧架構
├── Next.js 14 (App Router) - 前端框架
├── Zustand - 狀態管理
├── TypeScript - 類型安全
├── TipTap - 富文本編輯器
└── Firebase - 後端數據存儲
```

**為什麼選擇這個組合？**
- **Next.js 14**：提供現代化的 React 開發體驗和卓越的性能
- **Zustand**：相比 Redux 更輕量，但功能完整的狀態管理方案
- **TypeScript**：在複雜的狀態管理中提供類型安全保障
- **Firebase**：簡化後端開發，讓我們專注於前端邏輯

## 系統架構設計

### 整體架構思路

```
自動保存系統架構

     ┌────────────────────────────────────────────────┐
     │                    UI 組件層                    │
     │                                                │
     │   ┌─────────────────┐   ┌────────────────────┐ │
     │   │  編輯器組件       │   │ SaveStatusIndicator│ │
     │   │  (表單輸入)       │   │ (保存狀態顯示)       │ │
     │   └─────────────────┘   └────────────────────┘ │
     │                                                │
     └────────────────────────────────────────────────┘
                           │
                           ▼
     ┌────────────────────────────────────────────────┐
     │                   業務邏輯層                     │
     │                                                │
     │      ┌────────────────────────────────────┐    │
     │      │         usePromptPageLogic         │    │
     │      │  • 表單狀態管理                      │    │
     │      │  • Debounce 自動保存                │    │
     │      │  • 數據驗證                         │    │
     │      │  • 錯誤處理                         │    │
     │      └────────────────────────────────────┘    │
     │                                                │
     └────────────────────────────────────────────────┘
                           │
                           ▼
     ┌────────────────────────────────────────────────┐
     │                   狀態管理層                     │
     │                                                │
     │   ┌─────────────────┐   ┌────────────────────┐ │
     │   │  useSaveStore   │   │  usePromptStore    │ │
     │   │  (保存狀態)      │   │  (業務數據)          │ │
     │   └─────────────────┘   └────────────────────┘ │
     │                                                │
     └────────────────────────────────────────────────┘
                           │
                           ▼
     ┌────────────────────────────────────────────────┐
     │                     API 層                     │
     │                                                │
     │               Firebase/REST API                │
     │                                                │
     └────────────────────────────────────────────────┘
```

### 核心設計原則

1. **關注點分離**：UI、業務邏輯、狀態管理各司其職
2. **單一職責**：每個 Hook 和組件都有明確的責任範圍  
3. **可組合性**：小而專精的函數可以靈活組合
4. **類型安全**：全程 TypeScript 保護，減少運行時錯誤

## 核心工具

### 1. Debounce 防抖機制

Debounce 是自動保存的核心技術，它能延遲函數執行直到停止觸發一段時間後。

```typescript
export type DebouncedFunction<T extends (...args: unknown[]) => unknown> = T & {
  cancel: () => void;
};

export default function debounce<T extends (...args: unknown[]) => void>(
  func: T, 
  delay: number
): DebouncedFunction<T> {
  let timer: ReturnType<typeof setTimeout>;
  
  const debouncedFunc = function (...args: Parameters<T>) {
    clearTimeout(timer);
    timer = setTimeout(() => {
      func(...args);
    }, delay);
  } as T;

  // 提供取消機制
  (debouncedFunc as DebouncedFunction<T>).cancel = () => {
    clearTimeout(timer);
  };

  return debouncedFunc as DebouncedFunction<T>;
}
```

- **泛型設計**：支持任意函數類型，保持類型安全
- **取消機制**：提供 `cancel` 方法，確保在組件卸載時清理資源

**在專案內應用：**
用戶每次按鍵都會觸發，但只有停止輸入 1 秒後才真正執行保存，這樣既保證了數據不丟失，又避免了過度頻繁的 API 調用。

### 2. 深度比較工具

為了避免不必要的保存觸發，會需要精確判斷數據是否真的有變更。

```typescript
export const deepEqual = (obj1: unknown, obj2: unknown): boolean => {
  // 快速路徑：相同引用或相同原始值
  if (obj1 === obj2) return true;
  
  // null 或 undefined 處理
  if (obj1 == null || obj2 == null) return obj1 === obj2;
  
  // 類型不同直接返回 false
  if (typeof obj1 !== typeof obj2) return false;
  
  // 原始類型直接比較
  if (typeof obj1 !== 'object') return obj1 === obj2;
  
  // 對象深度比較邏輯...
  // (省略具體實現細節)
  
  return true;
};
```

**為什麼會需要此一比較工具？**
在 JavaScript 中，對物件進行淺層比較時，如果它們的引用不同，即使內容完全相同，結果也會是 false。深度比較則能確保只有在內容真正變化時才觸發保存。

### 3. 狀態管理架構

#### 保存狀態 Store

這個 Store 專門管理保存相關的狀態，支持多個文件同時編輯的場景。

```typescript
interface SaveState {
  isSaving: boolean;  
  promptSaveStates: Record<string, { 
    lastSavedAt: Date | null;     // 最後保存時間
    hasSaveError: boolean;        // 是否有保存錯誤
    isActive: boolean;            // 是否有未保存的變更
  }>;
  setSaving: (isSaving: boolean, promptId?: string) => void;
  setSaved: (promptId: string) => void;
  setSaveError: (hasError: boolean, promptId?: string) => void;
  // ... 
}
```

**多實例支持的設計考量：**
這種設計允許同時追蹤多個檔案的保存狀態，每個檔案都有獨立的狀態管理，互不干擾。
在本專案中，透過保存狀態 Store（Save State Store）的設計實現多實例支持：

- Zustand 作為集中狀態管理器
- Zustand 提供一個全域的 Store，用來集中管理所有檔案的保存狀態。
  `Record<string, ...>` 結構管理多檔案狀態
  promptSaveStates 並非只儲存單一檔案狀態，而是採用類似字典（Map）的結構：
    - 鍵（Key）：每個檔案的唯一標識（如 promptId）
    - 值（Value）：包含該檔案獨立保存狀態（如 lastSavedAt、hasSaveError 等）的物件

運作方式說明：

- 當你需要更新或查詢某個檔案的狀態時，會以該檔案的 promptId 作為 key，從 promptSaveStates 這個大區塊中找到對應的狀態物件進行操作。

- 例如，若要標記 ID 為 "file-A" 的檔案有未保存變更，就更新 promptSaveStates['file-A'] 的狀態；而操作 ID 為 "file-B" 的檔案時，則讀寫 promptSaveStates['file-B'] 的狀態。

- 由於每個檔案都透過唯一 ID 進行索引，因此它們的狀態完全隔離，實現多實例支持且互不干擾。

這種設計不僅能集中管理，也能保證每個檔案的狀態獨立，適合需要同時處理多檔案狀態的應用場景。

### 4. 業務邏輯整合

核心的 `usePromptPageLogic` Hook 負責整合表單管理、變更檢測和自動保存觸發：

```typescript
export const usePromptPageLogic = ({ promptId }: UsePromptPageLogicProps) => {
  // 表單狀態管理
  const [formData, setFormData] = useState({
    name: "",
    shortcut: "",
    content: ""
  });
  
  // 存儲初始值用於變更比較
  const [initialValues, setInitialValues] = useState({
    name: "",
    shortcut: "",
    content: ""
  });

  // 核心保存函數
  const savePrompt = useCallback(async () => {
    // 保存邏輯實現
  }, [/* 依賴項 */]);

  // 創建 debounced 的保存函數
  const debouncedSave = useMemo(
    () => debounce(async () => {
      await savePrompt();
    }, 1000), // 1 秒延遲
    [savePrompt]
  );

  // 核心邏輯：檢查變更並觸發自動保存
  useEffect(() => {
    const hasChanges = !deepEqual(formData, initialValues);
    
    if (hasChanges && currentPrompt) {
      setActive(true, promptId);   // 標記為活躍狀態
      debouncedSave();             // 觸發延遲保存
    } else if (!hasChanges) {
      setActive(false, promptId);  // 標記為非活躍狀態
    }
    
    // 清理函數：取消待執行的保存
    return () => {
      debouncedSave.cancel();
    };
  }, [formData, initialValues, currentPrompt, debouncedSave, setActive, promptId]);

  // 返回表單處理器和狀態
  return {
    // 表單數據和處理函數
  };
};
```

**這個 Hook ：**

1. **自動觸發機制**：通過 `useEffect` 監聽表單數據變更
2. **智能去重**：使用 `deepEqual` 避免錯誤觸發
3. **狀態同步**：與 Zustand store 整合
4. **資源清理**：確保組件卸載時取消待執行的保存

## 用戶體驗優化

### 1. UI 的實時狀態反饋

`SaveStatusIndicator` 組件提供即時的保存狀態反饋：

```typescript
const SaveStatusIndicator = ({ className = '' }) => {
  const [displayState, setDisplayState] = useState<'idle' | 'saving' | 'saved' | 'error'>('idle');

  const renderContent = () => {
    switch (displayState) {
      case 'saving':
        return (
          <div className="flex items-center space-x-2 text-primary">
            <Spinner className="h-3 w-3 animate-spin" />
            <span>保存中...</span>
          </div>
        );
      case 'saved':
        return (
          <div className="flex items-center space-x-2 text-green-600">
            <CheckIcon className="h-3 w-3" />
            <span>已保存所有更改</span>
          </div>
        );
      case 'error':
        return (
          <div className="flex items-center space-x-2 text-red-600">
            <ErrorIcon className="h-3 w-3" />
            <span>保存失敗</span>
          </div>
        );
      default:
        return null;
    }
  };

  return (
    <div className={`transition-all duration-200 ${className}`}>
      {renderContent()}
    </div>
  );
};
```


## 錯誤處理與容錯設計

### 1. 網路異常處理

```typescript
const savePrompt = useCallback(async () => {
  try {
    setSaving(true, promptId);
    await updatePrompt(promptId, updatedPrompt);
    setSaved(promptId);
  } catch (error) {
    // 區分不同類型的錯誤
    if (error.status === 401) {
      // 認證失效，重定向登錄頁
      router.push('/login');
    } else if (error.status === 403) {
      // 權限不足
      setSaveError(true, promptId);
      showNotification('您沒有編輯此內容的權限');
    } else if (error.status >= 500) {
      // 服務器錯誤
      setSaveError(true, promptId);
      showNotification('服務器暫時無法響應，請稍後再試');
    } else {
      // 其他錯誤
      setSaveError(true, promptId);
      console.error("保存時發生錯誤:", error);
    }
  }
}, [/* 依賴項 */]);
```

## 專案實作內性能優化策略

### 1. 內存優化

```typescript
// 使用 useCallback 避免不必要的重新渲染
const handleNameChange = useCallback((e: React.ChangeEvent<HTMLInputElement>) => {
  updateFormField('name', e.target.value);
}, [updateFormField]);

// 使用 useMemo 緩存計算結果
const debouncedSave = useMemo(
  () => debounce(async () => {
    await savePrompt();
  }, 1000),
  [savePrompt]
);
```

### 3. 狀態更新優化

```typescript
// 使用 Zustand 的淺層比較避免不必要的訂閱觸發
const { isSaving, getSaveStateForPrompt } = useSaveStore(
  useShallow((state) => ({
    isSaving: state.isSaving,
    getSaveStateForPrompt: state.getSaveStateForPrompt
  }))
);
```

### 用戶體驗改善

1. 用戶無需手動保存，專注於內容創作
2. 清晰的保存狀態指示，用戶始終了解目前編輯狀態

## 適用場景與擴展

### 小結

通過這個真實項目的實踐，我學到了：

- **如何設計可擴展的前端架構**：模塊化設計讓系統易於維護和擴展
- **狀態管理的最佳實踐**：Zustand 作為輕量級但功能完善的狀態管理工具，帶來高效的解決方案
- **性能優化的具體技巧**：從 debounce 到內存管理，細緻調校提升整體效
- **用戶體驗設計的重要性**：技術實現需要為用戶體驗服務

這個項目體現了現代前端開發的高度複雜性，也證明了只要有合理的架構設計和邏輯實現，就能打造出穩定且高效的用戶體驗。自動保存功能雖然看似簡單，但要達到穩定性與流暢體驗，背後需要面對諸多細節與邊界情境的挑戰。