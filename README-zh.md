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

- [x] **阶段1**: 需求分析与设计
- [ ] **阶段2**: 爬虫开发
- [ ] **阶段3**: 后端 API 开发
- [ ] **阶段4**: 前端开发
- [ ] **阶段5**: 测试与优化
- [ ] **阶段6**: 部署上线

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
