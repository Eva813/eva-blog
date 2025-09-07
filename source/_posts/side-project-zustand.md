---
title: side-project-zustand
date: 2025-03-31 07:23:58
tags: ["React", "Zustand", "side-project"]
categories: side-project
---

這段時間在製作一個 snippet 管理的 side project，過程中遇到了一些跨頁面共享資料的需求。專案中有一包資料稱為 snippet，內部包含多個 folder 和小項目的 snippets。這些資料不僅會在 /snippet 路徑下的元件使用，還會跨 route 被其他頁面存取。

一開始，我選擇使用 React 的 Context API，透過建立一個 SnippetsContext 來管理狀態。在應用程式的最上層放置一次 SnippetsProvider，然後在需要的任何元件內直接使用 import { useSnippets } from '@/contexts/SnippetsContext' 來取得 context。然而，隨著專案規模逐步擴展，這種方法逐漸顯露出其局限性。我發現即使已經有了最上層的 Provider，但在多個 dialog 或 component 中，仍然需要重複地導入 useSnippets，不僅造成代碼重複，也使得專案結構變得臃腫複雜。

為了解決上述問題，我嘗試了另一個狀態管理工具 —— Zustand。Zustand 提供了一種更直覺、更精簡的方式來建立和存取全域狀態，不需要透過 Provider 包裹元件，大幅減少了樣板代碼的使用，也讓資料流更加清晰易懂。

在這篇文章中，我會分享如何在專案中實際導入並使用 Zustand

## 初步實作 Zustand

```shell
npm install zustand
```
如果想使用持久化功能，也可以安裝：
```shell
npm install zustand/middleware
```

## store 資料夾
在專案的 src 資料夾下建立一個 store 資料夾，這之中包含給 folder , snippet 的 slice。之所以採用 slice 是因為原本 snippet 的處理邏輯較為複雜，包含了 folder 的儲存、內部 snippet 的儲存，以及一些專門給 dialog 使用的 UI 狀態。透過將 store 分拆成多個 slice（例如 folderSlice 與 uiSlice），每個模組都只負責處理自己相關的 state 與操作，能更好地維護代碼的結構與可讀性。

```javascript
src/
 ├── types/
 │    └── snippet.ts            // 共用型別，也可以有 auth.ts, settings.ts 等
 └── stores/
      ├── snippet/
      │     ├── slices/
      │     │     ├── folderSlice.ts
      │     │     ├── snippetSlice.ts
      │     │     └── uiSlice.ts
      │     └── index.ts        // snippetStore.ts
      ├── auth/
      │     └── index.ts        // 假如之後， authStore.ts (可以進一步拆分 authSlices)
      └── settings/
            └── index.ts        // 假如之後，settingsStore.ts
```

在專案中的 snippet 資料夾內，會建立一個 index.ts 檔案來整合所有獨立的 slice，並導出一個完整的 Zustand store。這麼做的優點是：之後在任何元件中需要使用狀態時，只需導入整個 store，而不需要逐一引入每個 slice

```typescript
// src/stores/snippet/index.ts
import { create } from 'zustand';
import { persist } from 'zustand/middleware';
import { FolderSlice, createFolderSlice } from './slices/folderSlice';
import { SnippetSlice, createSnippetSlice } from './slices/snippetSlice';
import { UISlice, createUISlice } from './slices/uiSlice';

// 組合所有模組的型別
export type AppStore = FolderSlice & SnippetSlice & UISlice;

export const useSnippetStore = create<AppStore>()(
  persist(
    (set, get, api) => ({
      ...createFolderSlice(set, get, api),
      ...createSnippetSlice(set, get, api),
      ...createUISlice(set, get, api),
    }),
    {
      name: 'my-snippets-storage',
      // partialize 使我們只持久化 folders，UI 狀態不必存入 localStorage
      partialize: (state) => ({ folders: state.folders }),
    }
  )
);
```
- 使用 zustand 提供的 persist middleware，能輕鬆將特定狀態持久化到 localStorage。
- 透過 partialize 參數，我能夠靈活地指定只有某些狀態 (例如：folders) 需要持久化，而 UI 或其他臨時狀態則保持記憶體內即可。

## folderSlice.ts

