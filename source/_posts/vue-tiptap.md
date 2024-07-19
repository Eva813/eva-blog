---
title: 使用 Vue3 與 Tiptap 打造文字編輯器
date: 2024-07-19 14:49:26
tags: [vue3]
---

在現代網頁應用中，強大的文字編輯器是必不可少的。Tiptap 是一款基於 ProseMirror 的高擴展性編輯器。本文將介紹如何在 Vue3 中使用 Tiptap，並結合 Vuetify 實現文字編輯器。

## 官網文件

首先官網：https://tiptap.dev/docs/editor/getting-started/install/vue3

```javascript
<template>
  <editor-content :editor="editor" />
</template>

<script setup>
  import { useEditor, EditorContent } from '@tiptap/vue-3'
  import StarterKit from '@tiptap/starter-kit'

  const editor = useEditor({
    content: '<p>I’m running Tiptap with Vue.js. 🎉</p>',
    extensions: [StarterKit],
  })
</script>
```

根據對應 EXTENSIONS 找出如何加入在編輯器，如：bold, italic

```javascript
     <button @click="editor.chain().focus().toggleBold().run()" :class="{ 'is-active': editor.isActive('bold') }">
      Toggle bold
    </button>
<button @click="editor.chain().focus().toggleItalic().run()" :class="{ 'is-active': editor.isActive('italic') }">
      Toggle italic
    </button>
```

<!-- more -->

以此類推... 根據所需要的功能一步步加入

## 專案內實踐

以 vue3 為範例 + vuetify

```html
<template>
  <div class="editor-container">
    <div v-if="editor" class="d-flex align-center flex-wrap toolbar">
      <v-menu>
        <template v-slot:activator="{ props }">
          <v-btn
            density="compact"
            class="mr-2 rounded"
            color="black"
            icon="mdi-format-size"
            v-bind="props"
            :variant="currentFontSize ? 'tonal' : 'text'"
          ></v-btn>
        </template>
        <v-list dense>
          <v-list-item @click="unsetFontSize()">
            {{ t('Shared.Default') }}
          </v-list-item>
          <v-list-item
            v-for="size in fontSizes"
            :key="size"
            :class="{ 'list-item-active' : editor.isActive('textStyle', { fontSize: `${size}px` })}"
            @click="setFontSize(`${size}px`)"
          >
            <v-list-item-title>{{ size }}</v-list-item-title>
          </v-list-item>
        </v-list>
      </v-menu>

      <v-btn
        density="compact"
        class="mr-2 rounded"
        :variant="editor.isActive('bold') ? 'tonal' : 'text'"
        color="black"
        icon="mdi-format-bold"
        @click="editor.chain().focus().toggleBold().run()"
      ></v-btn>
      <v-btn
        density="compact"
        class="mr-2 rounded"
        :variant="editor.isActive('italic') ? 'tonal' : 'text'"
        color="black"
        icon="mdi-format-italic"
        @click="editor.chain().focus().toggleItalic().run()"
      ></v-btn>
      <v-btn
        density="compact"
        class="mr-2 rounded"
        :variant="editor.isActive('bulletList') ? 'tonal' : 'text'"
        color="black"
        icon="mdi-format-list-bulleted"
        @click="editor.chain().focus().toggleBulletList().run()"
      ></v-btn>
      <v-btn
        density="compact"
        class="mr-2 rounded"
        :variant="editor.isActive('orderedList') ? 'tonal' : 'text'"
        color="black"
        icon="mdi-format-list-numbered"
        @click="editor.chain().focus().toggleOrderedList().run()"
      ></v-btn>
      <!-- 靠左/中/右 -->
      <v-btn
        density="compact"
        class="mr-2 rounded"
        :variant="editor.isActive({ textAlign: `left` }) ? 'tonal' : 'text'"
        color="black"
        icon="mdi-format-align-left"
        @click="editor.chain().focus().setTextAlign('left').run()"
      ></v-btn>
      <v-btn
        density="compact"
        class="mr-2 rounded"
        :variant="editor.isActive({ textAlign: 'center' }) ? 'tonal' : 'text'"
        color="black"
        icon="mdi-format-align-center"
        @click="editor.chain().focus().setTextAlign('center').run()"
      ></v-btn>
      <v-btn
        density="compact"
        class="mr-2 rounded"
        :variant="editor.isActive({ textAlign: `right` }) ? 'tonal' : 'text'"
        color="black"
        icon="mdi-format-align-right"
        @click="editor.chain().focus().setTextAlign('right').run()"
      ></v-btn>
      <!-- 顏色 -->
      <v-menu>
        <template v-slot:activator="{ props }">
          <v-btn
            density="compact"
            class="mr-2 rounded"
            :color="currentColor ? 'white' : 'black'"
            icon="mdi-format-color-text"
            v-bind="props"
            :variant="currentColor ? 'tonal' : 'text'"
            :style="{ 'background-color': currentColor }"
          ></v-btn>
        </template>
        <v-list dense>
          <v-list-item @click="unsetColor()">
            {{ t('Shared.Default') }}
          </v-list-item>
          <v-list-item
            v-for="color in textColor"
            :key="color"
            class="d-flex align-center justify-center"
            @click="setColor(color)"
          >
            <v-list-item-title>
              <div
                :style="{ 'background-color': color }"
                class="editor-color-container"
                :class="{ 'list-colorPicker-active' : isColorActive(color) }"
              ></div>
            </v-list-item-title>
          </v-list-item>
        </v-list>
      </v-menu>
    </div>
    <EditorContent
      :editor="editor"
      class="p-2 tiptap-container"
      :style="{ height: props.height, borderColor: hasError ? 'red' : '' }"
    />
    <v-col v-if="hasError" class="pt-1 pl-6 text-caption">
      <span class="text-red">{{ t('CommonErrorMessage.Required') }}</span>
    </v-col>
  </div>
</template>
```

