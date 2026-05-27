---
created: 2026-05-27
tags:
  - java/internship
  - vue3
related:
  - "[[basics]]"
  - "[[../springboot3/basics]]"
---

# Vue Router 4

## 安装

```bash
npm install vue-router@4
```

## 基本配置

```js
// router/index.js
import { createRouter, createWebHistory } from 'vue-router'

const routes = [
  { path: '/',          component: () => import('../views/Home.vue') },
  { path: '/about',     component: () => import('../views/About.vue') },
  { path: '/users/:id', component: () => import('../views/UserDetail.vue') },
]

const router = createRouter({
  history: createWebHistory(),    // HTML5 模式（去掉 #）
  routes,
})

export default router
```

```js
// main.js
import { createApp } from 'vue'
import App from './App.vue'
import router from './router'

createApp(App).use(router).mount('#app')
```

## 路由跳转

```vue
<!-- 声明式 -->
<router-link to="/">首页</router-link>
<router-link :to="`/users/${id}`">详情</router-link>

<!-- 编程式 -->
<script setup>
import { useRouter } from 'vue-router'
const router = useRouter()

function go() {
  router.push('/about')
  router.push({ path: '/users', query: { page: 1 } })
  router.replace('/login')       // 替换当前记录（不能回退）
  router.go(-1)                  // 后退
}
</script>
```

## 路由参数

```vue
<script setup>
import { useRoute } from 'vue-router'

const route = useRoute()
console.log(route.params.id)     // /users/123 → 123
console.log(route.query.page)    // /users?page=1 → 1
</script>
```

## 导航守卫

```js
// router/index.js
router.beforeEach((to, from, next) => {
  const token = localStorage.getItem('token')
  if (to.path === '/admin' && !token) {
    next('/login')      // 未登录，跳转登录页
  } else {
    next()              // 放行
  }
})

router.afterEach((to) => {
  document.title = to.meta.title || '默认标题'
})
```

---

**相关笔记**: [[basics]] · [[composition-api]]
