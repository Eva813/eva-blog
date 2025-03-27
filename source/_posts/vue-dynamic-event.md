---
title: Vue 動態事件名稱
date: 2025-03-27 07:16:48
tags: ["Vue"]
categories: Vue
---

在 Vue 中使用動態事件名稱的功能自 Vue 2.6.0 版本開始支持。可以使用 v-on 或 @ 來動態綁定事件名稱

簡單舉例：
根據裝置類型切換事件（桌機 vs 手機）

```html
<script setup>
const event = ref(navigator.maxTouchPoints ? 'touchstart' : 'click')
const handle = () => console.log('Triggered on', event.value)
</script>

<button @[event]="handle">按我</button>
```


2.「檔案卡片」元件預覽或跳轉：

點擊 (click)：選擇／下載檔案
滑鼠移入 (mouseover)：預覽檔案摘要

```html
<template>
  <div class="file-card">
    <button v-on="{ click: handleDownload, mouseover: handlePreview }">
      {{ fileName }}
    </button>
    <div v-if="showPreview" class="preview-popup">
      {{ previewText }}
    </div>
  </div>
</template>
```

```javascript
<script setup lang="ts">
import { ref, computed, nextTick } from 'vue'

const props = defineProps<{ fileName: string; fileUrl: string }>()
const showPreview = ref(false)
const previewText = ref('Loading preview…')

// 點擊 → 觸發下載
const handleDownload = () => {
  window.open(props.fileUrl, '_blank')
}

// 滑鼠移入 → 取得並顯示檔案摘要
const handlePreview = async () => {
  showPreview.value = true
  // 模擬 API 請求
  await new Promise(res => setTimeout(res, 300))
  previewText.value = `Preview of ${props.fileName}`
}

// 滑鼠移出後隱藏預覽
const hidePreview = () => {
  showPreview.value = false
}

// 監聽滑鼠離開整個卡片
const cardRef = ref<HTMLElement | null>(null)
cardRef.value?.addEventListener('mouseleave', hidePreview)
</script>

<style scoped>
.file-card { position: relative; display: inline-block; }
.preview-popup {
  position: absolute;
  top: 100%;
  left: 0;
  padding: 0.5rem;
  border: 1px solid #ccc;
  background: white;
  box-shadow: 0 2px 6px rgba(0,0,0,0.1);
}
</style>
```

[Demo](https://stackblitz.com/edit/vue-tip-dynamic-event-names-evguwtw8?file=README.md)

注意：在「同一欄位會因不同情境而切換觸發事件」時，才建議用動態綁定。若只是單純切換事件名稱，建議直接在方法內判斷即可。


也可參考 Michael Hoffmann 這裡的另一實作方式：
https://stackblitz.com/edit/vue-tip-dynamic-event-names?file=src%2FApp.vue