```js
<script setup lang="ts">
import { useEditor, EditorContent } from '@tiptap/vue-3'
import StarterKit from '@tiptap/starter-kit'
import TextStyle from '@tiptap/extension-text-style';
import { FontSize } from './fontSizeExtension';
import TextAlign from '@tiptap/extension-text-align'
import { Color } from '@tiptap/extension-color'

const props = defineProps({
    modelValue: String as PropType<string | null>,
    height: {
        type: String as PropType<string>,
        default: '12rem',
    },
    isRequired: {
        type: Boolean,
        default: false,
    }
})
const { t } = useI18n();
const emit = defineEmits(['update:modelValue']);
const hasError = ref(false);
const fontSizes = ref(['12', '14', '16', '18', '20', '24']);
const textColor = ref(['#F44336', '#2196F3','#FFC107', '#757575', '#BF360C', '#FF9800'])
const currentColor = ref('');
const currentFontSize = ref('');

const editor = useEditor({
    content: props.modelValue,
    onUpdate: ({ editor }) => {
        const value = editor.isEmpty ? '' : editor.getHTML();
        if (value) {
            validateContent(value);
        }
        emit('update:modelValue', value);
        updateToolbarState();
    },
    onBlur({ editor }) {
        const value = editor.isEmpty ? '' : editor.getHTML();
        if (!value && props.isRequired) {
            validateContent(value);
        }
    },
    extensions: [
        StarterKit,
        TextStyle,
        Color,
        FontSize.configure({
            types: ['textStyle'],
        }),
        TextAlign.configure({
            types: ['heading', 'paragraph'],
        }),
    ]
})

const validateContent = (content: string) => {
    hasError.value = !content;
};
const setFontSize = (size: string) => {
    currentFontSize.value = size;
    editor.value?.chain().focus().setFontSize(size).run();
    updateToolbarState();
};
const unsetFontSize = () => {
    currentFontSize.value = '';
    editor.value?.chain().focus().unsetFontSize().run();
    updateToolbarState();
};
const setColor = (color: string) => {
    currentColor.value = color;
    editor.value?.chain().focus().setColor(color).run();
    updateToolbarState();
};
const unsetColor = () => {
    currentColor.value = '';
    editor.value?.chain().focus().unsetColor().run();
    updateToolbarState();
};

const updateToolbarState = () => {
    currentColor.value = editor.value?.getAttributes('textStyle').color || '';
    currentFontSize.value = editor.value?.getAttributes('textStyle').fontSize || '';
};

// active color
const isColorActive = computed(() => (color: string) => {
    return editor.value?.isActive('textStyle', { color }) || false;
});

watch(() => props.modelValue, (newValue) => {
    if (editor.value && newValue !== editor.value.getHTML()) {
        editor.value.commands.setContent(newValue || '', false);
    }
});

onBeforeUnmount(() => {
    editor.value?.destroy();
})
</script>

```

