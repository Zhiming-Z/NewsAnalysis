# 热点新闻爬取与展示系统 - API 接口文档

## 概述

本文档描述热点新闻系统的 RESTful API 接口规范。

### 基础信息

- **Base URL**: `https://api.example.com/api/v1`
- **协议**: HTTPS
- **数据格式**: JSON
- **字符编码**: UTF-8

### 通用响应格式

#### 成功响应

```json
{
  "code": 0,
  "message": "success",
  "data": { ... }
}
```

#### 错误响应

```json
{
  "code": 40001,
  "message": "错误信息描述",
  "data": null
}
```

### 错误码说明

| 错误码 | 说明 |
|--------|------|
| 0 | 成功 |
| 40001 | 参数错误 |
| 40002 | 资源不存在 |
| 40003 | 请求频率超限 |
| 50001 | 服务器内部错误 |

---

## 1. 新闻接口

### 1.1 获取新闻列表

获取新闻列表，支持分页和筛选。

**请求**

```
GET /news
```

**查询参数**

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| page | integer | 否 | 页码，默认 1 |
| page_size | integer | 否 | 每页数量，默认 20，最大 100 |
| category_id | integer | 否 | 分类 ID，筛选指定分类 |
| source | string | 否 | 新闻来源，如 "sina", "netease" |
| keyword | string | 否 | 搜索关键词 |
| start_date | string | 否 | 开始日期，格式 YYYY-MM-DD |
| end_date | string | 否 | 结束日期，格式 YYYY-MM-DD |
| sort_by | string | 否 | 排序字段：publish_time(默认), views |
| sort_order | string | 否 | 排序方向：desc(默认), asc |

**响应示例**

```json
{
  "code": 0,
  "message": "success",
  "data": {
    "total": 1234,
    "page": 1,
    "page_size": 20,
    "total_pages": 62,
    "items": [
      {
        "id": 10001,
        "title": "新闻标题示例",
        "summary": "新闻摘要内容，通常为正文前200字...",
        "source": "新浪新闻",
        "source_url": "https://news.sina.com.cn/xxx",
        "author": "记者名",
        "cover_image": "https://example.com/images/cover.jpg",
        "category": {
          "id": 1,
          "name": "科技",
          "slug": "technology"
        },
        "tags": ["人工智能", "科技创新"],
        "publish_time": "2026-01-30T10:30:00Z",
        "created_at": "2026-01-30T10:35:00Z",
        "views": 1520
      }
    ]
  }
}
```

---

### 1.2 获取新闻详情

根据新闻 ID 获取完整新闻内容。

**请求**

```
GET /news/{news_id}
```

**路径参数**

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| news_id | integer | 是 | 新闻 ID |

**响应示例**

```json
{
  "code": 0,
  "message": "success",
  "data": {
    "id": 10001,
    "title": "新闻标题示例",
    "content": "<p>新闻正文内容，支持HTML格式...</p>",
    "summary": "新闻摘要内容",
    "source": "新浪新闻",
    "source_url": "https://news.sina.com.cn/xxx",
    "author": "记者名",
    "cover_image": "https://example.com/images/cover.jpg",
    "images": [
      "https://example.com/images/1.jpg",
      "https://example.com/images/2.jpg"
    ],
    "category": {
      "id": 1,
      "name": "科技",
      "slug": "technology"
    },
    "tags": ["人工智能", "科技创新"],
    "publish_time": "2026-01-30T10:30:00Z",
    "created_at": "2026-01-30T10:35:00Z",
    "updated_at": "2026-01-30T10:35:00Z",
    "views": 1520,
    "related_news": [
      {
        "id": 10002,
        "title": "相关新闻标题",
        "cover_image": "https://example.com/images/cover2.jpg",
        "publish_time": "2026-01-30T09:00:00Z"
      }
    ]
  }
}
```

---

### 1.3 获取热门新闻

获取当前热门/推荐新闻列表。

**请求**

```
GET /news/hot
```

**查询参数**

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| limit | integer | 否 | 返回数量，默认 10，最大 50 |
| category_id | integer | 否 | 分类 ID |
| time_range | string | 否 | 时间范围：day, week, month，默认 day |

**响应示例**

```json
{
  "code": 0,
  "message": "success",
  "data": {
    "items": [
      {
        "id": 10001,
        "title": "热门新闻标题",
        "summary": "新闻摘要...",
        "source": "新浪新闻",
        "cover_image": "https://example.com/images/cover.jpg",
        "category": {
          "id": 1,
          "name": "科技",
          "slug": "technology"
        },
        "publish_time": "2026-01-30T10:30:00Z",
        "views": 15200,
        "hot_score": 98.5
      }
    ]
  }
}
```

