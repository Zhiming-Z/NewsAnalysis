# 热点新闻爬取与展示系统 - 数据库设计文档

## 1. 概述

本文档描述热点新闻系统的数据库设计，包括表结构、字段说明、索引设计和关系图。

### 1.1 数据库选型

- **主数据库**: PostgreSQL 15+
- **缓存数据库**: Redis 7+
- **字符集**: UTF-8

### 1.2 设计原则

- 遵循数据库范式设计
- 合理使用索引优化查询性能
- 预留扩展字段
- 软删除设计

---

## 2. ER 图 (实体关系图)

```
┌─────────────────┐       ┌─────────────────┐       ┌─────────────────┐
│    categories   │       │      news       │       │     sources     │
├─────────────────┤       ├─────────────────┤       ├─────────────────┤
│ id (PK)         │───┐   │ id (PK)         │   ┌───│ id (PK)         │
│ name            │   │   │ title           │   │   │ name            │
│ slug            │   │   │ content         │   │   │ slug            │
│ description     │   │   │ summary         │   │   │ domain          │
│ icon            │   └──►│ category_id(FK) │   │   │ logo            │
│ sort_order      │       │ source_id (FK)  │◄──┘   │ is_active       │
│ created_at      │       │ source_url      │       │ created_at      │
│ updated_at      │       │ author          │       │ updated_at      │
└─────────────────┘       │ cover_image     │       └─────────────────┘
                          │ publish_time    │
                          │ views           │       ┌─────────────────┐
                          │ is_hot          │       │      tags       │
                          │ status          │       ├─────────────────┤
                          │ created_at      │   ┌───│ id (PK)         │
                          │ updated_at      │   │   │ name            │
                          │ deleted_at      │   │   │ slug            │
                          └────────┬────────┘   │   │ created_at      │
                                   │            │   │ updated_at      │
                                   │            │   └─────────────────┘
                                   │            │
                          ┌────────┴────────┐   │
                          │    news_tags    │   │
                          ├─────────────────┤   │
                          │ news_id (FK)    │───┘
                          │ tag_id (FK)     │◄──────
                          │ created_at      │
                          └─────────────────┘

┌─────────────────┐       ┌─────────────────┐
│  news_images    │       │ search_keywords │
├─────────────────┤       ├─────────────────┤
│ id (PK)         │       │ id (PK)         │
│ news_id (FK)    │       │ keyword         │
│ image_url       │       │ search_count    │
│ sort_order      │       │ last_search_at  │
│ created_at      │       │ created_at      │
└─────────────────┘       │ updated_at      │
                          └─────────────────┘

┌─────────────────┐
│  crawl_logs     │
├─────────────────┤
│ id (PK)         │
│ source_id (FK)  │
│ start_time      │
│ end_time        │
│ status          │
│ news_count      │
│ error_message   │
│ created_at      │
└─────────────────┘
```

---

## 3. 表结构设计

### 3.1 分类表 (categories)

存储新闻分类信息。

```sql
CREATE TABLE categories (
    id SERIAL PRIMARY KEY,
    name VARCHAR(50) NOT NULL COMMENT '分类名称',
    slug VARCHAR(50) NOT NULL UNIQUE COMMENT '分类标识符',
    description VARCHAR(255) COMMENT '分类描述',
    icon VARCHAR(100) COMMENT '分类图标',
    sort_order INTEGER DEFAULT 0 COMMENT '排序顺序',
    is_active BOOLEAN DEFAULT TRUE COMMENT '是否启用',
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- 索引
CREATE INDEX idx_categories_slug ON categories(slug);
CREATE INDEX idx_categories_sort_order ON categories(sort_order);
```

