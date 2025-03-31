# Vue状态管理

> 在 Vue 中，全局状态管理是指在多个组件之间共享和管理状态。Vue 提供了两种主要的全局状态管理工具：**Vuex** 和 **Pinia**。这两种工具都可以帮助开发者在复杂的应用中更好地管理状态。

## 准备工作

### 初始化

```powershell
# 初始化
pnpm create vite 项目名称 --template vue-ts
# 下载依赖
pnpm i
# 添加vue-router依赖
pnpm add vue-router@4
# 启动项目
pnpm dev
```

在`src`中新键`view`文件夹，新建`Bar.vue`和`Baz.vue`两个文件

`Bar.vue`

```vue
<template>
  <h1>Bar</h1>
</template>
<script setup lang="ts"></script>
```

`Baz.vue`

```vue
<template>
  <h1>Baz</h1>
</template>
<script setup lang="ts"></script>
```

### 配置路由

在`src`中新键`router`文件夹，新建`routes.ts`和`index.ts`两个文件

在`routes.ts`中导入页面

```ts
import type { RouteRecordRaw } from 'vue-router'
import Bar from '../views/Bar.vue'
import Baz from '../views/Baz.vue'

export const routes: Array<RouteRecordRaw> = [
  {path:'/',redirect:'/bar'},
  { path: '/bar', component: Bar },
  { path: '/baz', component: Baz },
]
```

在`index.ts`中创建路由

```ts
import { createRouter, createWebHistory } from 'vue-router'
import { routes } from './routes'

export default createRouter({ routes, history: createWebHistory() })
```





































## 不使用全局状态管理工具

不使用全局状态管理工具的情况下，也可以进行多个组件之间共享和管理状态







































