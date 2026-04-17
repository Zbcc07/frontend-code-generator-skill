# 前端代码开发详细规范

## 1. 代码风格规范

### 1.1 Vue 3 + TypeScript 规范

#### 组件结构
```vue
<template>
  <!-- 模板代码 -->
</template>

<script setup lang="ts">
// 1. 类型导入
import type { PropType } from 'vue'

// 2. Vue API 导入
import { ref, computed, onMounted } from 'vue'

// 3. 组件导入
import ChildComponent from './components/ChildComponent.vue'

// 4. 工具/类型导入
import type { PageConfig } from '@/types'
import { fetchData } from '@/api'

// 5. Props 定义
interface Props {
  title: string
  config?: PageConfig
}

const props = withDefaults(defineProps<Props>(), {
  config: () => ({
    hero: { title: '', subtitle: '' },
    items: []
  })
})

// 6. Emits 定义
const emit = defineEmits<{
  (e: 'update', value: string): void
  (e: 'submit', data: unknown): void
}>()

// 7. 响应式数据
const loading = ref(false)
const dataList = ref<any[]>([])

// 8. 计算属性
const filteredList = computed(() => {
  return dataList.value.filter(item => item.visible)
})

// 9. 方法定义
const handleClick = (item: any) => {
  emit('update', item.id)
}

// 10. 生命周期
onMounted(() => {
  loadData()
})

const loadData = async () => {
  loading.value = true
  try {
    const res = await fetchData()
    dataList.value = res.data
  } finally {
    loading.value = false
  }
}
</script>

<style scoped lang="scss">
/* SCSS 样式 */
</style>
```

#### 命名规范
| 类型 | 规范 | 示例 |
|------|------|------|
| 组件名 | PascalCase | `UserProfile.vue` |
| 变量/函数 | camelCase | `userName`, `getUserInfo()` |
| 常量 | SCREAMING_SNAKE_CASE | `API_BASE_URL` |
| CSS Class | kebab-case | `user-card`, `btn-primary` |
| 接口/类型 | PascalCase + 后缀 | `UserInfo`, `UserConfigType` |
| 文件名 | camelCase | `userService.ts` |

### 1.2 SCSS 规范

#### 文件组织
```scss
// 变量定义（variables.scss）
$primary-color: #1890ff;
$text-color: #333333;
$border-radius: 4px;

// 混合宏（mixins.scss）
@mixin flex-center {
  display: flex;
  align-items: center;
  justify-content: center;
}

@mixin respond-to($breakpoint) {
  @if $breakpoint == 'mobile' {
    @media (max-width: 768px) { @content; }
  }
  @if $breakpoint == 'tablet' {
    @media (max-width: 1024px) { @content; }
  }
}

// 组件样式（component.scss）
.user-card {
  // 1. 布局属性
  display: flex;
  flex-direction: column;
  
  // 2. 盒模型属性
  padding: 16px;
  margin-bottom: 12px;
  border-radius: $border-radius;
  
  // 3. 视觉属性
  background-color: #ffffff;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
  
  // 4. 其他属性
  transition: all 0.3s ease;
  
  // 嵌套选择器
  &:hover {
    box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15);
  }
  
  // 子元素
  .card-title {
    font-size: 16px;
    font-weight: 600;
    color: $text-color;
    margin-bottom: 8px;
  }
  
  // 响应式
  @include respond-to('mobile') {
    padding: 12px;
  }
}
```

#### BEM 命名规范
```scss
// Block - 块
.hero-section { }

// Element - 元素
.hero-section__title { }
.hero-section__subtitle { }

// Modifier - 修饰符
.hero-section--dark { }
.btn--primary { }
.btn--large { }
```

## 2. 文件组织详细规范

### 2.1 完整目录结构

