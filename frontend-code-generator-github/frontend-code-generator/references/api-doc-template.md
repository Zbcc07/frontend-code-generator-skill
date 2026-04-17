# {页面名称} 接口文档

**Controller**: `{controller-name}`
**API 基础路径**: `/api`
**作者**: system
**版本**: 1.0
**说明**: {页面功能说明}

---

## 统一响应格式

所有接口统一返回如下 JSON 结构：

```json
{
  "code": 0,
  "message": "ok",
  "data": {}
}
```

| 字段 | 类型 | 说明 |
|------|------|------|
| `code` | int | 状态码，`0` 表示成功，其余为业务异常 |
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

### 1. 获取{页面名称}数据

**接口地址**: `GET /api/{endpoint}`

**请求方式**: `GET`

**功能说明**: 获取{页面名称}页面全量数据，包含页面配置和数据列表。

#### 请求参数

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| `keyword` | String | 否 | 搜索关键词，不传或空字符串则不过滤 |
| `category` | String | 否 | 分类筛选，不传或传 `all` 返回全部 |
| `pageNo` | Number | 否 | 页码，从 1 开始，默认 1 |
| `pageSize` | Number | 否 | 每页条数，默认 10 |

#### 返回值

```json
{
  "code": 0,
  "message": "ok",
  "data": {
    "hero": {
      "title": "页面主标题",
      "subtitle": "页面副标题描述"
    },
    "items": [
      {
        "id": 1,
        "title": "项目标题",
        "description": "项目描述"
      }
    ],
    "pagination": {
      "pageNo": 1,
      "pageSize": 10,
      "total": 100,
      "totalPages": 10
    }
  }
}
```

#### 字段说明

**hero 对象字段**

| 字段 | 类型 | 说明 |
|------|------|------|
| `title` | String | 页面主标题 |
| `subtitle` | String | 页面副标题/描述文字 |

**items 数组子对象字段**

| 字段 | 类型 | 说明 |
|------|------|------|
| `id` | Number | 唯一标识 |
| `title` | String | 标题/名称 |
| `description` | String | 描述文字 |

**pagination 分页对象字段**

| 字段 | 类型 | 说明 |
|------|------|------|
| `pageNo` | Number | 当前页码 |
| `pageSize` | Number | 每页条数 |
| `total` | Number | 总记录数 |
| `totalPages` | Number | 总页数 |

#### 请求示例

```javascript
import axios from 'axios'

// 示例1：无筛选条件，获取第一页全部数据
const { data: res } = await axios.get('/api/{endpoint}')

// 示例2：按分类筛选 + 关键词搜索
const { data: res } = await axios.get('/api/{endpoint}', {
  params: {
    keyword: '关键词',
    category: 'category-id',
    pageNo: 1,
    pageSize: 10
  }
})

if (res.code === 0) {
  const { hero, items, pagination } = res.data
  console.log('Hero 配置:', hero)
  console.log('数据列表:', items)
  console.log('分页信息:', pagination)
}
```

---

## 数据表结构设计

### {主表名}

| 字段名 | 类型 | 说明 |
|--------|------|------|
| id | BIGINT | 主键，唯一标识 |
| title | VARCHAR(200) | 标题/名称 |
| description | TEXT | 描述文字 |
| sort_order | INT | 排序序号 |
| create_time | DATETIME | 创建时间 |
| update_time | DATETIME | 更新时间 |

### {配置表名}

| 字段名 | 类型 | 说明 |
|--------|------|------|
| id | BIGINT | 主键 |
| config_key | VARCHAR(50) | 配置项标识 |
| config_value | TEXT | 配置值 |
| update_time | DATETIME | 更新时间 |

**关联关系说明**：
- 配置表与主表：无直接关联，配置表存储页面级配置

---

## Mock 数据说明

当前前端使用 Mock 实现，位于：

```
src/mock/modules/{module}.ts
```

### Mock 实现的接口映射

| 前端 Mock 函数 | 对应真实接口 | 说明 |
|----------------|------------|------|
| `get{Module}Data(params)` | `GET /api/{endpoint}` | 获取页面数据（支持筛选分页） |

### 后端对接指南

对接真实后端时，只需将 Mock 函数替换为真实 `axios` 请求即可：

```javascript
// 替换前（Mock）
export const get{Module}Data = async (params = {}) => {
  // Mock 实现...
}

// 替换后（真实接口）
export const get{Module}Data = async (params) => {
  const { data } = await axios.get('/api/{endpoint}', { params })
  return data
}
```

---

## 注意事项

1. **统一响应格式**: 接口返回 `{ code, message, data }` 结构，前端通过 `code === 0` 判断成功
2. **筛选逻辑**: `keyword` 和 `category` 可组合使用，同时满足两个条件时取交集
3. **搜索范围**: 关键词搜索匹配 `title` 和 `description` 字段，大小写不敏感
4. **分页逻辑**: 分页仅作用于 `items` 数组，`hero` 配置不受分页影响，每次请求完整返回
5. **默认分类**: `all`（全部）为前端固定值，不需要入库存储
6. **图片路径**: `image` 字段为相对路径（以 `/images/` 开头），前端拼接静态资源基础路径
7. **排序**: 列表返回时按 `sort_order` 升序排列

---

## 更新日志

| 版本 | 日期 | 说明 |
|------|------|------|
| 1.0 | YYYY-MM-DD | 初始版本 |