---

### 1.4 获取最新新闻

获取最新发布的新闻列表。

**请求**

```
GET /news/latest
```

**查询参数**

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| limit | integer | 否 | 返回数量，默认 10，最大 50 |
| category_id | integer | 否 | 分类 ID |

**响应示例**

```json
{
  "code": 0,
  "message": "success",
  "data": {
    "items": [
      {
        "id": 10005,
        "title": "最新新闻标题",
        "summary": "新闻摘要...",
        "source": "网易新闻",
        "cover_image": "https://example.com/images/cover.jpg",
        "category": {
          "id": 2,
          "name": "经济",
          "slug": "economy"
        },
        "publish_time": "2026-01-30T14:30:00Z",
        "views": 320
      }
    ]
  }
}
```

---

## 2. 分类接口

### 2.1 获取分类列表

获取所有新闻分类。

**请求**

```
GET /categories
```

**响应示例**

```json
{
  "code": 0,
  "message": "success",
  "data": {
    "items": [
      {
        "id": 1,
        "name": "科技",
        "slug": "technology",
        "description": "科技创新、互联网、AI 等科技新闻",
        "icon": "tech-icon",
        "news_count": 3256,
        "sort_order": 1
      },
      {
        "id": 2,
        "name": "经济",
        "slug": "economy",
        "description": "财经、市场、商业等经济相关新闻",
        "icon": "economy-icon",
        "news_count": 2180,
        "sort_order": 2
      },
      {
        "id": 3,
        "name": "政治",
        "slug": "politics",
        "description": "国内政治、国际政治等政治相关新闻",
        "icon": "politics-icon",
        "news_count": 1890,
        "sort_order": 3
      },
      {
        "id": 4,
        "name": "体育",
        "slug": "sports",
        "description": "各类体育赛事和运动新闻",
        "icon": "sports-icon",
        "news_count": 2450,
        "sort_order": 4
      },
      {
        "id": 5,
        "name": "艺术",
        "slug": "arts",
        "description": "文化、艺术、娱乐等新闻",
        "icon": "arts-icon",
        "news_count": 1650,
        "sort_order": 5
      },
      {
        "id": 6,
        "name": "其他",
        "slug": "others",
        "description": "其他类别新闻",
        "icon": "others-icon",
        "news_count": 980,
        "sort_order": 99
      }
    ]
  }
}
```

---

### 2.2 获取分类详情

根据分类 ID 或 slug 获取分类详情。

**请求**

```
GET /categories/{category_id}
```

**路径参数**

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| category_id | integer/string | 是 | 分类 ID 或 slug |

**响应示例**

```json
{
  "code": 0,
  "message": "success",
  "data": {
    "id": 1,
    "name": "科技",
    "slug": "technology",
    "description": "科技创新、互联网、AI 等科技新闻",
    "icon": "tech-icon",
    "news_count": 3256,
    "sort_order": 1,
    "created_at": "2026-01-01T00:00:00Z",
    "updated_at": "2026-01-30T00:00:00Z"
  }
}
```

---

### 2.3 获取分类下的新闻

获取指定分类下的新闻列表。

**请求**

```
GET /categories/{category_id}/news
```

**路径参数**

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| category_id | integer/string | 是 | 分类 ID 或 slug |

**查询参数**

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| page | integer | 否 | 页码，默认 1 |
| page_size | integer | 否 | 每页数量，默认 20 |
| sort_by | string | 否 | 排序字段：publish_time, views |
| sort_order | string | 否 | 排序方向：desc, asc |

**响应示例**

与 "获取新闻列表" 接口响应格式相同。

---

## 3. 搜索接口

### 3.1 搜索新闻

根据关键词搜索新闻。

**请求**

```
GET /search
```

**查询参数**

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| q | string | 是 | 搜索关键词 |
| page | integer | 否 | 页码，默认 1 |
| page_size | integer | 否 | 每页数量，默认 20 |
| category_id | integer | 否 | 限定分类 |
| source | string | 否 | 限定来源 |
| start_date | string | 否 | 开始日期 |
| end_date | string | 否 | 结束日期 |

**响应示例**

```json
{
  "code": 0,
  "message": "success",
  "data": {
    "total": 156,
    "page": 1,
    "page_size": 20,
    "total_pages": 8,
    "query": "人工智能",
    "items": [
      {
        "id": 10001,
        "title": "<em>人工智能</em>发展新突破",
        "summary": "最新<em>人工智能</em>技术取得重大进展...",
        "source": "新浪新闻",
        "cover_image": "https://example.com/images/cover.jpg",
        "category": {
          "id": 1,
          "name": "科技",
          "slug": "technology"
        },
        "publish_time": "2026-01-30T10:30:00Z",
        "score": 15.6
      }
    ]
  }
}
```

