# 热点新闻爬取与展示系统

一个自动获取最新热点新闻，并按照不同类别在前端页面进行展示的系统。

## 项目概述

本系统旨在开发一个热点新闻爬取与展示平台，能够：

- 自动从多个新闻源获取最新热点新闻
- 按类别（经济、政治、艺术、科技、体育等）分类展示
- 提供响应式前端界面，支持多端访问
- 支持搜索和筛选功能

## 技术栈

| 层级 | 技术选型 |
|------|----------|
| **前端** | Vue.js 3 + TypeScript + Element Plus + Vite |
| **后端** | Python 3.11 + FastAPI + SQLAlchemy |
| **数据库** | PostgreSQL 15 + Redis 7 |
| **爬虫** | Scrapy + Selenium + Celery |
| **部署** | Docker + Nginx |

## 项目结构

```
hot-news-system/
├── docs/                       # 设计文档
│   ├── technical-solution.md   # 技术方案文档
│   ├── api-documentation.md    # API 接口文档
│   ├── database-design.md      # 数据库设计文档
│   └── architecture-diagram.md # 系统架构图
├── backend/                    # 后端服务 (待开发)
├── frontend/                   # 前端应用 (待开发)
├── crawler/                    # 爬虫服务 (待开发)
└── docker-compose.yml          # Docker 编排 (待开发)
```

## 设计文档

本项目包含以下设计文档：

1. **[技术方案文档](docs/technical-solution.md)** - 技术选型、系统架构、新闻源调研
2. **[API 接口文档](docs/api-documentation.md)** - RESTful API 接口规范
3. **[数据库设计文档](docs/database-design.md)** - 数据库表结构设计
4. **[系统架构图](docs/architecture-diagram.md)** - 系统架构可视化图表

## 功能特性

### 新闻爬取模块
- 支持多个新闻源的数据爬取
- 定时自动更新新闻数据（每30分钟）
- 数据去重和清洗
- 异常处理和日志记录

### 新闻分类
- 经济类：财经、市场、商业等
- 政治类：国内政治、国际政治等
- 艺术类：文化、艺术、娱乐等
- 科技类：科技创新、互联网、AI 等
- 体育类：各类体育赛事和运动
- 其他类别：可根据需要扩展

### 前端展示
- 响应式设计，支持多端访问
- 按类别分栏展示
- 新闻卡片展示（标题、摘要、来源、时间）
- 支持搜索和筛选功能
- 支持点击查看详情

## 里程碑

### 阶段1：需求分析与设计 ✅

- [x] 完成项目需求分析和功能规划
- [x] 完成技术选型评估（Vue.js + FastAPI + PostgreSQL）
- [x] 完成系统架构设计和模块划分
- [x] 编写技术方案文档 (`docs/technical-solution.md`)
- [x] 设计 RESTful API 接口规范 (`docs/api-documentation.md`)
- [x] 完成数据库表结构设计 (`docs/database-design.md`)
- [x] 绘制系统架构图 (`docs/architecture-diagram.md`)
- [x] 调研并确定目标新闻源（新浪、网易、腾讯等）

**交付物**：技术方案文档、API 接口文档、数据库设计文档、系统架构图

---

### 阶段2：爬虫开发 🔲

- [ ] 搭建 Scrapy 爬虫项目框架
- [ ] 实现新浪新闻爬虫 (sina_spider.py)
- [ ] 实现网易新闻爬虫 (netease_spider.py)
- [ ] 实现腾讯新闻爬虫 (tencent_spider.py)
- [ ] 开发数据清洗 Pipeline（提取标题、正文、时间、来源）
- [ ] 开发数据去重 Pipeline（基于 URL 和标题 Hash）
- [ ] 开发自动分类 Pipeline（基于关键词规则）
- [ ] 集成 Selenium 处理动态加载页面
- [ ] 配置 Celery + Redis 实现定时任务调度
- [ ] 实现爬取日志记录和异常处理机制

**交付物**：完整的爬虫服务模块，支持多源数据采集和自动分类

---

### 阶段3：后端 API 开发 🔲

- [ ] 搭建 FastAPI 项目框架
- [ ] 配置数据库连接（PostgreSQL + SQLAlchemy）
- [ ] 实现新闻相关 API（列表、详情、热门、最新）
- [ ] 实现分类相关 API（分类列表、分类新闻）
- [ ] 实现搜索 API（关键词搜索、热门搜索词）
- [ ] 实现标签和新闻源 API
- [ ] 实现统计 API（总体统计、分类统计）
- [ ] 配置 Redis 缓存提升查询性能
- [ ] 实现 API 速率限制和错误处理
- [ ] 编写 API 自动化测试用例

**交付物**：完整的后端 RESTful API 服务，支持新闻查询、搜索和统计

---

### 阶段4：前端开发 🔲

- [ ] 搭建 Vue.js 3 + TypeScript 项目框架
- [ ] 配置 Element Plus UI 组件库
- [ ] 实现首页布局（轮播图、热门新闻、分类展示）
- [ ] 实现新闻列表页（分页、筛选、排序）
- [ ] 实现新闻详情页（正文展示、相关推荐）
- [ ] 实现分类页面（按类别浏览新闻）
- [ ] 实现搜索功能（关键词搜索、搜索结果展示）
- [ ] 配置 Pinia 状态管理
- [ ] 实现响应式设计（适配 PC、平板、手机）
- [ ] 优化页面加载性能和用户体验

**交付物**：完整的前端 SPA 应用，提供美观易用的新闻浏览界面

---

### 阶段5：测试与优化 🔲

- [ ] 编写并执行后端单元测试
- [ ] 编写并执行前端组件测试
- [ ] 进行 API 接口集成测试
- [ ] 进行爬虫稳定性测试
- [ ] 执行性能压力测试（目标：1000+ 并发用户）
- [ ] 优化数据库查询性能（索引优化、查询优化）
- [ ] 优化前端加载速度（代码分割、懒加载）
- [ ] 修复发现的 Bug 和问题
- [ ] 进行安全性检查和漏洞修复

**交付物**：测试报告、性能优化报告、Bug 修复记录

---

### 阶段6：部署上线 🔲

- [ ] 编写 Dockerfile 和 docker-compose.yml
- [ ] 配置 Nginx 反向代理和 SSL 证书
- [ ] 配置生产环境数据库和缓存
- [ ] 部署爬虫服务和定时任务
- [ ] 部署后端 API 服务
- [ ] 部署前端静态资源
- [ ] 配置日志收集和监控告警
- [ ] 编写运维文档和部署手册
- [ ] 进行生产环境验证测试
- [ ] 正式上线运行

**交付物**：生产环境部署、运维文档、监控系统

## 开发指南

### 环境要求

- Python 3.11+
- Node.js 18+
- PostgreSQL 15+
- Redis 7+
- Docker & Docker Compose

### 快速开始

```bash
# 克隆项目
git clone <repository-url>
cd hot-news-system

# 使用 Docker Compose 启动所有服务 (待实现)
docker-compose up -d
```

## 许可证

MIT License
