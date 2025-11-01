# 矿小航导航站/插件
> README生成：Claud Sonnet 4.5  
> 网站部署：[https://nav.atcumt.com](https://nav.atcumt.com)，使用`nav-website2025`分支代码

[![Netlify Status](https://api.netlify.com/api/v1/badges/d6b9fb9b-3546-43f0-8c95-cd6c0d939da0/deploy-status)](https://app.netlify.com/sites/wondrous-churros-ae57a8/deploys)


## 项目简介

一个基于 Vue 3 的个性化浏览器导航页面，专为中国矿业大学（CUMT）学生设计，同时也可作为通用导航页使用。项目支持：

- ✨ 个性化可拖拽标签网格
- 🔍 多搜索引擎集成（Bing、百度、搜狗、Google、GitHub）
- 🎨 自定义背景壁纸（图片/视频）
- ⚙️ 实时配置调整（圆角、透明度等）
- 💾 本地持久化存储
- 🔌 支持打包为浏览器插件

---

## 项目结构

```
atcumt-main/
├── public/                  # 静态资源
│   └── index.html          # HTML 模板
├── src/
│   ├── assets/             # 资源文件
│   │   ├── background/     # 背景图片/视频
│   │   ├── css/           # 全局样式
│   │   ├── ico/           # 图标文件
│   │   └── svg/           # SVG 图标
│   ├── components/         # Vue 组件
│   │   ├── LoginPage.vue  # 登录页面（未启用）
│   │   ├── Search.vue     # 搜索框组件
│   │   ├── TagsBox.vue    # 标签网格组件
│   │   ├── TimeBox.vue    # 时间显示组件
│   │   └── UserBox.vue    # 用户设置组件
│   ├── router/            # 路由配置
│   │   └── index.js       # 路由定义
│   ├── store/             # Vuex 状态管理
│   │   └── index.js       # 全局状态
│   ├── utils/             # 工具函数
│   │   ├── helpers.js     # 辅助函数
│   │   └── polyfills.js   # 兼容性补丁
│   ├── App.vue            # 根组件
│   └── main.js            # 应用入口
├── scripts/
│   └── build-plugin.mjs   # 插件打包脚本
├── vue.config.js          # Vue CLI 配置
├── jsconfig.json          # JS 编译配置
└── package.json           # 项目配置
```

---


## 快速开始

### 环境要求

- Node.js >= 14.x
- npm >= 6.x 或 yarn >= 1.22

### 安装依赖

```bash
cd atcumt-nav-main
npm install
npm install yarn
```

### 开发模式

```bash
npm run serve
```

访问 http://localhost:8000

### 生产构建

```bash
# 标准 Web 构建
npm run build

# 浏览器插件构建
npm run build:plugin
```

插件文件将输出到 `browserPlugin/` 目录

### 代码规范

```bash
# 检查代码风格
npm run lint

# 自动修复
npm run lint:fix
```

---



## 项目结构

```
atcumt-main/
├── public/                  # 静态资源
│   └── index.html          # HTML 模板
├── src/
│   ├── assets/             # 资源文件
│   │   ├── background/     # 背景图片/视频
│   │   ├── css/           # 全局样式
│   │   ├── ico/           # 图标文件
│   │   └── svg/           # SVG 图标
│   ├── components/         # Vue 组件
│   │   ├── LoginPage.vue  # 登录页面（未启用）
│   │   ├── Search.vue     # 搜索框组件
│   │   ├── TagsBox.vue    # 标签网格组件
│   │   ├── TimeBox.vue    # 时间显示组件
│   │   └── UserBox.vue    # 用户设置组件
│   ├── router/            # 路由配置
│   │   └── index.js       # 路由定义
│   ├── store/             # Vuex 状态管理
│   │   └── index.js       # 全局状态
│   ├── utils/             # 工具函数
│   │   ├── helpers.js     # 辅助函数
│   │   └── polyfills.js   # 兼容性补丁
│   ├── App.vue            # 根组件
│   └── main.js            # 应用入口
├── scripts/
│   └── build-plugin.mjs   # 插件打包脚本
├── vue.config.js          # Vue CLI 配置
├── jsconfig.json          # JS 编译配置
└── package.json           # 项目配置
```

---


## 核心功能

### 1. 搜索功能 (`Search.vue`)

**特性：**
- 支持 5 种搜索引擎切换（Bing、百度、搜狗、Google、GitHub）
- Bing 搜索建议（JSONP 实现）
- 自定义搜索框样式（高度、透明度、圆角）
- 响应式显示/隐藏建议列表

**实现要点：**
```javascript
// 动态注入 JSONP 获取搜索建议
const dataUrl = `https://api.bing.com/qsonhs.aspx?type=cb&q=${c}&cb=window.bing.sug`
window.bing = {
  sug: (json) => {
    if (json.AS.Results !== undefined)
      this.searchSuggestion = json.AS.Results[0].Suggests
  }
}
```

### 2. 可拖拽标签网格 (`TagsBox.vue`)

**特性：**
- 基于 `vue3-drr-grid-layout` 的网格系统
- 12 列自适应布局
- 拖拽重排序
- 自定义图标圆角和透明度
- 自动查找空位添加新标签

**布局算法：**
```javascript
// 12x10 矩阵查找空位
const matrix = Array(12).fill().map(() => Array(10).fill(-1))
tags.forEach(tag => matrix[tag.y][tag.x] = tag.i)
// 从第一行开始查找 -1 位置
```

### 3. 状态持久化 (`store/index.js`)

**存储内容：**
- 标签列表配置
- UI 样式设置（圆角、透明度、尺寸）
- 背景壁纸选择
- 用户自定义数据

**实现机制：**
```javascript
// Vuex 插件自动同步 localStorage
store.subscribe((mutation, state) => {
  localStorage.setItem('vuex-state', JSON.stringify(state))
})
```

### 4. 用户中心 (`UserBox.vue`)

**功能模块：**
- 登录/注册（连接后端 API）
- 新增标签（自动获取网站图标）
- 设置面板（Drawer 侧边栏）
  - 背景切换（图片/视频）
  - 图标样式调整
  - 搜索框样式调整
- 备份与恢复（规划中）

**API 集成：**
```javascript
// 后端服务器：http://1.13.195.5:8080
- POST /users/login         // 用户登录
- POST /users/register      // 用户注册
- POST /emails/send/registerCode  // 发送验证码
- GET /spider/getIcon       // 获取网站图标
```

### 5. 背景系统 (`App.vue`)

**支持格式：**
- 静态图片（8 张预设）
- 动态视频（2 个预设）
- 自定义上传（规划中）

**切换逻辑：**
```javascript
if (back.type === 'pic') {
  video.style.display = 'none'
  body.style.backgroundImage = `url(${back.src})`
} else {
  video.style.display = 'block'
  video.src = back.link
}
```

---

## 开发指南

### 1. 添加新的搜索引擎

**步骤：**

1. 在 `Search.vue` 的 `data()` 中添加引擎配置：

```javascript
searchEngine: [
  // ...existing engines
  {
    name: 'duckduckgo',
    icon: require('@/assets/ico/duckduckgo.ico'),
  }
]
```

2. 在 `searchContent()` 方法中添加跳转逻辑：

```javascript
searchContent() {
  const duckduckgo = 'https://duckduckgo.com/?q='
  // ...
  else if (s === 'duckduckgo')
    url = duckduckgo + c
}
```

### 2. 添加新的 Vuex 模块

**创建模块文件** `store/modules/user.js`：

```javascript
export default {
  namespaced: true,
  state: {
    userInfo: null,
    isLoggedIn: false,
  },
  mutations: {
    SET_USER(state, user) {
      state.userInfo = user
      state.isLoggedIn = true
    },
    LOGOUT(state) {
      state.userInfo = null
      state.isLoggedIn = false
    }
  },
  actions: {
    async login({ commit }, { email, password }) {
      // API 调用逻辑
      const user = await api.login(email, password)
      commit('SET_USER', user)
    }
  }
}
```

**在 `store/index.js` 中注册：**

```javascript
import user from './modules/user'

const store = createStore({
  // ...existing config
  modules: {
    user
  }
})
```

**组件中使用：**

```javascript
import { mapState, mapActions } from 'vuex'

export default {
  computed: {
    ...mapState('user', ['userInfo', 'isLoggedIn'])
  },
  methods: {
    ...mapActions('user', ['login', 'logout'])
  }
}
```

### 3. 创建统一的 HTTP 客户端

**创建** `src/utils/http.js`：

```javascript
import axios from 'axios'

const instance = axios.create({
  baseURL: process.env.VUE_APP_API_BASE || 'http://1.13.195.5:8080',
  timeout: 10000,
  withCredentials: true
})

// 请求拦截器
instance.interceptors.request.use(
  config => {
    const token = localStorage.getItem('token')
    if (token) {
      config.headers.Authorization = `Bearer ${token}`
    }
    return config
  },
  error => Promise.reject(error)
)

// 响应拦截器
instance.interceptors.response.use(
  response => {
    const { code, data, message } = response.data
    if (code === 200) {
      return data
    } else {
      console.error(message)
      return Promise.reject(new Error(message))
    }
  },
  error => {
    console.error('网络错误:', error)
    return Promise.reject(error)
  }
)

export default instance
```

**在 `.env` 文件中配置：**

```bash
# .env.development
VUE_APP_API_BASE=http://localhost:8080

# .env.production
VUE_APP_API_BASE=https://api.yourdomain.com
```

**组件中使用：**

```javascript
import http from '@/utils/http'

export default {
  methods: {
    async fetchData() {
      try {
        const data = await http.get('/users/profile')
        this.userData = data
      } catch (error) {
        this.$message.error('获取数据失败')
      }
    }
  }
}
```

### 4. 添加新的导航标签

**方式一：通过界面添加**  
点击右下角 "+" 按钮，输入网址和名称，系统自动获取图标

**方式二：直接修改 store**  
在 `store/index.js` 的 `tagList` 中添加：

```javascript
tagList: [
  // ...existing tags
  {
    x: 7,        // 水平位置（0-11）
    y: 0,        // 垂直位置
    w: 1,        // 宽度（网格单位）
    h: 3,        // 高度（网格单位）
    i: 5,        // 唯一标识
    name: 'Stack Overflow',
    url: 'https://stackoverflow.com',
    icon: require('@/assets/ico/stackoverflow.png')
  }
]
```

### 5. 自定义背景壁纸

**添加静态图片：**

1. 将图片放到 `src/assets/background/`
2. 在 `store/index.js` 中引入：

```javascript
import myBackground from '@/assets/background/my-bg.jpg'

backgroundPictures: [
  // ...existing
  {
    src: myBackground,
    key: 10,
    type: 'pic'
  }
]
```

**添加视频背景：**

```javascript
import myVideo from '@/assets/background/my-video.mp4'
import myVideoPoster from '@/assets/background/my-video-poster.jpg'

backgroundPictures: [
  // ...existing
  {
    link: myVideo,
    src: myVideoPoster,  // 预览图
    key: 11,
    type: 'video'
  }
]
```

### 6. 添加新路由页面

**创建页面组件** `src/views/About.vue`：

```vue
<script>
export default {
  name: 'AboutPage'
}
</script>

<template>
  <div class="about-page">
    <h1>关于矿小航</h1>
    <p>这是一个导航站项目</p>
  </div>
</template>

<style scoped>
.about-page {
  padding: 20px;
}
</style>
```

**在 `router/index.js` 中注册：**

```javascript
import About from '@/views/About.vue'

const routes = [
  // ...existing routes
  {
    name: 'about',
    path: '/about',
    component: About
  }
]
```

**导航跳转：**

```vue
<!-- 声明式 -->
<router-link to="/about">
关于
</router-link>

<!-- 编程式 -->
<script>
export default {
  methods: {
    goToAbout() {
      this.$router.push('/about')
    }
  }
}
</script>
```

### 7. 配置代理解决跨域

在 `vue.config.js` 中已配置代理：

```javascript
devServer: {
  proxy: {
    '/api': {
      target: 'http://1.13.195.5:8080',
      changeOrigin: true,
      pathRewrite: {
        '^/api': ''
      }
    }
  }
}
```

**使用方式：**

```javascript
// 原始请求：http://1.13.195.5:8080/users/login
// 改为：/api/users/login（开发环境自动代理）

await http.post('/api/users/login', { email, password })
```

### 8. 打包为浏览器插件

**1. 创建 manifest.json**（Chrome/Edge）：

```json
{
  "manifest_version": 3,
  "name": "矿小航导航",
  "version": "1.0.0",
  "description": "个性化浏览器导航页",
  "chrome_url_overrides": {
    "newtab": "index.html"
  },
  "permissions": ["storage"],
  "icons": {
    "16": "favicon.ico",
    "48": "favicon.ico",
    "128": "favicon.ico"
  }
}
```

**2. 执行构建：**

```bash
npm run build:plugin
```

**3. 加载插件：**
- 打开 Chrome -> 扩展程序 -> 开启开发者模式
- 点击"加载已解压的扩展程序"
- 选择 `browserPlugin/` 目录

---

## 深度分析

### 架构优势

✅ **模块化设计**：组件职责清晰，易于维护  
✅ **响应式状态管理**：Vuex 集中管理应用状态  
✅ **持久化方案**：localStorage 自动同步  
✅ **插件化支持**：可打包为浏览器扩展  
✅ **多搜索引擎**：提供灵活的搜索体验

### 存在的问题

⚠️ **直接 DOM 操作**  
- 大量使用 `document.querySelector` 和 `style.*` 直接操作
- 降低了组件的可测试性和复用性
- **建议**：改用 Vue 的 `ref`、`computed` 和样式绑定

⚠️ **违反 Vuex 最佳实践**  
```javascript
// ❌ 不推荐：直接修改 state
this.$store.state.vis = false

// ✅ 推荐：通过 mutation
this.$store.commit('setVis', false)
```

⚠️ **硬编码的后端地址**  
- API 地址写死在组件中：`http://1.13.195.5:8080`
- **建议**：使用环境变量 + axios 实例统一管理

⚠️ **缺少错误处理**  
- fetch 请求缺少完善的错误捕获
- JSONP 注入未检查脚本加载失败

⚠️ **无类型约束**  
- 纯 JavaScript 项目，缺少 TypeScript 类型保护
- 易出现运行时错误

⚠️ **缺少单元测试**  
- 没有测试用例
- 重构风险高

### 安全考虑

🔒 **JSONP 风险**：动态注入外部脚本，需信任数据源  
🔒 **localStorage 敏感数据**：避免存储 token 等敏感信息  
🔒 **CSP 策略**：如作为浏览器插件需配置内容安全策略  
🔒 **XSS 防护**：用户输入的 URL 需要验证和过滤

---


## 改进建议

### 高优先级（推荐立即实施）

#### 1. 统一 API 管理 ⭐⭐⭐

**问题**：API 地址分散在各组件中，难以维护

**解决方案**：
```javascript
// src/api/index.js
import http from '@/utils/http'

export const userAPI = {
  login: (data) => http.post('/users/login', data),
  register: (data, code) => http.post(`/users/register?code=${code}`, data),
  sendCode: (email) => http.post(`/emails/send/registerCode?email=${email}`)
}

export const iconAPI = {
  getIcon: (name) => http.get(`/spider/getIcon?name=${name}`)
}
```

**收益**：集中管理、易于测试、便于切换环境

#### 2. 规范 Vuex 使用 ⭐⭐⭐

**问题**：存在直接修改 `state` 的情况

**解决方案**：
```javascript
// ❌ 错误写法
this.$store.state.vis = false
this.$store.state.tagRadius = this.tagRadius

// ✅ 正确写法
this.$store.commit('setVis', false)
this.$store.commit('updateTagRadius', this.tagRadius)
```

添加缺失的 mutations：
```javascript
mutations: {
  setVis(state, value) {
    state.vis = value
  }
}
```

#### 3. 移除直接 DOM 操作 ⭐⭐

**问题**：降低组件可维护性

**解决方案**：
```vue
<!-- ❌ 不推荐 -->
export default {
  mounted() {
    const searchBox = document.querySelector('.searchBox')
    searchBox.style.height = '40px'
  }
}

<!-- ✅ 推荐 -->
<template>
  <div class="searchBox" :style="boxStyle" />
</template>

export default {
  computed: {
    boxStyle() {
      return {
        height: this.$store.state.searchBoxSize + 'px',
        opacity: this.$store.state.searchBoxOpacity / 100
      }
    }
  }
}
```

### 中优先级（建议实施）

#### 4. 添加 TypeScript 支持 ⭐⭐

**步骤：**
```bash
vue add typescript
```

**改造示例：**
```typescript
// store/types.ts
export interface Tag {
  x: number
  y: number
  w: number
  h: number
  i: number
  name: string
  url: string
  icon: string
}

export interface RootState {
  tagList: Tag[]
  tagRadius: number
  searchBoxSize: number
}
```

#### 5. 增加单元测试 ⭐⭐

**安装依赖：**
```bash
npm install -D @vue/test-utils vitest jsdom
```

**示例测试：**
```javascript
// tests/components/TimeBox.spec.js
import { mount } from '@vue/test-utils'
import TimeBox from '@/components/TimeBox.vue'

describe('TimeBox', () => {
  it('显示当前时间', () => {
    const wrapper = mount(TimeBox)
    expect(wrapper.find('.timeBox').exists()).toBe(true)
  })
})
```

#### 6. 添加 CI/CD 流程 ⭐⭐

**GitHub Actions 示例** (`.github/workflows/ci.yml`)：
```yaml
name: CI

on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-node@v3
        with:
          node-version: 16
      - run: npm ci
      - run: npm run lint
      - run: npm run build
```

### 低优先级（可选）

#### 7. 国际化支持

使用 `vue-i18n` 支持多语言

#### 8. 主题切换

增加暗黑模式/明亮模式切换

#### 9. PWA 支持

添加 Service Worker，支持离线访问

---