---

### 3.2 获取热门搜索词

获取当前热门搜索关键词。

**请求**

```
GET /search/hot-keywords
```

**查询参数**

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| limit | integer | 否 | 返回数量，默认 10 |

**响应示例**

```json
{
  "code": 0,
  "message": "success",
  "data": {
    "items": [
      {
        "keyword": "人工智能",
        "search_count": 1520,
        "trend": "up"
      },
      {
        "keyword": "股市",
        "search_count": 1280,
        "trend": "stable"
      },
      {
        "keyword": "新能源",
        "search_count": 980,
        "trend": "up"
      }
    ]
  }
}
```

---

## 4. 标签接口

### 4.1 获取热门标签

获取热门新闻标签。

**请求**

```
GET /tags/hot
```

**查询参数**

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| limit | integer | 否 | 返回数量，默认 20 |

**响应示例**

```json
{
  "code": 0,
  "message": "success",
  "data": {
    "items": [
      {
        "id": 1,
        "name": "人工智能",
        "news_count": 356
      },
      {
        "id": 2,
        "name": "新能源",
        "news_count": 289
      }
    ]
  }
}
```

---

### 4.2 获取标签下的新闻

获取指定标签关联的新闻列表。

**请求**

```
GET /tags/{tag_id}/news
```

**路径参数**

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| tag_id | integer | 是 | 标签 ID |

**查询参数**

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| page | integer | 否 | 页码，默认 1 |
| page_size | integer | 否 | 每页数量，默认 20 |

**响应示例**

与 "获取新闻列表" 接口响应格式相同。

---

## 5. 新闻源接口

### 5.1 获取新闻源列表

获取支持的新闻来源列表。

**请求**

```
GET /sources
```

**响应示例**

```json
{
  "code": 0,
  "message": "success",
  "data": {
    "items": [
      {
        "id": 1,
        "name": "新浪新闻",
        "slug": "sina",
        "domain": "news.sina.com.cn",
        "logo": "https://example.com/logos/sina.png",
        "news_count": 5680,
        "is_active": true
      },
      {
        "id": 2,
        "name": "网易新闻",
        "slug": "netease",
        "domain": "news.163.com",
        "logo": "https://example.com/logos/netease.png",
        "news_count": 4520,
        "is_active": true
      },
      {
        "id": 3,
        "name": "腾讯新闻",
        "slug": "tencent",
        "domain": "news.qq.com",
        "logo": "https://example.com/logos/tencent.png",
        "news_count": 4100,
        "is_active": true
      }
    ]
  }
}
```

---

## 6. 统计接口

### 6.1 获取总体统计

获取系统总体统计数据。

**请求**

```
GET /stats/overview
```

**响应示例**

```json
{
  "code": 0,
  "message": "success",
  "data": {
    "total_news": 125680,
    "total_sources": 8,
    "total_categories": 6,
    "today_news": 856,
    "last_update_time": "2026-01-30T14:30:00Z"
  }
}
```

---

### 6.2 获取分类统计

获取各分类的新闻数量统计。

**请求**

```
GET /stats/categories
```

**响应示例**

```json
{
  "code": 0,
  "message": "success",
  "data": {
    "items": [
      {
        "category_id": 1,
        "category_name": "科技",
        "total_count": 32560,
        "today_count": 156,
        "percentage": 25.9
      },
      {
        "category_id": 2,
        "category_name": "经济",
        "total_count": 28900,
        "today_count": 142,
        "percentage": 23.0
      }
    ]
  }
}
```

---

## 7. 系统接口

### 7.1 健康检查

检查 API 服务健康状态。

**请求**

```
GET /health
```

**响应示例**

```json
{
  "code": 0,
  "message": "success",
  "data": {
    "status": "healthy",
    "version": "1.0.0",
    "timestamp": "2026-01-30T14:30:00Z",
    "services": {
      "database": "ok",
      "redis": "ok",
      "crawler": "ok"
    }
  }
}
```

---

## 附录

### A. HTTP 状态码

| 状态码 | 说明 |
|--------|------|
| 200 | 请求成功 |
| 400 | 请求参数错误 |
| 404 | 资源不存在 |
| 429 | 请求频率超限 |
| 500 | 服务器内部错误 |

### B. 速率限制

- 普通接口：100 次/分钟
- 搜索接口：30 次/分钟

超过限制将返回 429 状态码。

### C. 文档版本

| 版本 | 日期 | 修改说明 |
|------|------|----------|
| v1.0 | 2026-01-30 | 初始版本 |
