---
title: 製作簽名面板元件
date: 2023-12-23 12:49:11
tags: ["Vue"]
---

因工作需求需要製作簽名面板，查看套件使用度較高 [signature_pad](https://www.npmjs.com/package/signature_pad)

1. 在專案內安裝 signature_pad 套件 `npm i signature_pad`
2. 嘗試將引入的簽名套件包成可以重複使用的元件。

initializeSignaturePad : 進行初始化，如尺寸大小、筆的顏色、畫布背景色等。這邊將這些設置放在 props 讓使用時可以保有一些彈性空間。
resizeCanvas : 用於調整畫布的大小，目的是確保畫布的大小能夠適應不同的設備和螢幕解析度。
saveSignature\ clearSignature: 顧名思義就是儲存、清除簽名
注意：因為將簽名功能包成元件，使用 emit 將儲存的簽名傳到外層。

```vue
<template>
  <div
    class="signature-pad"
    :style="{ width: props.width, height: props.height }"
  >
    <canvas ref="canvas"></canvas>
    <slot>
      <button @click="clearSignature">清除簽名</button>
      <button @click="saveSignature">儲存簽名</button>
    </slot>
  </div>
</template>

<script setup>
import { onMounted, ref, watch, onUnmounted } from "vue";
import SignaturePad from "signature_pad";

const props = defineProps({
  width: { type: String, default: "100%" },
  height: { type: String, default: "300px" },
  penColor: { type: String, default: "black" },
  backgroundColor: { type: String, default: "white" },
  options: { type: Object, default: () => ({}) },
});

const canvas = ref(null);
let signaturePad = null;

const initializeSignaturePad = () => {
  if (canvas.value) {
    signaturePad = new SignaturePad(canvas.value, {
      ...props.options,
      penColor: props.penColor,
      backgroundColor: props.backgroundColor,
    });
  }
};

const resizeCanvas = () => {
  //window.devicePixelRatio 來獲取設備的像素密度，如果無法獲取到，則將ratio設為1。
  const ratio = Math.max(window.devicePixelRatio || 1, 1);
  //根據畫布元素的寬度和高度，乘以ratio，來設定畫布的寬度和高度。為了確保畫布在高像素密度的設備上顯示正確。
  canvas.value.width = canvas.value.offsetWidth * ratio;
  canvas.value.height = canvas.value.offsetHeight * ratio;
  //獲取畫布的2D繪圖上下文，並使用scale方法將繪圖上下文的縮放比例設為ratio。
  canvas.value.getContext("2d").scale(ratio, ratio);
  signaturePad.clear(); // Clears any existing drawing
};

const emit = defineEmits(["clear", "save"]);

const saveSignature = () => {
  if (signaturePad) {
    const signatureImage = signaturePad.toDataURL();
    emit("save", signatureImage);
  }
  return null;
};

const clearSignature = () => {
  if (signaturePad) {
    signaturePad.clear();
    emit("clear"); // 發射清除事件
  }
};

// Initialize and resize canvas
onMounted(() => {
  initializeSignaturePad();
  window.addEventListener("resize", resizeCanvas);
  resizeCanvas();
});

onUnmounted(() => {
  window.removeEventListener("resize", resizeCanvas);
});

// Watch for prop changes
watch(
  () => [props.penColor, props.backgroundColor, props.options],
  () => {
    initializeSignaturePad();
  },
  { deep: true }
);
</script>
```

### 參考資料：

https://github.com/WangShayne/vue3-signature
