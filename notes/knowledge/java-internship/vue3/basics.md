---
created: 2026-05-27
tags:
  - java/internship
  - vue3
related:
  - "[[../_index]]"
  - "[[composition-api]]"
---

# Vue 3 基础

## 创建项目

```bash
npm create vue@latest          # 官方推荐（脚手架）
npm create vite@latest         # Vite 构建（推荐）
npm install -g @vue/cli        # Vue CLI（旧）
```

## 组合式 API（Composition API）

### setup 语法糖

```vue
<script setup>
// Vue 3.2+ 推荐写法，不用 return
import { ref, onMounted } from 'vue'

const count = ref(0)
const msg = 'Hello Vue3'

onMounted(() => {
  console.log('组件挂载了')
})
</script>

<template>
  <p>{{ msg }}</p>
  <button @click="count++">{{ count }}</button>
</template>
```

### ref vs reactive

```js
import { ref, reactive } from 'vue'

// ref：基本类型 + 对象，取值需 .value
const count = ref(0)
count.value++

// reactive：只接受对象，直接访问
const user = reactive({ name: '张三', age: 20 })
user.age++

// 推荐：全用 ref（写法统一，解构不丢失响应）
const name = ref('张三')
const age  = ref(20)
```

## 生命周期

```vue
<script setup>
import { onMounted, onUpdated, onUnmounted } from 'vue'

onMounted(() => {})       // 挂载完成
onUpdated(() => {})       // 更新完成
onUnmounted(() => {})     // 卸载完成
</script>
```

| Vue 2 (选项式) | Vue 3 (组合式) |
| -------------- | -------------- |
| `beforeCreate` / `created` | `setup()` 本身就是 |
| `beforeMount` | — |
| `mounted` | `onMounted` |
| `beforeUpdate` | — |
| `updated` | `onUpdated` |
| `beforeDestroy` | `onBeforeUnmount` |
| `destroyed` | `onUnmounted` |

## 计算属性与侦听器

```vue
<script setup>
import { ref, computed, watch } from 'vue'

const firstName = ref('张')
const lastName  = ref('三')

// 计算属性
const fullName = computed(() => firstName.value + lastName.value)

// 侦听器
watch(firstName, (newVal, oldVal) => {
  console.log(`变了: ${oldVal} → ${newVal}`)
})

// 侦听多个
watch([firstName, lastName], ([new1, new2], [old1, old2]) => {
  console.log('其中一个变了')
})

// 立即执行 + 深度监听
watch(() => user.value, (newVal) => { ... }, { deep: true, immediate: true })
</script>
```

## 组件通信

### 父传子 props

```vue
<!-- 父组件 -->
<Child :title="msg" :count="count" />

<!-- 子组件 -->
<script setup>
defineProps({
  title: String,
  count: { type: Number, default: 0 }
})
</script>
<template>
  <p>{{ title }} - {{ count }}</p>
</template>
```

### 子传父 emit

```vue
<!-- 子组件 -->
<button @click="emit('update', '新数据')">发送</button>
<script setup>
const emit = defineEmits(['update'])
</script>

<!-- 父组件 -->
<Child @update="handleUpdate" />
```

### 插槽 slot

```vue
<!-- 子组件 -->
<div class="card">
  <slot name="header" />
  <slot />
  <slot name="footer" />
</div>

<!-- 父组件 -->
<Card>
  <template #header>标题</template>
  <p>默认内容</p>
  <template #footer>底部</template>
</Card>
```

## 常用指令

| 指令 | 说明 | 示例 |
| ---- | ---- | ---- |
| `v-model` | 双向绑定 | `<input v-model="name" />` |
| `v-if` / `v-else` | 条件渲染 | `<div v-if="show">显示</div>` |
| `v-show` | 显隐切换（display） | `<div v-show="show">显示</div>` |
| `v-for` | 列表渲染 | `<li v-for="item in list" :key="item.id">` |
| `v-on` / `@` | 事件绑定 | `<button @click="handle">` |
| `v-bind` / `:` | 属性绑定 | `<img :src="url" />` |
| `v-html` | 渲染 HTML | `<div v-html="htmlContent" />` |

> **`v-if` vs `v-show`**：`v-if` 真正增删 DOM；`v-show` 仅切换 CSS `display`。频繁切换用 `v-show`

---

**相关笔记**: [[composition-api]] · [[router]] · [[../springboot3/basics]]
