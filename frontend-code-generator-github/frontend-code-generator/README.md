# Frontend Code Generator Skill

CodeBuddy Skill，用于规范 Vue3 + TypeScript + SCSS 前端项目开发。

## 功能特性

- **技术栈规范**：Vue 3 + TypeScript + SCSS，组件化开发
- **样式管理**：所有组件样式单独抽出，分散管理
- **Figma 设计稿还原**：先规划布局确认后再开发，背景优先使用 CSS
- **响应式布局**：PC 设计稿自动适配移动端
- **数据可配置性**：支持 JSON 和 Mock 两种数据源，用户可通过配置切换
- **接口文档先行**：一个页面对应一个接口文档，用户确认后再生成 Mock

## 安装

### 方式一：直接下载

1. 下载本仓库的 ZIP 文件
2. 解压到 CodeBuddy 的 skills 目录：

**Windows:**
```
%USERPROFILE%\.codebuddy\skills\frontend-code-generator\
```

**Mac/Linux:**
```
~/.codebuddy/skills/frontend-code-generator/
```

### 方式二：Git 克隆

```bash
# 克隆仓库
git clone https://github.com/yourname/frontend-code-generator-skill.git

# 复制到 CodeBuddy skills 目录（Windows）
copy frontend-code-generator-skill\SKILL.md %USERPROFILE%\.codebuddy\skills\frontend-code-generator\
copy frontend-code-generator-skill\references %USERPROFILE%\.codebuddy\skills\frontend-code-generator\

# 或 Mac/Linux
cp -r frontend-code-generator-skill/* ~/.codebuddy/skills/frontend-code-generator/
```

## 使用方法

安装完成后，在 CodeBuddy 中直接描述你的前端开发需求即可自动触发 Skill。

### 示例

**你输入：**
> 帮我根据这个 Figma 设计稿开发一个行业案例页面

**AI 会自动：**
1. 识别设计稿类型（PC/移动端）
2. 规划整体布局 → 让你确认
3. 生成接口文档 → 让你确认入参和接口拆分
4. 根据确认的文档生成 Mock 接口 + JSON 数据
5. 开发 Vue3 + TS + SCSS 页面
6. 样式单独抽出到各组件目录

### 主动触发

如果想确保 Skill 被使用，可以在对话开头加上：
```
@skill://frontend-code-generator 帮我开发一个首页...
```

## 规范详情

### 技术栈
- **框架**: Vue 3 (Composition API)
- **语言**: TypeScript
- **样式**: SCSS
- **开发模式**: 组件化开发

### 目录结构

```
src/
├── components/              # 全局组件
│   └── ComponentName/
│       ├── index.vue
│       └── index.scss       # 样式单独抽出
├── views/                   # 页面
│   └── pageName/
│       ├── index.vue
│       ├── index.scss       # 页面样式单独抽出
│       └── components/      # 页面级组件
│           └── ComponentName/
│               ├── index.vue
│               └── index.scss
├── data/                    # 本地 JSON 数据源
├── mock/                    # Mock 接口
└── types/                   # TypeScript 类型定义
```

### 响应式规则

| 设计稿类型 | 响应式要求 |
|-----------|-----------|
| PC 设计稿 | **必须响应式**，适配移动端 |
| 移动端设计稿 | 可选，默认不强制适配 PC |
| 双端设计稿 | **必须响应式**，按设计稿实现 |

### 数据配置

1. **先生成接口文档**（一个页面一个文档）
2. **用户确认**接口入参、返回值、接口拆分
3. **根据文档生成** Mock 接口和 JSON 数据
4. 用户可通过配置切换 JSON 或 Mock 数据源

## 开发流程

```
1. 分析需求/设计稿
   ↓
2. 【用户确认】规划整体布局
   ↓
3. 生成接口文档（一个页面一个文档）
   ↓
4. 【用户确认】接口入参、接口拆分
   ↓
5. 生成 Mock 接口 + JSON 数据（根据已确认文档）
   ↓
6. 开发页面组件
   ↓
7. 完成
```

## 文件说明

- `SKILL.md` - Skill 核心配置文件
- `references/coding-standards.md` - 详细开发规范
- `references/api-doc-template.md` - 接口文档模板

## 贡献

欢迎提交 Issue 和 Pull Request 来完善这个 Skill。

## License

MIT License