在建立 folderSlice 時，透過 Zustand 的 StateCreator 定義了一個 slice，專門負責管理資料夾（folders）相關的狀態與邏輯。透過這個方式，能清楚地將資料夾的操作行為集中管理。

```typescript
// src/stores/snippet/slices/folderSlice.ts
import { StateCreator } from 'zustand';
import { Folder } from '@/types/snippets';

export interface FolderSlice {
  folders: Folder[];
  setFolders: (folders: Folder[]) => void;
  updateFolder: (id: string, updates: Partial<Folder>) => void;
  addFolder: (folder: Omit<Folder, "id">, index?: number) => Folder;
  deleteFolder: (id: string) => void;
}

export const createFolderSlice: StateCreator<FolderSlice> = (set, get) => ({
  folders: [
    {
      id: 'HplOMyf2mDqvVMdphJbt',
      name: 'My Sample Snippets',
      description: 'This is a sample folder',
      snippets: [
        {
          id: '5mJw031VPo2WxNIQyeXN',
          name: 'Demo - Plain text',
          content: 'be a software engineer',
          shortcut: '/do',
        },
        {
          id: '6mJw031VPo2WxNIQyeYN',
          name: 'Demo - Styled Text',
          content:
            'be a translate expert, I will give you a sentence and help me translate to english',
          shortcut: '/doT',
        },
      ],
    },
    {
      id: 'folder-1741057188488',
      name: 'Test',
      description: 'test',
      snippets: [
        {
          id: 'snippet-1741057206823',
          name: 'test',
          content: '<p>New snippet content Test</p>',
          shortcut: '/test',
        },
      ],
    },
  ],
  setFolders: (folders) => set({ folders }),
  updateFolder: (id, updates) =>
    set({
      folders: get().folders.map((folder) =>
        folder.id === id ? { ...folder, ...updates } : folder
      ),
    }),
    // 新增資料夾，可選擇插入位置
    addFolder: (folder, index) => {
      const newFolder: Folder = { ...folder, id: `folder-${Date.now()}` };
      set((state) => ({
        folders:
          typeof index === "number"
            ? [
                ...state.folders.slice(0, index),
                newFolder,
                ...state.folders.slice(index),
              ]
            : [...state.folders, newFolder],
      }));
      return newFolder;
    },
  deleteFolder: (id) =>
    set({
      folders: get().folders.filter((folder) => folder.id !== id),
    }),
});
```
- 使用 set 來更新狀態，get 取得當前狀態。
- addFolder 方法可指定新增資料夾的位置（透過可選的 index 參數）；若未指定，則預設新增到列表尾端。

## snippetSlice.ts

snippetSlice 負責 snippet 的新增、刪除與更新操作。由於 snippets 存放於 folders 中，因此此 slice 會依賴 FolderSlice 的狀態來進行資料處理。

```typescript
// src/stores/snippet/slices/snippetSlice.ts
import { StateCreator } from 'zustand';
import { Snippet } from '@/types/snippets';
import { FolderSlice } from './folderSlice';

export interface SnippetSlice {
  addSnippetToFolder: (folderId: string, snippet: Omit<Snippet, 'id'>) => Snippet;
  deleteSnippetFromFolder: (folderId: string, snippetId: string) => void;
  updateSnippet: (snippetId: string, updatedSnippet: Partial<Snippet>) => void;
}

// 這裡依賴 FolderSlice，因為 snippets 都儲存在 folders 內
// 修改後，addSnippetToFolder 回傳新 snippet
export const createSnippetSlice: StateCreator<
  FolderSlice & SnippetSlice,
  [],
  [],
  SnippetSlice
> = (set, get) => ({
  // 新增一個 snippet 到指定的資料夾，並回傳新增的 snippet（包含自動生成的 id）
  addSnippetToFolder: (folderId, snippet) => {
    const newSnippet: Snippet = { ...snippet, id: `snippet-${Date.now()}` };
    set({
      folders: get().folders.map((folder) =>
        folder.id === folderId
          ? { ...folder, snippets: [...folder.snippets, newSnippet] }
          : folder
      ),
    });
    return newSnippet; // 回傳新增後完整的 snippet 物件
  },
  deleteSnippetFromFolder: (folderId, snippetId) =>
    set({
      folders: get().folders.map((folder) =>
        folder.id === folderId
          ? {
              ...folder,
              snippets: folder.snippets.filter(
                (snippet) => snippet.id !== snippetId
              ),
            }
          : folder
      ),
    }),
  updateSnippet: (snippetId, updatedSnippet) =>
    set({
      folders: get().folders.map((folder) => ({
        ...folder,
        snippets: folder.snippets.map((snippet) =>
          snippet.id === snippetId ? { ...snippet, ...updatedSnippet } : snippet
        ),
      })),
    }),
});
```

