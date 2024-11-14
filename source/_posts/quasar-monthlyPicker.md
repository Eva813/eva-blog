---
title: 自訂 Quasar 日期選擇器：實現 Year/Month 選擇功能
date: 2024-08-24 19:46:38
tags: [quasar, Vue]
categories: Quasar
---

![image](https://hackmd.io/_uploads/B1UukbOo0.png)

在 Quasar 的日期選擇器，要使用 QInput + QDate 方式，可以參考文件 [Date Picker](https://quasar.dev/vue-components/date/#with-qinput)。

但在此次功能需求，是讓使用者只能選擇年/月。所以首先依循查詢結果，找到 [monthpicker](https://github.com/quasarframework/quasar/discussions/13101) ，在其中 [codepen - QDate: DATE YEAR/MONTH PICKER](https://codepen.io/metalsadman/pen/xxYBqMo)，透過設置 setView 屬性，使得選擇器界面可以顯示年、月以及日的選擇視圖。

## 自制 monthlyPicker

因此參考上方的方式，建立一個 monthlyPicker 元件。

```html
<template>
  <div class="q-mr-md">
    <q-input
      dense
      v-model="monthValue"
      id="date-input"
      class="single-month-picker"
      readonly
    >
      <template v-slot:append>
        <q-icon
          name="fa-regular fa-calendar"
          class="cursor-pointer"
          size="1.1rem"
        >
          <q-popup-proxy cover transition-show="scale" transition-hide="scale">
            <div class="q-date__header flex column items-start">
              <span @click="handleToMonthView" class="cursor-pointer"
                >{{ "Year - Month" }}</span
              >
              <div class="text-h5 cursor-pointer" @click="handleToYearView">
                {{ monthValue }}
              </div>
            </div>
            <q-date
              class="date-disable-btn"
              dense
              ref="dateRef"
              v-model="monthValue"
              default-view="Years"
              emit-immediately
              @update:model-value="onUpdate"
              mask="YYYY-MM"
              minimal
              years-in-month-view
              :navigation-min-year-month="minYearMonth"
              :navigation-max-year-month="maxYearMonth"
            >
              <div class="row items-center justify-end date-action-btn">
                <q-btn
                  dense
                  flat
                  v-close-popup
                  class="btn--no-hover"
                  label="Close"
                  color="primary"
                />
              </div>
            </q-date>
          </q-popup-proxy>
        </q-icon>
      </template>
    </q-input>
  </div>
</template>
```

```javascript
<script setup lang="ts">
import { date, QDate } from 'quasar'

const emit = defineEmits(['selectedDate'])
const props = defineProps({
    month: {
        type: String,
        required: true
    }
})
const monthValue = ref(props.month)
const dateRef = ref<InstanceType<typeof QDate> | null>(null)
const currentView = ref<'Years' | 'Months' | 'Days'>('Years')

// computed
const minYearMonth = computed(() => {
    const oneYearAgo = date.subtractFromDate(new Date(), { month: 13 })
    return date.formatDate(oneYearAgo, 'YYYY/MM')
})

const maxYearMonth = computed(() => {
    const oneMonthAgo = date.subtractFromDate(new Date(), { month: 1 })
    return date.formatDate(oneMonthAgo, 'YYYY/MM')
})

const onUpdate = () => {
    emit('selectedDate', date.formatDate(new Date(monthValue.value), 'YYYY-MM'))
    setCalendarView('Months')
}

const handleToMonthView = () => {
    setCalendarView('Months')
}
const handleToYearView = () => {
    setCalendarView('Years')
}

const setCalendarView = (view: 'Years' | 'Months') => {
    currentView.value = view
    dateRef.value?.setView(view)
}
</script>
```

### 簡要說明

- QDate 組件：設置 default-view 為 Years，讓選擇器初始顯示年份視圖。mask 設置為 YYYY-MM，確保輸出格式為年-月。
- 計算屬性：透過 minYearMonth 和 maxYearMonth 屬性，限制使用者只能選擇過去一年內的日期範圍。
- 視圖切換：通過 handleToMonthView 和 handleToYearView 函數來實現視圖切換，並且用 setCalendarView 函數來控制視圖變更。

## 外層使用該元件方式

```html
<template>
  <div>
    <monthly-picker :month="selectedMonth" @selectedDate="handleDateSelected" />
    <p>你選擇的月份是：{{ selectedMonth }}</p>
  </div>
</template>

<script setup lang="ts">
  import { ref } from "vue";
  import MonthlyPicker from "./components/MonthlyPicker.vue";

  const selectedMonth = ref("2024-08");

  const handleDateSelected = (date: string) => {
    selectedMonth.value = date;
  };
</script>
```