![image](https://hackmd.io/_uploads/BJVzYNwdC.png)

### 編輯器功能項目

我有加入的功能有：
文字大小、粗體、斜體、bulletList、orderedList、對齊(靠左、置中、靠右)、文字顏色

### 文字編輯區塊:

```javascript
<EditorContent :editor="editor" class="p-2 tiptap-container" :style="{ height: props.height, borderColor: hasError ? 'red' : '' }"/>
```

綁定的 style 是:

1.height: 要彈性設置編輯區塊高度
2.borderColor: 因為考量所設置編輯器的頁面，須設置必填寫

### 必填

在 props 設置控制是否為必填寫

useEditor: 在 onUpdate、onBlur 兩動作去檢查是否有值，接著 hasError 取得檢查結果，分別會在邊框、以及區塊下方顯示提示訊息
![image](https://hackmd.io/_uploads/rJHjYVv_C.png)

### text-align

```javascript
editor.chain().focus().setTextAlign("left").run();
```

https://tiptap.dev/docs/editor/extensions/functionality/textalign

### fontSize

是額外撰寫一個 fontSizeExtension.ts
透過 addGlobalAttributes 設置 html 樣式

```javascript
 parseHTML: (element) => element.style.fontSize.replace(/['"]+/g, ""),
```

HTML 元素中解析出屬性值，從元素的 style 屬性中提取 font-size，並移取代("")，目的是將 HTML 中的 font-size 樣式轉換為内部使用的屬性值。

```javascript
renderHTML: (attributes) => {
  if (!attributes.fontSize) {
    return {};
  }

  return {
    style: `font-size: ${attributes.fontSize}`,
  };
};
```

若屬性沒有 fontSize 則為空物件，否則，會回傳包含 style 屬性的物件。

`addCommands` 用於定義可在編輯器中執行的方法

```javascript
addCommands() {
    return {
        setFontSize:
            (fontSize) =>
                ({ chain }) => {
                return chain().setMark("textStyle", { fontSize }).run();
            },
        unsetFontSize:
            () =>
            ({ chain }) => {
                return chain()
                    .setMark("textStyle", { fontSize: null })
                    .removeEmptyTextStyle()
                    .run();
            },
    };
}
```

1. setFontSize: 設置內文文字大小
2. unsetFontSize: 取消內文文字大小的設置

在使用時，用下拉選單可以選擇 fontSize 項目

![image](https://hackmd.io/_uploads/B1INtrPdR.png)

```html
<v-menu>
  <template v-slot:activator="{ props }">
    <v-btn
      density="compact"
      class="mr-2 rounded"
      color="black"
      icon="mdi-format-size"
      v-bind="props"
      :variant="currentFontSize ? 'tonal' : 'text'"
    ></v-btn>
  </template>
  <v-list dense>
    <v-list-item @click="unsetFontSize()">
      {{ t('Shared.Default') }}
    </v-list-item>
    <v-list-item
      v-for="size in fontSizes"
      :key="size"
      :class="{ 'list-item-active' : editor.isActive('textStyle', { fontSize: `${size}px` })}"
      @click="setFontSize(`${size}px`)"
    >
      <v-list-item-title>{{ size }}</v-list-item-title>
    </v-list-item>
  </v-list>
</v-menu>
```

```javascript
<script setup lang="ts">
import { useEditor, EditorContent } from '@tiptap/vue-3'
import StarterKit from '@tiptap/starter-kit'
import TextStyle from '@tiptap/extension-text-style';
import { FontSize } from './fontSizeExtension';
import TextAlign from '@tiptap/extension-text-align'
import { Color } from '@tiptap/extension-color'

const props = defineProps({
    modelValue: String as PropType<string | null>,
    height: {
        type: String as PropType<string>,
        default: '12rem',
    },
    isRequired: {
        type: Boolean,
        default: false,
    }
})
const { t } = useI18n();
const emit = defineEmits(['update:modelValue']);
const hasError = ref(false);
const fontSizes = ref(['12', '14', '16', '18', '20', '24']);

const currentFontSize = ref('');

const editor = useEditor({
    content: props.modelValue,
    onUpdate: ({ editor }) => {
        const value = editor.isEmpty ? '' : editor.getHTML();
        if (value) {
            validateContent(value);
        }
        emit('update:modelValue', value);
        updateToolbarState();
    },
    onBlur({ editor }) {
        const value = editor.isEmpty ? '' : editor.getHTML();
        if (!value && props.isRequired) {
            validateContent(value);
        }
    },
    extensions: [
        StarterKit,
        TextStyle,
        Color,
        FontSize.configure({
            types: ['textStyle'],
        }),
        TextAlign.configure({
            types: ['heading', 'paragraph'],
        }),
    ]
})

const validateContent = (content: string) => {
    hasError.value = !content;
};
const setFontSize = (size: string) => {
    currentFontSize.value = size;
    editor.value?.chain().focus().setFontSize(size).run();
    updateToolbarState();
};
const unsetFontSize = () => {
    currentFontSize.value = '';
    editor.value?.chain().focus().unsetFontSize().run();
    updateToolbarState();
};


const updateToolbarState = () => {
    currentColor.value = editor.value?.getAttributes('textStyle').color || '';
    currentFontSize.value = editor.value?.getAttributes('textStyle').fontSize || '';
};


watch(() => props.modelValue, (newValue) => {
    if (editor.value && newValue !== editor.value.getHTML()) {
        editor.value.commands.setContent(newValue || '', false);
    }
});

onBeforeUnmount(() => {
    editor.value?.destroy();
})
</script>

```

參考資料：
https://github.com/ueberdosis/tiptap/issues/2730
https://gist.github.com/gregveres/64ec1d8a733feb735b7dd4c46331abae

### 設置文字顏色

使用顏色設置方法
`currentColor` 用來決定顯示該項目被選擇

1. :variant: 用來設置按鈕風格樣式，tonal 為按鈕有背景色，而 text 為文字有顏色但沒有背景色。
2. 在下拉選單的顏色綁定陣列設置的色彩，並透過 setColor 決定顏色

![image](https://hackmd.io/_uploads/rke5W8vuR.png)

```html
<v-menu>
  <template v-slot:activator="{ props }">
    <v-btn
      density="compact"
      class="mr-2 rounded"
      :color="currentColor ? 'white' : 'black'"
      icon="mdi-format-color-text"
      v-bind="props"
      :variant="currentColor ? 'tonal' : 'text'"
      :style="{ 'background-color': currentColor }"
    ></v-btn>
  </template>
  <v-list dense>
    <v-list-item @click="unsetColor()"> {{ t('Shared.Default') }} </v-list-item>
    <v-list-item
      v-for="color in textColor"
      :key="color"
      class="d-flex align-center justify-center"
      @click="setColor(color)"
    >
      <v-list-item-title>
        <div
          :style="{ 'background-color': color }"
          class="editor-color-container"
          :class="{ 'list-colorPicker-active' : isColorActive(color) }"
        ></div>
      </v-list-item-title>
    </v-list-item>
  </v-list>
</v-menu>
```

```javaScript
<script setup lang="ts">
import { useEditor, EditorContent } from '@tiptap/vue-3'
import StarterKit from '@tiptap/starter-kit'
import TextStyle from '@tiptap/extension-text-style';
import TextAlign from '@tiptap/extension-text-align'
import { Color } from '@tiptap/extension-color'

const props = defineProps({
    modelValue: String as PropType<string | null>,
    height: {
        type: String as PropType<string>,
        default: '12rem',
    },
    isRequired: {
        type: Boolean,
        default: false,
    }
})
const { t } = useI18n();
const emit = defineEmits(['update:modelValue']);
const hasError = ref(false);
const textColor = ref(['#F44336', '#2196F3','#FFC107', '#757575', '#BF360C', '#FF9800'])
const currentColor = ref('');
const currentFontSize = ref('');

const editor = useEditor({
    content: props.modelValue,
    onUpdate: ({ editor }) => {
        const value = editor.isEmpty ? '' : editor.getHTML();
        if (value) {
            validateContent(value);
        }
        emit('update:modelValue', value);
        updateToolbarState();
    },
    onBlur({ editor }) {
        const value = editor.isEmpty ? '' : editor.getHTML();
        if (!value && props.isRequired) {
            validateContent(value);
        }
    },
    extensions: [
        StarterKit,
        TextStyle,
        Color,
        FontSize.configure({
            types: ['textStyle'],
        }),
        TextAlign.configure({
            types: ['heading', 'paragraph'],
        }),
    ]
})

const validateContent = (content: string) => {
    hasError.value = !content;
};


const setColor = (color: string) => {
    currentColor.value = color;
    editor.value?.chain().focus().setColor(color).run();
    updateToolbarState();
};
const unsetColor = () => {
    currentColor.value = '';
    editor.value?.chain().focus().unsetColor().run();
    updateToolbarState();
};

const updateToolbarState = () => {
    currentColor.value = editor.value?.getAttributes('textStyle').color || '';
    currentFontSize.value = editor.value?.getAttributes('textStyle').fontSize || '';
};

// active color
const isColorActive = computed(() => (color: string) => {
    return editor.value?.isActive('textStyle', { color }) || false;
});

watch(() => props.modelValue, (newValue) => {
    if (editor.value && newValue !== editor.value.getHTML()) {
        editor.value.commands.setContent(newValue || '', false);
    }
});

onBeforeUnmount(() => {
    editor.value?.destroy();
})
</script>

```

## 小結

本文介紹了如何使用 Tiptap 在 Vue3 中實現具有多項功能的文字編輯器。通過靈活的擴展，您可以輕鬆添加所需功能。希望透過範例和解釋能幫助更好地理解和使用 Tiptap。如果您有更多需求，請參考官方文檔進行進一步探索。