**字段说明**

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| id | SERIAL | 是 | 主键，自增 |
| name | VARCHAR(50) | 是 | 分类名称，如"科技"、"经济" |
| slug | VARCHAR(50) | 是 | URL 友好的标识符，唯一 |
| description | VARCHAR(255) | 否 | 分类描述 |
| icon | VARCHAR(100) | 否 | 分类图标标识 |
| sort_order | INTEGER | 否 | 显示排序，数值越小越靠前 |
| is_active | BOOLEAN | 否 | 是否启用，默认 true |
| created_at | TIMESTAMP | 否 | 创建时间 |
| updated_at | TIMESTAMP | 否 | 更新时间 |

**初始数据**

```sql
INSERT INTO categories (name, slug, description, sort_order) VALUES
('科技', 'technology', '科技创新、互联网、AI 等科技新闻', 1),
('经济', 'economy', '财经、市场、商业等经济相关新闻', 2),
('政治', 'politics', '国内政治、国际政治等政治相关新闻', 3),
('体育', 'sports', '各类体育赛事和运动新闻', 4),
('艺术', 'arts', '文化、艺术、娱乐等新闻', 5),
('其他', 'others', '其他类别新闻', 99);
```

---

### 3.2 新闻源表 (sources)

存储新闻来源网站信息。

```sql
CREATE TABLE sources (
    id SERIAL PRIMARY KEY,
    name VARCHAR(100) NOT NULL COMMENT '来源名称',
    slug VARCHAR(50) NOT NULL UNIQUE COMMENT '来源标识符',
    domain VARCHAR(255) NOT NULL COMMENT '网站域名',
    logo VARCHAR(255) COMMENT '网站 Logo URL',
    description VARCHAR(255) COMMENT '来源描述',
    crawl_config JSONB COMMENT '爬虫配置',
    is_active BOOLEAN DEFAULT TRUE COMMENT '是否启用',
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- 索引
CREATE INDEX idx_sources_slug ON sources(slug);
CREATE INDEX idx_sources_is_active ON sources(is_active);
```

**字段说明**

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| id | SERIAL | 是 | 主键，自增 |
| name | VARCHAR(100) | 是 | 来源名称，如"新浪新闻" |
| slug | VARCHAR(50) | 是 | URL 友好的标识符，唯一 |
| domain | VARCHAR(255) | 是 | 网站域名 |
| logo | VARCHAR(255) | 否 | 网站 Logo URL |
| description | VARCHAR(255) | 否 | 来源描述 |
| crawl_config | JSONB | 否 | 爬虫配置（爬取规则等） |
| is_active | BOOLEAN | 否 | 是否启用爬取 |
| created_at | TIMESTAMP | 否 | 创建时间 |
| updated_at | TIMESTAMP | 否 | 更新时间 |

**初始数据**

```sql
INSERT INTO sources (name, slug, domain, description) VALUES
('新浪新闻', 'sina', 'news.sina.com.cn', '新浪网新闻中心'),
('网易新闻', 'netease', 'news.163.com', '网易新闻中心'),
('腾讯新闻', 'tencent', 'news.qq.com', '腾讯新闻'),
('今日头条', 'toutiao', 'www.toutiao.com', '今日头条'),
('澎湃新闻', 'thepaper', 'www.thepaper.cn', '澎湃新闻'),
('36氪', '36kr', '36kr.com', '36氪科技新闻');
```

---

### 3.3 新闻表 (news)

存储新闻主体信息，是系统核心表。

