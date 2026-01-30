# Hot News Crawler and Display System
# 热点新闻爬取与展示系统

## 📖 项目简介

一个自动化的热点新闻爬取与展示系统，能够从多个新闻源自动抓取最新资讯，并按照不同类别（经济、政治、艺术、科技、体育等）在前端页面进行展示。

## ✨ 功能特性

### 核心功能
- 🔍 **多源爬取**: 支持从多个新闻网站自动抓取最新资讯
- 🏷️ **智能分类**: 自动将新闻分类到经济、政治、艺术、科技、体育等类别
- 🔄 **定时更新**: 可配置的定时任务，自动更新新闻数据
- 🎨 **响应式设计**: 支持 PC 端和移动端访问
- 🔎 **搜索筛选**: 支持关键词搜索和分类筛选
- 📊 **数据统计**: 新闻来源、时间、分类等信息展示

### 技术亮点
- 数据去重和清洗
- 异常处理和日志记录
- 高性能缓存机制
- RESTful API 设计
- 容器化部署支持

## 🛠️ 技术栈

### 后端
- **语言**: Python / Node.js
- **框架**: Flask / Express
- **爬虫**: Scrapy / Selenium / Puppeteer
- **数据库**: PostgreSQL / MongoDB
- **缓存**: Redis

### 前端
- **框架**: React / Vue.js
- **UI 组件库**: Ant Design / Element UI
- **状态管理**: Redux / Vuex
- **构建工具**: Webpack / Vite

### 部署
- **容器**: Docker
- **编排**: Docker Compose
- **CI/CD**: GitHub Actions / GitLab CI

## 📁 项目结构

```
.
├── backend/                 # 后端代码
│   ├── crawler/            # 爬虫模块
│   │   ├── spiders/        # 爬虫脚本
│   │   ├── processors/     # 数据处理
│   │   └── utils/          # 工具函数
│   ├── api/                # API 接口
│   │   ├── routes/         # 路由定义
│   │   ├── controllers/    # 控制器
│   │   └── models/         # 数据模型
│   ├── config/             # 配置文件
│   ├── tests/              # 测试代码
│   └── requirements.txt    # Python 依赖
├── frontend/               # 前端代码
│   ├── src/
│   │   ├── components/     # 组件
│   │   ├── pages/          # 页面
│   │   ├── services/       # API 调用
│   │   └── utils/          # 工具函数
│   ├── public/             # 静态资源
│   └── package.json        # Node 依赖
├── docker/                 # Docker 配置
│   ├── Dockerfile.backend
│   ├── Dockerfile.frontend
│   └── docker-compose.yml
├── docs/                   # 文档
│   ├── api.md             # API 文档
│   ├── database.md        # 数据库设计
│   └── deployment.md      # 部署文档
└── README.md              # 项目说明
```

## 🚀 快速开始

### 环境要求
- Python 3.8+ 或 Node.js 16+
- PostgreSQL 13+ 或 MongoDB 5+
- Redis 6+
- Docker (可选)

### 安装步骤

#### 1. 克隆仓库
```bash
git clone https://github.com/your-username/hot-news-crawler.git
cd hot-news-crawler
```

#### 2. 后端安装

**Python 版本:**
```bash
cd backend
python -m venv venv
source venv/bin/activate  # Windows: venv\Scripts\activate
pip install -r requirements.txt
```

**Node.js 版本:**
```bash
cd backend
npm install
```

#### 3. 前端安装
```bash
cd frontend
npm install
```

#### 4. 配置环境变量
```bash
# 复制配置文件模板
cp backend/config/.env.example backend/config/.env

# 编辑配置文件，填写数据库、缓存等配置信息
```

#### 5. 初始化数据库
```bash
# 运行数据库迁移脚本
cd backend
python scripts/init_db.py  # 或 npm run init-db
```

#### 6. 启动服务

**开发环境:**
```bash
# 启动后端
cd backend
python app.py  # 或 npm run dev

# 启动前端（新终端）
cd frontend
npm run dev
```

**Docker 部署:**
```bash
docker-compose up -d
```

### 访问应用
- 前端页面: http://localhost:3000
- API 文档: http://localhost:5000/docs
- 管理后台: http://localhost:5000/admin

## 📝 开发指南

### 添加新的新闻源

1. 在 `backend/crawler/spiders/` 创建新的爬虫文件
2. 继承基础爬虫类
3. 实现 `parse()` 方法解析数据
4. 在配置文件中注册新爬虫

示例：
```python
# backend/crawler/spiders/example_news.py
from .base import BaseSpider

class ExampleNewsSpider(BaseSpider):
    name = 'example_news'
    start_urls = ['https://example.com/news']

    def parse(self, response):
        # 解析逻辑
        pass
```

### API 接口

所有 API 接口遵循 RESTful 规范：

- `GET /api/news` - 获取新闻列表
- `GET /api/news/:id` - 获取新闻详情
- `GET /api/news/categories` - 获取分类列表
- `GET /api/news/search?q=keyword` - 搜索新闻

详细 API 文档请查看 [docs/api.md](docs/api.md)

## 🧪 测试

### 后端测试
```bash
cd backend
pytest  # 或 npm test
```

### 前端测试
```bash
cd frontend
npm run test
```

### 爬虫测试
```bash
cd backend
python -m crawler.tests.test_spiders
```

## 📊 项目进度

本项目使用 Linear 进行项目管理和任务跟踪。

- [Linear 项目](https://linear.app/zhaozhiming/project/hot-news-crawler-and-display-system-f20e1eae6e87)
- [需求分析与设计](https://linear.app/zhaozhiming/issue/ENG-12/需求分析与设计)
- [爬虫开发](https://linear.app/zhaozhiming/issue/ENG-13/爬虫开发)
- [后端 API 开发](https://linear.app/zhaozhiming/issue/ENG-14/后端-api-开发)
- [前端开发](https://linear.app/zhaozhiming/issue/ENG-15/前端开发)
- [测试与优化](https://linear.app/zhaozhiming/issue/ENG-16/测试与优化)
- [部署上线](https://linear.app/zhaozhiming/issue/ENG-17/部署上线)

## 🤝 贡献指南

欢迎提交 Issue 和 Pull Request！

1. Fork 本仓库
2. 创建特性分支 (`git checkout -b feature/AmazingFeature`)
3. 提交更改 (`git commit -m 'Add some AmazingFeature'`)
4. 推送到分支 (`git push origin feature/AmazingFeature`)
5. 提交 Pull Request

## 📄 许可证

本项目采用 MIT 许可证 - 查看 [LICENSE](LICENSE) 文件了解详情

## 📮 联系方式

如有问题或建议，请通过以下方式联系：

- 提交 [Issue](https://github.com/your-username/hot-news-crawler/issues)
- 发送邮件至: your-email@example.com

## 🙏 致谢

感谢所有为这个项目做出贡献的开发者！

---

**注意**: 本项目仅供学习交流使用，请遵守相关新闻网站的 robots.txt 和使用条款。