- 新增 snippet（addSnippetToFolder）

  提供一個資料夾 id 和 snippet 的內容（不含 id）。
  自動產生 snippet 的 id（透過 Date.now()）。
  將新的 snippet 放到指定的資料夾內，並回傳完整的新 snippet，方便後續操作使用。

- 刪除 snippet（deleteSnippetFromFolder）

  根據指定的資料夾 id 和 snippet id 移除 snippet。
  使用 filter 方法，確保其他 snippet 不受影響。

- 更新 snippet（updateSnippet）

  -提供 snippet id 和欲更新的部分 snippet 屬性。
  -使用 map 方法，只更新符合指定 id 的 snippet，其他 snippet 維持不變。

## uiSlice.ts

uiSlice 專門用來管理專案內與 UI 顯示相關的狀態，例如 dialog（對話框）的開啟或關閉狀態，以及當前匹配（matched）的 snippet 資訊。

```typescript
// src/stores/snippet/slices/uiSlice.ts
import { StateCreator } from 'zustand';
import { MatchedSnippet } from '@/types/snippets';

export interface UISlice {
  isDialogOpen: boolean;
  matchedSnippet: MatchedSnippet;
  setIsDialogOpen: (open: boolean) => void;
  setMatchedSnippet: (snippet: MatchedSnippet) => void;
}

export const createUISlice: StateCreator<UISlice> = (set) => ({
  isDialogOpen: false,
  matchedSnippet: {
    content: '',
    targetElement: null,
    insert: false,
    shortcut: '',
  },
  setIsDialogOpen: (open: boolean) => set({ isDialogOpen: open }),
  setMatchedSnippet: (snippet: MatchedSnippet) => set({ matchedSnippet: snippet }),
});
```
- isDialogOpen
負責追蹤 dialog 是否正在顯示，便於控制 dialog 元件的顯示與隱藏。
- matchedSnippet
用於暫時儲存用戶目前匹配到的 snippet 資訊，例如 snippet 的內容、目標元素、快捷鍵資訊等等，讓 UI 能即時反應用戶操作。


## 在頁面中使用 Zustand 管理的狀態

想在頁面或元件內使用剛才建立的 Zustand 狀態時，只需從我們整合好的 useSnippetStore 引入所需的狀態或方法即可

```typescript
import { useSnippetStore } from '@/stores/snippet';
import { useEffect } from 'react';
import { Snippet } from '@/types/snippets';

function MyComponent() {
  // 從整合後的 store 直接取出需要的方法
  const { addFolder } = useSnippetStore();

  useEffect(() => {
    // 舉例：在元件載入時新增一個 folder
    const newFolder = addFolder({
      name: 'New Folder',
      description: 'Example folder created on load',
      snippets: [],
    });

    console.log('新增了資料夾：', newFolder);
  }, [addFolder]);

  return <div>你的元件內容</div>;
}

```
- 不再需要額外引入任何 Context Provider。
- 在任何需要存取全局狀態的地方，只需簡單導入 useSnippetStore，然後直接調用對應的方法或取得狀態即可。

## 結論
藉由導入 Zustand 並將狀態拆分成多個獨立的 Slice（folderSlice、snippetSlice、uiSlice），使我能夠：

- 清晰地分離狀態責任：每個 Slice 專注於處理特定領域的邏輯，如資料夾管理、snippet 操作，以及 UI 狀態。
- 靈活管理持久化策略：透過 Zustand 提供的 middleware（如 persist 與 partialize），只將必要的資料（例如資料夾）持久化至 localStorage，而 UI 等臨時狀態則保留在記憶體中。
- 有效降低耦合度與樣板代碼：每個元件不再需要額外引入 Provider，簡化了開發流程，並且降低未來維護成本。