```
my-project/
├── public/                          # 静态资源（不经过构建）
│   ├── images/
│   │   ├── home/                   # 首页图片
│   │   │   ├── banner-bg.jpg
│   │   │   └── feature-icon.png
│   │   ├── about/                  # 关于页图片
│   │   ├── cases/                  # 案例页图片
│   │   └── shared/                 # 通用图片
│   │       ├── logo.png
│   │       └── avatar-default.png
│   └── css/
│       └── global.scss             # 全局样式
│
├── src/
│   ├── api/                        # 接口定义
│   │   ├── index.ts
│   │   └── modules/
│   │       ├── home.ts
│   │       └── cases.ts
│   │
│   ├── components/                 # 全局公共组件
│   │   ├── common/                 # 通用组件
│   │   │   ├── AppButton/
│   │   │   │   ├── index.vue
│   │   │   │   └── index.scss      # 组件样式单独抽出
│   │   │   └── AppCard/
│   │   │       ├── index.vue
│   │   │       └── index.scss
│   │   └── layout/                 # 布局组件
│   │       ├── AppHeader/
│   │       │   ├── index.vue
│   │       │   └── index.scss
│   │       └── AppFooter/
│   │           ├── index.vue
│   │           └── index.scss
│   │
│   ├── views/                      # 页面视图
│   │   ├── home/                   # 首页模块
│   │   │   ├── index.vue           # 页面入口
│   │   │   ├── index.scss          # 页面样式单独抽出
│   │   │   ├── components/         # 页面级组件
│   │   │   │   ├── HeroSection/
│   │   │   │   │   ├── index.vue
│   │   │   │   │   └── index.scss  # 组件样式单独抽出
│   │   │   │   └── FeatureList/
│   │   │   │       ├── index.vue
│   │   │   │       └── index.scss
│   │   │   └── types.ts            # 页面专属类型
│   │   │
│   │   ├── cases/                  # 案例页模块
│   │   │   ├── index.vue
│   │   │   ├── index.scss          # 页面样式单独抽出
│   │   │   ├── components/
│   │   │   │   └── ComponentName/
│   │   │   │       ├── index.vue
│   │   │   │       └── index.scss
│   │   │   └── types.ts
│   │   │
│   │   └── about/
│   │       ├── index.vue
│   │       ├── index.scss          # 页面样式单独抽出
│   │       └── components/
│   │           └── ComponentName/
│   │               ├── index.vue
│   │               └── index.scss
│   │
│   ├── composables/                # 组合式函数
│   │   ├── useFetch.ts
│   │   └── usePagination.ts
│   │
│   ├── data/                       # 本地 JSON 数据源
│   │   ├── home.json
│   │   ├── cases.json
│   │   └── about.json
│   │
│   ├── mock/                       # Mock 接口
│   │   ├── index.ts
│   │   └── modules/
│   │       ├── home.ts
│   │       └── cases.ts
│   │
│   ├── types/                      # 全局类型定义
│   │   ├── index.ts
│   │   ├── api.ts                  # 接口相关类型
│   │   └── components.ts           # 组件相关类型
│   │
│   ├── utils/                      # 工具函数
│   │   ├── index.ts
│   │   ├── request.ts              # 请求封装
│   │   └── helpers.ts
│   │
│   ├── styles/                     # 样式文件
│   │   ├── variables.scss          # SCSS 变量
│   │   ├── mixins.scss             # SCSS 混合宏
│   │   └── global.scss
│   │
│   ├── App.vue
│   ├── main.ts
│   └── router/
│       └── index.ts
│
├── docs/                           # 接口文档
│   ├── home-api.md
│   └── cases-api.md
│
├── index.html
├── package.json
├── tsconfig.json
└── vite.config.ts
```

### 2.2 文件存放规则详解

#### 图片资源规则
```
public/images/
├── {module-name}/          # 按功能模块分文件夹
│   └── {descriptive-name}.{ext}
└── shared/                 # 通用图片
    └── {descriptive-name}.{ext}

示例：
public/images/
├── home/
│   ├── hero-banner.jpg
│   ├── feature-1.png
│   └── feature-2.png
├── cases/
│   ├── case-mobile-mall.png
│   └── case-big-screen.png
└── shared/
    ├── logo.png
    └── icons/
        ├── arrow-right.svg
        └── search.svg
```

#### 样式文件规则

所有组件和页面的样式必须**单独抽出**，分散到各组件目录中管理。

**页面样式结构：**
```
src/views/{module-name}/
├── index.vue               # 页面组件
├── index.scss              # 页面样式单独抽出
├── components/             # 页面级子组件
│   └── ComponentName/
│       ├── index.vue
│       └── index.scss      # 组件样式单独抽出

// index.vue 中引入样式
<script setup lang="ts">
// ...
</script>

<template>
  <!-- ... -->
</template>

<style scoped lang="scss">
@import './index.scss';
</style>
```

**全局组件样式结构：**
```
src/components/
├── common/
│   └── AppButton/
│       ├── index.vue
│       └── index.scss      # 组件样式单独抽出
└── layout/
    └── AppHeader/
        ├── index.vue
        └── index.scss      # 组件样式单独抽出
```