```sql
CREATE TABLE news (
    id BIGSERIAL PRIMARY KEY,
    title VARCHAR(255) NOT NULL COMMENT '新闻标题',
    content TEXT COMMENT '新闻正文',
    summary VARCHAR(500) COMMENT '新闻摘要',
    category_id INTEGER NOT NULL REFERENCES categories(id) COMMENT '分类ID',
    source_id INTEGER NOT NULL REFERENCES sources(id) COMMENT '来源ID',
    source_url VARCHAR(500) NOT NULL COMMENT '原文链接',
    source_url_hash VARCHAR(64) NOT NULL COMMENT '原文链接哈希值',
    author VARCHAR(100) COMMENT '作者',
    cover_image VARCHAR(500) COMMENT '封面图片URL',
    publish_time TIMESTAMP NOT NULL COMMENT '发布时间',
    views INTEGER DEFAULT 0 COMMENT '浏览次数',
    is_hot BOOLEAN DEFAULT FALSE COMMENT '是否热门',
    hot_score DECIMAL(10,2) DEFAULT 0 COMMENT '热度分数',
    status SMALLINT DEFAULT 1 COMMENT '状态：0-草稿，1-已发布，2-已下线',
    extra_data JSONB COMMENT '扩展数据',
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    deleted_at TIMESTAMP COMMENT '软删除时间'
);

-- 索引
CREATE INDEX idx_news_category_id ON news(category_id);
CREATE INDEX idx_news_source_id ON news(source_id);
CREATE INDEX idx_news_publish_time ON news(publish_time DESC);
CREATE INDEX idx_news_status ON news(status);
CREATE INDEX idx_news_is_hot ON news(is_hot);
CREATE INDEX idx_news_hot_score ON news(hot_score DESC);
CREATE INDEX idx_news_views ON news(views DESC);
CREATE UNIQUE INDEX idx_news_source_url_hash ON news(source_url_hash);
CREATE INDEX idx_news_created_at ON news(created_at DESC);
CREATE INDEX idx_news_deleted_at ON news(deleted_at) WHERE deleted_at IS NULL;

-- 全文搜索索引 (PostgreSQL)
CREATE INDEX idx_news_title_gin ON news USING gin(to_tsvector('chinese', title));
CREATE INDEX idx_news_content_gin ON news USING gin(to_tsvector('chinese', content));

-- 复合索引
CREATE INDEX idx_news_category_publish ON news(category_id, publish_time DESC);
CREATE INDEX idx_news_source_publish ON news(source_id, publish_time DESC);
```

**字段说明**

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| id | BIGSERIAL | 是 | 主键，自增 |
| title | VARCHAR(255) | 是 | 新闻标题 |
| content | TEXT | 否 | 新闻正文（HTML格式） |
| summary | VARCHAR(500) | 否 | 新闻摘要 |
| category_id | INTEGER | 是 | 关联分类ID |
| source_id | INTEGER | 是 | 关联来源ID |
| source_url | VARCHAR(500) | 是 | 原文链接 |
| source_url_hash | VARCHAR(64) | 是 | 原文链接的MD5/SHA哈希，用于去重 |
| author | VARCHAR(100) | 否 | 作者 |
| cover_image | VARCHAR(500) | 否 | 封面图片URL |
| publish_time | TIMESTAMP | 是 | 原文发布时间 |
| views | INTEGER | 否 | 浏览次数 |
| is_hot | BOOLEAN | 否 | 是否热门新闻 |
| hot_score | DECIMAL | 否 | 热度分数（用于排序） |
| status | SMALLINT | 否 | 状态：0-草稿，1-已发布，2-已下线 |
| extra_data | JSONB | 否 | 扩展数据（如原始JSON等） |
| created_at | TIMESTAMP | 否 | 入库时间 |
| updated_at | TIMESTAMP | 否 | 更新时间 |
| deleted_at | TIMESTAMP | 否 | 软删除时间 |

---

### 3.4 标签表 (tags)

存储新闻标签信息。

```sql
CREATE TABLE tags (
    id SERIAL PRIMARY KEY,
    name VARCHAR(50) NOT NULL UNIQUE COMMENT '标签名称',
    slug VARCHAR(50) NOT NULL UNIQUE COMMENT '标签标识符',
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- 索引
CREATE INDEX idx_tags_name ON tags(name);
CREATE INDEX idx_tags_slug ON tags(slug);
```

**字段说明**

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| id | SERIAL | 是 | 主键，自增 |
| name | VARCHAR(50) | 是 | 标签名称，唯一 |
| slug | VARCHAR(50) | 是 | URL 友好的标识符，唯一 |
| created_at | TIMESTAMP | 否 | 创建时间 |
| updated_at | TIMESTAMP | 否 | 更新时间 |

