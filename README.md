<div align="center">
  
# 🛫 Ctrip Assistant - AI 智能旅行助手

[![Python](https://img.shields.io/badge/Python-3.11+-blue.svg)](https://www.python.org/downloads/)
[![FastAPI](https://img.shields.io/badge/FastAPI-0.104+-green.svg)](https://fastapi.tiangolo.com/)
[![LangChain](https://img.shields.io/badge/LangChain-0.1+-orange.svg)](https://www.langchain.com/)
[![License](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

基于 LangGraph 和 LangChain 构建的智能旅行助手应用，提供航班预订、酒店预订、租车服务和旅行推荐等功能。

</div>

---
## 📖 目录

- [功能特性](#功能特性)
- [技术栈](#技术栈)
- [快速开始](#快速开始)
- [安装部署](#安装部署)
- [使用指南](#使用指南)
- [项目结构](#项目结构)
- [配置说明](#配置说明)
- [API 文档](#api文档)
- [开发指南](#开发指南)
- [常见问题](#常见问题)
- [贡献指南](#贡献指南)
- [许可证](#许可证)

<a name="功能特性"></a>
## ✨ 功能特性

- 🛬 **航班管理** - 搜索、预订、更新和取消航班
- 🏨 **酒店预订** - 搜索和管理酒店预订
- 🚗 **租车服务** - 预订和管理租车服务
- 🗺️ **旅行推荐** - 获取个性化的旅行建议
- 🤖 **AI 驱动** - 自然语言交互的多智能体工作流
- 👤 **人机协作** - 敏感操作需要用户确认
- 🔐 **用户认证** - JWT + OAuth2 安全认证
- 📊 **状态管理** - LangGraph 检查点持久化

<a name="技术栈"></a>
## 🛠️ 技术栈

| 类别 | 技术 |
|------|------|
| 后端框架 | FastAPI |
| AI 框架 | LangGraph, LangChain |
| 大语言模型 | OpenAI GPT |
| 数据库 | MySQL / SQLite |
| 认证授权 | JWT, OAuth2 |
| 配置管理 | Dynaconf |
| 搜索工具 | Tavily Search API |

<a name="快速开始"></a>
## 🚀 快速开始

### 前置要求

- Python 3.11 或更高版本
- MySQL 8.0+ (或使用 SQLite 开发模式)
- OpenAI API Key
- Tavily API Key (用于网络搜索)

### 一键启动

```bash
# 1. 克隆项目
git clone https://github.com/ranger52065/Ctrip-Assistant.git
cd Ctrip-Assistant

# 2. 创建虚拟环境
python -m venv .venv
.venv\Scripts\activate  # Windows
# source .venv/bin/activate  # Linux/Mac

# 3. 安装依赖
pip install -r requirements.txt

# 4. 配置环境变量
cp .env.example .env
# 编辑 .env 文件，填入你的配置信息

# 5. 启动应用
python main.py
```

访问 http://127.0.0.1:8000/docs 查看 API 文档

<a name="安装部署"></a>
## 📦 安装部署

### 详细安装步骤

#### 1. 克隆项目

```bash
git clone https://github.com/ranger52065/Ctrip-Assistant.git
cd Ctrip-Assistant
```

#### 2. 创建虚拟环境

**Windows:**
```bash
python -m venv .venv
.venv\Scripts\activate
```

**Linux/Mac:**
```bash
python3 -m venv .venv
source .venv/bin/activate
```

#### 3. 安装依赖

```bash
pip install -r requirements.txt
```

#### 4. 配置环境变量

创建 `.env` 文件（从模板复制）：

```bash
cp .env.example .env
```

编辑 `.env` 文件，填入你的配置：

```env
# OpenAI 配置
OPENAI_API_KEY=你的OpenAI密钥

# Tavily 搜索 API
TAVILY_API_KEY=你的Tavily密钥

# JWT 密钥（使用以下命令生成）
# python -c "import secrets; print(secrets.token_urlsafe(32))"
JWT_SECRET_KEY=你的JWT密钥

# 数据库配置（可选，默认使用 SQLite）
# EMP_CONF_DATABASE__DRIVER=mysql
# EMP_CONF_DATABASE__NAME=你的数据库名
# EMP_CONF_DATABASE__HOST=127.0.0.1
# EMP_CONF_DATABASE__PORT=3306
# EMP_CONF_DATABASE__USERNAME=你的用户名
# EMP_CONF_DATABASE__PASSWORD=你的密码
```

**获取 API 密钥：**
- OpenAI: https://platform.openai.com/api-keys
- Tavily: https://tavily.com/

#### 5. 配置应用设置

编辑 `config/development.yml`：

```yaml
# 日志级别
LOG_LEVEL: INFO

# 服务器配置
HOST: 127.0.0.1
PORT: 8000

# CORS 设置（添加你的前端地址）
ORIGINS: 
  - 'http://localhost:8080'
  - 'http://127.0.0.1:8080'

# 数据库配置
DATABASE:
  DRIVER: mysql
  NAME: 你的数据库名
  HOST: 127.0.0.1
  PORT: 3306
  USERNAME: 你的用户名
  PASSWORD: 你的密码
  QUERY:
    charset: utf8mb4

# JWT 配置
JWT_SECRET_KEY: 你的JWT密钥
ALGORITHM: HS256
ACCESS_TOKEN_EXPIRE_MINUTES: 30

# API 白名单（无需认证的接口）
WHITE_LIST:
  - '/api/login'
  - '/api/register'
  - '/static'
  - '/docs'
  - '/swagger'
  - '/openapi'
  - '/api/auth'

# 新用户默认密码
DEFAULT_PASSWORD: 你的默认密码
```

#### 6. 初始化数据库

应用会在首次运行时自动创建数据表。你也可以手动初始化：

```bash
python tools/init_db.py
```

### 启动应用

**开发模式：**

```bash
python main.py
```

或使用 uvicorn：

```bash
uvicorn main:Server.app --host 127.0.0.1 --port 8000 --reload
```

**生产模式：**

```bash
uvicorn main:Server.app --host 0.0.0.0 --port 8000
```

### 访问应用

- 📚 API 文档: http://127.0.0.1:8000/docs
- 📖 备用文档: http://127.0.0.1:8000/redoc
- 🌐 API 基础地址: http://127.0.0.1:8000/api

<a name="使用指南"></a>
## 📋 使用指南

### 1. 用户注册

```bash
curl -X POST "http://127.0.0.1:8000/api/register/" \
  -H "Content-Type: application/json" \
  -d '{
    "username": "testuser",
    "password": "password123",
    "email": "test@example.com",
    "real_name": "测试用户"
  }'
```

### 2. 用户登录

```bash
curl -X POST "http://127.0.0.1:8000/api/login/" \
  -H "Content-Type: application/json" \
  -d '{
    "username": "testuser",
    "password": "password123"
  }'
```

返回示例：

```json
{
  "id": 1,
  "username": "testuser",
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
}
```

### 3. 与 AI 助手交互

```bash
curl -X POST "http://127.0.0.1:8000/api/graph/" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "user_input": "帮我查询明天从北京到上海的航班",
    "config": {
      "configurable": {
        "passenger_id": "3442 587242",
        "thread_id": "unique-thread-id"
      }
    }
  }'
```

### 使用示例

#### 查询航班

```
用户: 帮我查一下明天从北京到上海的航班
助手: 我为您查询到以下航班信息...
```

#### 预订酒店

```
用户: 我想在上海预订一家酒店
助手: 好的，我为您找到以下酒店...
```

#### 租车服务

```
用户: 我需要租一辆车
助手: 请告诉我您的租车需求...
```

<a name="项目结构"></a>
## 📁 项目结构

```
Ctrip-Assistant/
├── main.py                 # 应用入口
├── requirements.txt        # Python 依赖
├── README.md              # 项目文档
├── AGENTS.md              # AI 开发指南
├── .gitignore             # Git 忽略文件
├── .env.example           # 环境变量模板
│
├── config/                # 配置文件
│   ├── __init__.py       # Dynaconf 设置
│   ├── development.yml   # 开发环境配置
│   ├── production.yml    # 生产环境配置
│   └── log_config.py     # 日志配置
│
├── api/                   # FastAPI 路由和模式
│   ├── routers.py        # 路由初始化
│   ├── schemas.py        # 基础模式
│   ├── system_mgt/       # 用户管理 API
│   │   ├── user_views.py
│   │   └── user_schemas.py
│   └── graph_api/        # 工作流 API
│       ├── graph_views.py
│       └── graph_schemas.py
│
├── db/                    # 数据库层
│   ├── __init__.py       # SQLAlchemy 设置
│   ├── dao.py            # 基础 DAO 类
│   └── system_mgt/       # 用户模型和 DAO
│       ├── models.py
│       └── user_dao.py
│
├── utils/                 # 工具函数
│   ├── dependencies.py   # FastAPI 依赖
│   ├── jwt_utils.py      # JWT 处理
│   ├── password_hash.py  # 密码哈希
│   ├── handler_error.py  # 错误处理
│   ├── cors.py           # CORS 配置
│   └── middlewares.py    # 中间件
│
├── graph_chat/            # LangGraph 工作流
│   ├── finally_graph.py  # 主工作流图
│   ├── state.py          # 状态定义
│   ├── assistant.py      # AI 助手节点
│   └── build_child_graph.py  # 子工作流构建
│
└── tools/                 # LangChain 工具
    ├── flights_tools.py  # 航班工具
    ├── hotels_tools.py   # 酒店工具
    ├── car_tools.py      # 租车工具
    └── trip_tools.py     # 旅行推荐工具
```

<a name="配置说明"></a>
## ⚙️ 配置说明

### 必需配置

| 配置项 | 说明 | 获取方式 |
|--------|------|----------|
| `OPENAI_API_KEY` | OpenAI API 密钥 | [OpenAI 官网](https://platform.openai.com/api-keys) |
| `TAVILY_API_KEY` | Tavily 搜索 API 密钥 | [Tavily 官网](https://tavily.com/) |
| `JWT_SECRET_KEY` | JWT 签名密钥 | 使用命令生成 |

**生成 JWT 密钥：**

```bash
python -c "import secrets; print(secrets.token_urlsafe(32))"
```

### 可选配置

| 配置项 | 说明 | 默认值 |
|--------|------|--------|
| `DATABASE` | 数据库配置 | SQLite |
| `ORIGINS` | CORS 允许的源 | `[]` |
| `WHITE_LIST` | API 白名单 | 登录注册接口 |
| `DEFAULT_PASSWORD` | 新用户默认密码 | - |

<a name="api文档"></a>
## 📚 API 文档

### 认证接口

| 方法 | 路径 | 说明 |
|------|------|------|
| POST | `/api/register/` | 用户注册 |
| POST | `/api/login/` | 用户登录 |
| POST | `/api/auth/` | OAuth2 认证 |

### 用户管理

| 方法 | 路径 | 说明 |
|------|------|------|
| GET | `/api/users/getUsers/` | 获取所有用户 |
| GET | `/api/users/{pk}/` | 获取单个用户 |
| PATCH | `/api/users/{pk}/` | 更新用户信息 |
| POST | `/api/users/delete/` | 批量删除用户 |

### AI 工作流

| 方法 | 路径 | 说明 |
|------|------|------|
| POST | `/api/graph/` | 执行 AI 工作流 |

详细 API 文档请访问：http://127.0.0.1:8000/docs

<a name="开发指南"></a>
## 💻 开发指南

### 运行测试

```bash
pytest
```

### 代码检查

```bash
# Lint 检查
ruff check .

# 类型检查
mypy .
```

### 代码规范

详见 [AGENTS.md](AGENTS.md) 文件

<a name="常见问题"></a>
## ❓ 常见问题

<details>
<summary><b>1. 如何获取 OpenAI API Key？</b></summary>

访问 https://platform.openai.com/api-keys 注册账号后创建 API Key
</details>

<details>
<summary><b>2. 数据库连接失败怎么办？</b></summary>

- 检查数据库服务是否启动
- 确认数据库配置信息正确
- 检查数据库用户权限
- 可以使用 SQLite 开发模式（无需配置）
</details>

<details>
<summary><b>3. 如何修改端口号？</b></summary>

编辑 `config/development.yml` 文件中的 `PORT` 配置项
</details>

<details>
<summary><b>4. 忘记密码怎么办？</b></summary>

直接修改数据库中的密码字段（需要哈希处理），或删除用户重新注册
</details>

<a name="贡献指南"></a>
## 🤝 贡献指南

欢迎贡献代码！请遵循以下步骤：

1. Fork 本仓库
2. 创建特性分支 (`git checkout -b feature/AmazingFeature`)
3. 提交更改 (`git commit -m 'Add some AmazingFeature'`)
4. 推送到分支 (`git push origin feature/AmazingFeature`)
5. 创建 Pull Request

<a name="许可证"></a>
## 📄 许可证

本项目采用 MIT 许可证 - 查看 [LICENSE](LICENSE) 文件了解详情

## 📮 联系方式

如有问题或建议，请创建 [Issue](https://github.com/ranger52065/Ctrip-Assistant/issues)

## 🙏 致谢

- [FastAPI](https://fastapi.tiangolo.com/) - 现代化的 Web 框架
- [LangChain](https://www.langchain.com/) - LLM 应用开发框架
- [LangGraph](https://langchain-ai.github.io/langgraph/) - 工作流编排
- [OpenAI](https://openai.com/) - GPT 模型
- [Tavily](https://tavily.com/) - 搜索 API

---

⭐ 如果这个项目对你有帮助，请给一个 Star！