**样式引入方式：**
```vue
<!-- 组件文件 index.vue -->
<script setup lang="ts">
// 组件逻辑
</script>

<template>
  <!-- 组件模板 -->
</template>

<style scoped lang="scss">
@import './index.scss';
</style>
```
```

## 3. Figma 设计稿还原规范

### 3.1 开发流程

```
1. 分析设计稿
   └── 提取颜色、字体、间距、布局等设计 token
   
2. 规划布局结构
   └── 输出：布局草图/结构说明
   └── 与用户确认布局合理性
   
3. 确认后开发
   └── 按确认的布局进行详细开发
   └── 严格还原设计稿细节
```

### 3.2 背景实现规范

#### ✅ 正确做法
```vue
<template>
  <section class="hero-section">
    <div class="hero-content">
      <h1>{{ title }}</h1>
    </div>
  </section>
</template>

<style scoped lang="scss">
.hero-section {
  // 使用 background 实现背景
  background-image: url('/images/home/hero-bg.jpg');
  background-size: cover;
  background-position: center;
  background-repeat: no-repeat;
  min-height: 600px;
}
</style>
```

#### ❌ 错误做法
```vue
<template>
  <section class="hero-section">
    <!-- 不要用 img 做背景 -->
    <img src="/images/home/hero-bg.jpg" class="bg-image" />
    <div class="hero-content">
      <h1>{{ title }}</h1>
    </div>
  </section>
</template>
```

### 3.3 响应式布局规范

#### 响应式策略

根据**设计稿类型**决定响应式策略：

| 设计稿类型 | 响应式要求 | 实现方式 |
|-----------|-----------|---------|
| **PC 设计稿** | **必须响应式** | 使用 Media Query 向下适配移动端 |
| **移动端设计稿** | **可选** | 默认不强制适配 PC，如需适配需明确说明 |
| **双端设计稿** | **必须响应式** | 按设计稿断点完整实现 |

**开发前确认清单：**
- [ ] 确认设计稿类型（PC / 移动端 / 双端）
- [ ] PC 设计稿：默认采用响应式，适配到移动端
- [ ] 移动端设计稿：询问用户是否需要适配 PC 端
- [ ] 记录响应式需求，避免遗漏

#### 断点定义
```scss
// styles/mixins.scss
$breakpoints: (
  'xs': 480px,    // 超小屏幕
  'sm': 768px,    // 平板
  'md': 1024px,   // 小桌面
  'lg': 1280px,   // 桌面
  'xl': 1440px    // 大桌面
);

@mixin respond-to($breakpoint) {
  $value: map-get($breakpoints, $breakpoint);
  @if $value {
    @media (max-width: $value) {
      @content;
    }
  }
}
```

#### 使用示例
```scss
.card-grid {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: 24px;
  
  @include respond-to('lg') {
    grid-template-columns: repeat(3, 1fr);
    gap: 20px;
  }
  
  @include respond-to('md') {
    grid-template-columns: repeat(2, 1fr);
    gap: 16px;
  }
  
  @include respond-to('sm') {
    grid-template-columns: 1fr;
    gap: 12px;
  }
}
```

## 4. 数据可配置性规范

### 4.1 生成顺序（重要）

**必须严格按以下顺序执行，每步需用户确认后再进行下一步：**

```
步骤1: 生成接口文档（一个页面一个文档）
   ↓
步骤2: 【用户确认】接口入参、返回值结构、接口拆分数量
   ↓
步骤3: 根据已确认的接口文档生成 Mock 接口和 JSON 数据
   ↓
步骤4: 开发页面组件
```

**关键原则：**
- **接口文档是标准**，Mock 接口必须根据文档实现
- **Mock 数据结构必须与接口文档保持一致**
- 用户确认接口文档后，**不允许随意修改接口结构**

### 4.2 数据渲染方式

每个页面需支持**两种数据渲染方式**，**用户可通过配置选择使用 JSON 或 Mock 的数据源**，通过修改配置文件或环境变量切换数据源，无需修改业务代码。

#### 方式一：本地 JSON 数据
```typescript
// src/data/cases.json
// 数据结构必须与接口文档定义的返回值一致
{
  "hero": {
    "title": "行业应用案例",
    "subtitle": "探索各行业领先企业如何使用 LowCode Pro 驱动业务创新"
  },
  "cases": [
    {
      "id": 1,
      "title": "交易移动商城",
      "description": "支持投标人/供应商的移动端操作"
    }
  ]
}

// src/views/cases/index.vue
<script setup lang="ts">
import casesData from '@/data/cases.json'