---

### 3.5 新闻-标签关联表 (news_tags)

新闻和标签的多对多关联表。

```sql
CREATE TABLE news_tags (
    news_id BIGINT NOT NULL REFERENCES news(id) ON DELETE CASCADE,
    tag_id INTEGER NOT NULL REFERENCES tags(id) ON DELETE CASCADE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    PRIMARY KEY (news_id, tag_id)
);

-- 索引
CREATE INDEX idx_news_tags_tag_id ON news_tags(tag_id);
```

**字段说明**

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| news_id | BIGINT | 是 | 新闻ID，外键 |
| tag_id | INTEGER | 是 | 标签ID，外键 |
| created_at | TIMESTAMP | 否 | 创建时间 |

---

### 3.6 新闻图片表 (news_images)

存储新闻中的图片信息。

```sql
CREATE TABLE news_images (
    id BIGSERIAL PRIMARY KEY,
    news_id BIGINT NOT NULL REFERENCES news(id) ON DELETE CASCADE,
    image_url VARCHAR(500) NOT NULL COMMENT '图片URL',
    local_path VARCHAR(255) COMMENT '本地存储路径',
    sort_order INTEGER DEFAULT 0 COMMENT '排序顺序',
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- 索引
CREATE INDEX idx_news_images_news_id ON news_images(news_id);
```

**字段说明**

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| id | BIGSERIAL | 是 | 主键，自增 |
| news_id | BIGINT | 是 | 新闻ID，外键 |
| image_url | VARCHAR(500) | 是 | 图片URL |
| local_path | VARCHAR(255) | 否 | 本地存储路径（如果下载保存） |
| sort_order | INTEGER | 否 | 图片排序 |
| created_at | TIMESTAMP | 否 | 创建时间 |

---

### 3.7 搜索关键词表 (search_keywords)

存储热门搜索关键词统计。

```sql
CREATE TABLE search_keywords (
    id SERIAL PRIMARY KEY,
    keyword VARCHAR(100) NOT NULL UNIQUE COMMENT '搜索关键词',
    search_count INTEGER DEFAULT 1 COMMENT '搜索次数',
    last_search_at TIMESTAMP COMMENT '最后搜索时间',
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- 索引
CREATE INDEX idx_search_keywords_count ON search_keywords(search_count DESC);
CREATE INDEX idx_search_keywords_last_search ON search_keywords(last_search_at DESC);
```

**字段说明**

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| id | SERIAL | 是 | 主键，自增 |
| keyword | VARCHAR(100) | 是 | 搜索关键词，唯一 |
| search_count | INTEGER | 否 | 搜索次数 |
| last_search_at | TIMESTAMP | 否 | 最后搜索时间 |
| created_at | TIMESTAMP | 否 | 创建时间 |
| updated_at | TIMESTAMP | 否 | 更新时间 |

---

### 3.8 爬取日志表 (crawl_logs)

记录爬虫执行日志。

```sql
CREATE TABLE crawl_logs (
    id BIGSERIAL PRIMARY KEY,
    source_id INTEGER REFERENCES sources(id) COMMENT '来源ID',
    task_id VARCHAR(100) COMMENT '任务ID',
    start_time TIMESTAMP NOT NULL COMMENT '开始时间',
    end_time TIMESTAMP COMMENT '结束时间',
    status SMALLINT DEFAULT 0 COMMENT '状态：0-进行中，1-成功，2-失败',
    news_count INTEGER DEFAULT 0 COMMENT '爬取新闻数量',
    new_count INTEGER DEFAULT 0 COMMENT '新增新闻数量',
    duplicate_count INTEGER DEFAULT 0 COMMENT '重复新闻数量',
    error_message TEXT COMMENT '错误信息',
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- 索引
CREATE INDEX idx_crawl_logs_source_id ON crawl_logs(source_id);
CREATE INDEX idx_crawl_logs_status ON crawl_logs(status);
CREATE INDEX idx_crawl_logs_start_time ON crawl_logs(start_time DESC);
```

