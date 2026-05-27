---
created: 2026-05-27
tags:
  - java/internship
  - vue3
related:
  - "[[basics]]"
  - "[[../_index]]"
---

# 组合式 API 进阶

## 组合函数（Composables）

**抽取复用逻辑，替代 Vue 2 的 mixin**

```js
// useCounter.js
import { ref, computed } from 'vue'

export function useCounter(initial = 0) {
  const count = ref(initial)
  const double = computed(() => count.value * 2)

  function increment() { count.value++ }
  function decrement() { count.value-- }
  function reset()     { count.value = initial }

  return { count, double, increment, decrement, reset }
}
```

```vue
<script setup>
import { useCounter } from './useCounter'
const { count, double, increment } = useCounter(10)
</script>
```

> **好处**：比 mixin 更清晰，数据来源一目了然，没有命名冲突

## 异步处理

```vue
<script setup>
import { ref } from 'vue'

const data = ref(null)
const loading = ref(false)

async function fetchData() {
  loading.value = true
  try {
    const res = await fetch('/api/users')
    data.value = await res.json()
  } catch (e) {
    console.error(e)
  } finally {
    loading.value = false
  }
}

fetchData()
</script>
```

### 封装请求组合函数

```js
// useFetch.js
import { ref } from 'vue'

export function useFetch(url) {
  const data = ref(null)
  const error = ref(null)
  const loading = ref(true)

  fetch(url)
    .then(res => res.json())
    .then(json => data.value = json)
    .catch(e => error.value = e)
    .finally(() => loading.value = false)

  return { data, error, loading }
}
```

```vue
<script setup>
const { data, loading } = useFetch('/api/users')
</script>
```

## 模板引用 ref

```vue
<script setup>
import { ref, onMounted } from 'vue'

const inputRef = ref(null)

onMounted(() => {
  inputRef.value.focus()  // 自动聚焦
})
</script>

<template>
  <input ref="inputRef" type="text" />
</template>
```

## provide / inject

**跨层级传数据（不用逐层 props）**

```vue
<!-- 祖先组件 -->
<script setup>
import { provide, ref } from 'vue'
const theme = ref('dark')
provide('theme', theme)
</script>

<!-- 子孙组件 -->
<script setup>
import { inject } from 'vue'
const theme = inject('theme', 'light')  // 第二个参数是默认值
</script>
```

---

**相关笔记**: [[basics]] · [[router]]