const pageData = ref(casesData)
</script>
```

#### 方式二：Mock 接口
```typescript
// src/mock/modules/cases.ts
// Mock 实现必须与接口文档定义保持一致
import type { MockMethod } from 'vite-plugin-mock'

export default [
  {
    url: '/api/cases',           // 与文档一致
    method: 'get',               // 与文档一致
    response: () => {
      return {
        code: 0,                 // 统一响应格式
        message: 'ok',
        data: {                  // 数据结构必须与文档一致
          hero: { title: '行业应用案例', subtitle: '...' },
          cases: [/* ... */]
        }
      }
    }
  }
] as MockMethod[]
```

### 4.3 接口文档与 Mock 的关系

| 项目 | 接口文档 | Mock 接口 |
|------|----------|-----------|
| **角色** | 标准定义 | 标准实现 |
| **生成顺序** | 先生成 | 后生成 |
| **内容** | 定义接口地址、参数、返回值结构 | 按文档模拟数据 |
| **变更** | 需用户确认 | 随文档变更 |

**示例流程：**

1. **生成接口文档** `docs/cases-api.md`：
   ```markdown
   ### 1. 获取案例列表
   **接口地址**: `GET /api/cases`
   **请求参数**: keyword, category, pageNo, pageSize
   **返回值**: { hero, cases, pagination }
   ```

2. **用户确认**：确认入参和返回值结构无误

3. **生成 Mock 接口** `src/mock/modules/cases.ts`：
   ```typescript
   // 严格按照文档实现
   url: '/api/cases',  // 与文档一致
   method: 'get',      // 与文档一致
   // 返回数据结构与文档定义一致
   ```

### 4.2 配置与视图分离

数据配置与视图分离，样式也与组件分离，便于独立维护。

```vue
<!-- index.vue -->
<template>
  <div class="cases-page">
    <HeroSection :config="pageData.hero" />
    <CaseList :items="pageData.cases" />
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted } from 'vue'
import HeroSection from './components/HeroSection/index.vue'
import CaseList from './components/CaseList/index.vue'
import { getCasesData } from '@/api/modules/cases'
import type { CasesPageData } from './types'

// 配置数据
const pageData = ref<CasesPageData>({
  hero: { title: '', subtitle: '' },
  cases: []
})

// 加载数据
onMounted(async () => {
  const res = await getCasesData()
  if (res.code === 0) {
    pageData.value = res.data
  }
})
</script>

<style scoped lang="scss">
@import './index.scss';
</style>
```

```scss
// index.scss - 页面样式单独抽出
.cases-page {
  padding: 20px;
  max-width: 1200px;
  margin: 0 auto;
}
```

### 4.3 类型定义

```typescript
// src/views/cases/types.ts
export interface HeroConfig {
  title: string
  subtitle: string
}

export interface CaseItem {
  id: number
  title: string
  typeTag: string
  category: string
  description: string
  image: string
}

export interface CasesPageData {
  hero: HeroConfig
  cases: CaseItem[]
}

// API 响应类型
export interface ApiResponse<T> {
  code: number
  message: string
  data: T
}
```

## 5. 组件可配置性规范

### 5.1 Props 设计原则

```vue
<script setup lang="ts">
interface Props {
  // 1. 必填项
  title: string
  
  // 2. 可选项（带默认值）
  size?: 'small' | 'medium' | 'large'
  disabled?: boolean
  
  // 3. 复杂配置对象
  config?: {
    theme: 'light' | 'dark'
    showBorder: boolean
    maxWidth?: number
  }
}

const props = withDefaults(defineProps<Props>(), {
  size: 'medium',
  disabled: false,
  config: () => ({
    theme: 'light',
    showBorder: true
  })
})
</script>
```

### 5.2 组件示例

组件采用**文件夹形式组织**，样式单独抽出。

```
src/views/home/components/HeroSection/
├── index.vue
└── index.scss
```

```vue
<!-- index.vue -->
<template>
  <section 
    class="hero-section" 
    :class="[`hero-section--${config.theme}`]"
    :style="sectionStyle"
  >
    <div class="hero-section__container">
      <h1 class="hero-section__title">{{ config.title }}</h1>
      <p v-if="config.subtitle" class="hero-section__subtitle">
        {{ config.subtitle }}
      </p>
      <slot name="extra" />
    </div>
  </section>
</template>

<script setup lang="ts">
import { computed } from 'vue'

interface Props {
  config: {
    title: string
    subtitle?: string
    theme?: 'light' | 'dark'
    backgroundImage?: string
    maxWidth?: number
    paddingY?: number
  }
}

const props = defineProps<Props>()