**字段说明**

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| id | BIGSERIAL | 是 | 主键，自增 |
| source_id | INTEGER | 否 | 来源ID，外键 |
| task_id | VARCHAR(100) | 否 | Celery任务ID |
| start_time | TIMESTAMP | 是 | 爬取开始时间 |
| end_time | TIMESTAMP | 否 | 爬取结束时间 |
| status | SMALLINT | 否 | 状态：0-进行中，1-成功，2-失败 |
| news_count | INTEGER | 否 | 爬取总数量 |
| new_count | INTEGER | 否 | 新增数量 |
| duplicate_count | INTEGER | 否 | 重复数量 |
| error_message | TEXT | 否 | 错误信息 |
| created_at | TIMESTAMP | 否 | 创建时间 |

---

## 4. Redis 缓存设计

### 4.1 缓存 Key 规范

| Key 格式 | 说明 | 过期时间 |
|----------|------|----------|
| `news:detail:{news_id}` | 新闻详情缓存 | 1小时 |
| `news:list:hot` | 热门新闻列表 | 5分钟 |
| `news:list:latest` | 最新新闻列表 | 5分钟 |
| `news:list:category:{category_id}:{page}` | 分类新闻列表 | 10分钟 |
| `category:list` | 分类列表 | 1小时 |
| `source:list` | 新闻源列表 | 1小时 |
| `search:hot_keywords` | 热门搜索词 | 30分钟 |
| `crawl:dedup:{url_hash}` | URL去重缓存 | 7天 |
| `stats:overview` | 总体统计数据 | 10分钟 |

### 4.2 去重 Set

```
crawl:url_hashes - Set 类型，存储已爬取的 URL 哈希值
```

---

## 5. 数据库维护

### 5.1 数据清理策略

```sql
-- 清理 90 天前的新闻（软删除）
UPDATE news 
SET deleted_at = CURRENT_TIMESTAMP 
WHERE created_at < CURRENT_TIMESTAMP - INTERVAL '90 days' 
AND deleted_at IS NULL;

-- 清理 180 天前的已软删除新闻（物理删除）
DELETE FROM news 
WHERE deleted_at < CURRENT_TIMESTAMP - INTERVAL '90 days';

-- 清理 30 天前的爬取日志
DELETE FROM crawl_logs 
WHERE created_at < CURRENT_TIMESTAMP - INTERVAL '30 days';
```

### 5.2 定期维护任务

```sql
-- 更新统计数据
-- 每天凌晨执行
VACUUM ANALYZE news;
VACUUM ANALYZE news_tags;
REINDEX TABLE news;
```

### 5.3 备份策略

- **全量备份**: 每日凌晨 3:00
- **增量备份**: 每小时
- **保留周期**: 全量备份保留 30 天，增量备份保留 7 天

---

## 6. 性能优化建议

### 6.1 索引优化

- 对高频查询字段建立索引
- 使用复合索引优化多条件查询
- 定期分析和重建索引

### 6.2 查询优化

- 避免 SELECT *，只查询需要的字段
- 使用分页避免大结果集
- 使用 EXPLAIN 分析查询计划

### 6.3 分区建议

当数据量超过 1000 万条时，建议对 news 表按时间分区：

```sql
-- 按月分区示例
CREATE TABLE news_2026_01 PARTITION OF news
FOR VALUES FROM ('2026-01-01') TO ('2026-02-01');

CREATE TABLE news_2026_02 PARTITION OF news
FOR VALUES FROM ('2026-02-01') TO ('2026-03-01');
```

---

## 附录

### A. 完整建表脚本

见 `scripts/init_database.sql`

### B. 文档版本

| 版本 | 日期 | 修改说明 |
|------|------|----------|
| v1.0 | 2026-01-30 | 初始版本 |