const sectionStyle = computed(() => ({
  backgroundImage: props.config.backgroundImage 
    ? `url(${props.config.backgroundImage})` 
    : undefined,
  '--max-width': props.config.maxWidth 
    ? `${props.config.maxWidth}px` 
    : '1200px',
  '--padding-y': props.config.paddingY 
    ? `${props.config.paddingY}px` 
    : '80px'
}))
</script>

<style scoped lang="scss">
@import './index.scss';
</style>
```

```scss
// index.scss - 组件样式单独抽出
.hero-section {
  background-size: cover;
  background-position: center;
  padding: var(--padding-y) 20px;
  
  &__container {
    max-width: var(--max-width);
    margin: 0 auto;
  }
  
  &__title {
    font-size: 48px;
    font-weight: 600;
    margin-bottom: 16px;
  }
  
  &__subtitle {
    font-size: 18px;
    opacity: 0.8;
  }
  
  &--dark {
    color: #ffffff;
    background-color: #1a1a1a;
  }
  
  &--light {
    color: #333333;
    background-color: #ffffff;
  }
}
</style>
```

## 6. 接口文档规范

### 6.1 文档生成原则

**一个页面对应一个接口文档**，文档内具体拆分几个接口需要**与用户确认**。

**生成流程：**
```
1. 分析页面功能需求
   ↓
2. 规划需要几个接口（建议与用户确认拆分数量）
   ↓
3. 生成接口文档初稿
   ↓
4. 【用户确认】接口入参、返回值、字段定义
   ↓
5. 确认后锁定文档，生成 Mock 和页面代码
```

**确认要点：**
- [ ] 接口拆分是否合理（如：一个页面需要 1 个还是多个接口）
- [ ] 请求参数是否完整（分页、筛选、搜索等）
- [ ] 返回值结构是否满足页面渲染需求
- [ ] 字段命名是否规范

### 6.2 文档结构

```markdown
# {页面名称} 接口文档

**Controller**: `{controller-name}`
**API 基础路径**: `/api`
**作者**: {author}
**版本**: {version}
**说明**: {description}

---

## 统一响应格式

所有接口统一返回如下 JSON 结构：

\`\`\`json
{
  "code": 0,
  "message": "ok",
  "data": {}
}
\`\`\`

| 字段 | 类型 | 说明 |
|------|------|------|
| `code` | int | 状态码，`0` 表示成功 |
| `message` | String | 状态描述 |
| `data` | Object/Array/null | 业务数据 |

### 常见错误码

| code | message | 说明 |
|------|---------|------|
| `0` | ok | 操作成功 |
| `400` | 参数校验失败 | 请求参数不合法 |
| `404` | 资源不存在 | 目标资源未找到 |
| `500` | 服务器内部错误 | 后端异常 |

---

## 接口列表

### 1. {接口名称}

**接口地址**: `{METHOD} {path}`

**请求方式**: `{METHOD}`

**功能说明**: {description}

#### 请求参数

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| `param1` | String | 是 | 参数说明 |
| `param2` | Number | 否 | 参数说明 |

#### 返回值

\`\`\`json
{
  "code": 0,
  "message": "ok",
  "data": { }
}
\`\`\`

#### 字段说明

**{对象名} 对象字段**

| 字段 | 类型 | 说明 |
|------|------|------|
| `field1` | String | 字段说明 |
| `field2` | Number | 字段说明 |

#### 请求示例

\`\`\`javascript
import axios from 'axios'

const { data: res } = await axios.get('/api/xxx', {
  params: { param1: 'value' }
})

if (res.code === 0) {
  console.log(res.data)
}
\`\`\`

---

## 数据表结构设计

### {表名}

| 字段名 | 类型 | 说明 |
|--------|------|------|
| id | BIGINT | 主键 |
| name | VARCHAR(100) | 名称 |

---

## Mock 数据说明

当前前端使用 Mock 实现，位于：

```
src/mock/modules/{module}.ts
```

### 后端对接指南

对接真实后端时，只需将 Mock 函数替换为真实 `axios` 请求。

---

## 注意事项

1. 注意事项 1
2. 注意事项 2

---

## 更新日志

| 版本 | 日期 | 说明 |
|------|------|------|
| 1.0 | YYYY-MM-DD | 初始版本 |
```

### 6.3 文档存放位置

```
docs/                    # 项目根目录下的 docs 文件夹
├── home-api.md
├── cases-api.md
└── about-api.md
```

或

```
src/views/{module}/
├── index.vue
├── api-doc.md          # 页面级接口文档
└── ...
```
