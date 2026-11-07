好的，项目方向正式确定。

从现在开始，我们不再纠结 A、B、C，也不同时启动多个项目。DevMate AI 将作为你的主项目，从需求分析、架构设计、后端开发、前端开发，一直到 RAG、Agent、评测、部署，完整地走一遍 AI 应用工程的开发流程。

我会把自己定位为你的项目技术导师：不仅告诉你代码怎么写，还会解释为什么这样设计、模块之间如何协作，以及实际开发中可能遇到什么问题。

# 一、最终项目方案：DevMate AI

![AI Internal Knowledge Search by Mohammed TAYOR on Dribbble](https://images.openai.com/static-rsc-4/iZ1L9Tc8KeaT7-qeIWqWFGGQVij85_1OxIP4F01HypErxUeTBuf18nggOks2FH4vsEq3CynBk5Jj8i6HBqSr6shwh0inLE4O2vMTxJ9PGYypzFTIw-ZhlAuRHnnBz97CtTYf7fgvRbIYJD-r0uXKrlH4nUl0RpCY1cWdru3GIAM2fihcYPoOZYr-umWnPnGs?purpose=fullsize)https://dribbble.com/shots/27379062-AI-Internal-Knowledge-Search?utm_source=chatgpt.com)

# DevMate AI

企业级智能知识库与研发协作助手

主项目已确定

FastAPI + Vue

本地优先

Python + uv

项目目标：构建一个可以真正运行、交互、演示，并且能够持续扩展的 AI 应用系统。

## 1.1 项目要解决什么问题？

假设一个研发团队有这些资料：

- 项目技术文档、架构设计文档。
- API 接口文档、数据库设计文档。
- 开发规范、部署手册。
- Bug 记录、技术问题解决方案。
- 项目运行状态、任务和缺陷数据。

这些资料分散在不同文件和系统中，开发人员经常需要花费时间查找信息。

DevMate AI 希望提供一个统一入口，让研发人员通过自然语言完成信息查询和研发辅助工作。

例如：

| 用户提出的问题                   | 系统应该如何处理                  |
| -------------------------------- | --------------------------------- |
| 项目使用什么认证机制？           | RAG 检索技术文档                  |
| JWT 的有效期是多少？             | 检索相关配置文档并引用证据        |
| 当前有多少未解决的高优先级 Bug？ | 调用 SQL 或业务 API               |
| 帮我整理项目的技术架构           | 检索资料、分析信息、生成 Markdown |
| 这个问题在项目文档里没有答案吗？ | 判断证据是否充分，不允许凭空编造  |

这里有一个非常重要的设计思想：

不同类型的问题应该交给不同的数据源和处理方式，而不是把所有问题都交给 RAG。

这会成为我们后续学习 AI 应用架构的核心实践之一。

## 1.2 最终技术架构

我们采用分层架构，先建立清晰的模块边界，再逐步实现。

![Production RAG: Learning from Scratch Done Right - by Priya](https://images.openai.com/static-rsc-4/1u-ggmINkvIWVrVCvVtCKMnj4N5-hFVcQQEYYYFvTL2wXerQutWZvHQ0E6VF-KMTccprEm2tByTYOrRbRAZrXjtM2-eh5CYx94iun2Kl6c7oLKH6YOp30ZN4cjRvuYkziA1Cv-Ts_uTacWb3YDNTyIwOg5Pu3RkU1gGnN9n4aSQ?purpose=inline)https://www.decodingai.com/p/production-rag-from-scratch-senior-architect-guide?action=share&utm_source=chatgpt.com)

整体结构：

```
                    用户
                     │
                     ▼
           Vue 3 + TypeScript
              前端交互层
                     │
                 HTTP API
                     │
                     ▼
              FastAPI 后端
                     │
          ┌──────────┴──────────┐
          │                     │
          ▼                     ▼
     业务应用层              AI 应用层
   Application Service         │
          │          ┌─────────┼─────────┐
          │          │         │         │
          │          ▼         ▼         ▼
          │         RAG    Tool Calling  Agent
          │          │         │         │
          │          ▼         ▼         ▼
          │       Retriever  SQL/API   Workflow
          │          │
          │          ▼
          │       Generator
          │
          └──────────┬──────────┘
                     │
          ┌──────────┼──────────┐
          ▼          ▼          ▼
      PostgreSQL   Qdrant     LLM API
       业务数据    向量数据    模型服务
```

注意：这张图是项目的目标架构，不代表第一天就要把所有模块实现。我们会从最简单的可运行系统开始。

## 1.3 技术选型最终确定

| 技术               | 在项目中的职责                   | 开发阶段 |
| ------------------ | -------------------------------- | -------- |
| Python + uv        | 后端语言与依赖管理               | 第一阶段 |
| FastAPI            | 后端 API 服务                    | 第一阶段 |
| Vue 3 + TypeScript | 前端界面                         | 第一阶段 |
| PostgreSQL         | 用户、会话、文档元数据、业务数据 | 第二阶段 |
| Qdrant             | 向量存储和检索                   | RAG 阶段 |
| Embedding API      | 文本向量化                       | RAG 阶段 |
| 现有模型 API       | LLM 推理                         | 第一阶段 |
| Pydantic           | 数据结构与参数校验               | 第一阶段 |
| LangChain          | 模型、检索等组件集成             | 按需引入 |
| LangGraph          | 多步骤 Agent 工作流              | 后期     |
| pytest             | 自动化测试                       | 持续进行 |
| Golden Set         | 固定问题集回归评测               | 后期     |
| LLM-as-Judge       | 辅助评价模型输出                 | 后期     |
| Langfuse           | Trace、延迟、Token 和成本观测    | 后期     |
| Docker Compose     | 本地服务编排及后续部署           | 后期     |

不急着把所有技术都装进项目。每引入一个组件，都必须有明确的业务需求。

# 二、MVP 功能边界

我们先定义一个真正能完成核心业务闭环的 MVP。

## 2.1 第一版必须实现的功能

MVP 核心功能

第一版目标

1. 文档管理

   上传 PDF、DOCX、Markdown，查看文档列表和基本信息。

2. 文档处理

   提取文本、清洗内容、切分 Chunk、生成 Embedding。

3. 知识库检索

   将向量存入 Qdrant，支持 Top-K 检索。

4. RAG 问答

   根据检索结果构造 Prompt，调用模型 API 生成答案。

5. 来源引用

   展示回答所依据的文档名称、片段等信息。

6. 无答案处理

   证据不足时拒绝回答，不让模型自由编造。

7. 聊天界面

   支持用户提问、展示答案、查看引用来源。

## 2.2 第一版暂时不做的功能

| 功能                | 暂缓原因                       |
| ------------------- | ------------------------------ |
| 多 Agent 协作       | 先理解单一流程和工具调用       |
| MCP / A2A           | 先掌握基础工具集成             |
| 自然语言转 SQL      | 需要先建立可靠的数据查询层     |
| 复杂 Semantic Layer | 先明确业务数据和指标定义       |
| 多租户和复杂权限    | 第一版先做单用户或简单用户体系 |
| Kubernetes          | 对当前本地 MVP 没有必要        |
| 自动修改代码        | 不属于第一版的核心价值         |

这些不是不重要，而是我们要避免在核心 RAG 闭环还没跑通时，就引入过多工程复杂度。

# 三、完整开发路线

整个项目拆成八个阶段，每个阶段都有明确的交付物。

1. 阶段 1：项目初始化与基础工程

   现在开始

   - 开发环境检查
   - 项目目录设计
   - uv 初始化 Python 后端
   - FastAPI 基础服务
   - Vue 3 前端初始化
   - 前后端联调

   交付：浏览器可以访问前端，前端可以请求后端 API。

2. 阶段 2：数据库与基础业务模块

   - PostgreSQL 安装与连接
   - SQLAlchemy / 数据库模型
   - 文档元数据管理
   - 文件上传与存储
   - API Schema 与 Repository 分层

   交付：可以上传、管理和查询文档。

3. 阶段 3：文档解析与知识库构建

   - PDF / DOCX / Markdown 解析
   - Chunking 策略
   - Metadata 设计
   - Embedding
   - Qdrant 入库

   交付：文档能够被转换为可检索的知识数据。

4. 阶段 4：RAG 核心链路

   - Query 处理
   - Dense Retrieval
   - Top-K 检索
   - Prompt 构建
   - LLM 生成
   - 来源引用
   - 无答案拒答

   交付：真正可用的知识库问答系统。

5. 阶段 5：RAG 优化与评测

   - Hybrid Search
   - Reranker
   - Query Rewrite
   - Golden Set
   - Recall@K、MRR、Faithfulness 等指标
   - LLM-as-Judge

   交付：能够评估和改进系统，而不是只凭感觉判断回答质量。

6. 阶段 6：工具调用与 Agent

   - Function Calling
   - Pydantic 参数校验
   - SQL / API 工具
   - 工具权限和错误处理
   - LangGraph 工作流
   - 状态管理与人工确认

   交付：从知识问答扩展到能够执行受控研发任务的助手。

7. 阶段 7：工程化与可观测性

   - 日志与异常处理
   - Trace
   - Token、延迟和成本统计
   - 权限控制
   - pytest 集成测试

   交付：具备可调试、可测试、可维护的工程基础。

8. 阶段 8：部署与 GitHub 项目完善

   - Docker Compose
   - 配置管理
   - README
   - 架构图
   - 演示数据
   - 测试说明
   - GitHub Actions

   交付：可以在其他机器上按照文档启动，并向别人完整演示。

# 四、项目目录规划

这是最终逐步演进的目标目录。我们不会现在就创建所有目录，而是在对应阶段按实际需要建立。

```
DevMate-AI/
│
├── backend/
│   ├── app/
│   │   ├── api/              # API 接口
│   │   │   └── v1/
│   │   ├── core/             # 配置、日志、异常
│   │   ├── schemas/          # Pydantic 数据结构
│   │   ├── models/           # 数据库模型
│   │   ├── services/         # 业务服务
│   │   ├── repositories/     # 数据访问
│   │   ├── database/         # 数据库连接
│   │   ├── ingestion/        # 文档处理
│   │   ├── rag/              # RAG 核心
│   │   ├── agent/            # Agent 与工具
│   │   └── main.py
│   ├── tests/
│   ├── pyproject.toml
│   └── uv.lock
│
├── frontend/
│   └── Vue 3 + TypeScript
│
├── evaluation/               # 评测数据和脚本
├── data/
│   └── demo/                 # 演示资料
├── docs/                     # 架构和技术文档
│
├── docker-compose.yml
├── .env.example
├── .gitignore
└── README.md
```

# 五、正式开发：第一阶段任务

我们现在进入阶段 1。

这一阶段不直接写 RAG，也不安装 LangChain。先把项目的基础开发环境和工程结构建立起来。

| 任务 | 内容         | 验收标准                        |
| ---- | ------------ | ------------------------------- |
| 1.1  | 检查开发环境 | Python、uv、Node、npm、Git 可用 |
| 1.2  | 创建项目目录 | 根目录创建成功                  |
| 1.3  | 初始化后端   | uv 项目可运行                   |
| 1.4  | 搭建 FastAPI | 后端健康检查接口正常            |
| 1.5  | 初始化 Vue   | 前端页面可以运行                |
| 1.6  | 前后端联调   | Vue 能请求 FastAPI 并展示结果   |
| 1.7  | Git 初始化   | 完成第一次项目提交              |

## 现在只做第一步：检查开发环境

你之前主要使用 Windows、PowerShell、Python 和 uv，所以我们继续沿用这个开发环境，不额外增加操作系统切换的复杂度。

打开 PowerShell，依次执行下面的命令：

```
python --version
uv --version
node --version
npm --version
git --version
```

建议使用：

- Python：3.12 或更高版本。
- uv：能够正常运行即可。
- Node.js：22 LTS 或更高的兼容 LTS 版本。
- npm：随 Node.js 安装。
- Git：能够正常运行。

然后创建项目根目录：

```
cd D:\code\github-clong

New-Item -ItemType Directory -Path DevMate-AI -Force

cd DevMate-AI

Get-Location
```

本步骤验收标准

- Python 版本能够正常显示。
- uv 版本能够正常显示。
- Node.js 和 npm 能够正常显示。
- Git 能够正常显示。
- `DevMate-AI` 项目目录创建成功。

现在不需要安装任何依赖，也不要提前创建 FastAPI、Vue 或数据库。

完成后，把 PowerShell 的完整执行结果发给我。

我会根据你的实际环境判断是否需要调整版本，然后带你完成 1.2 项目工程初始化。我们一次只推进一个步骤，每一步都解释设计原因、具体操作和验收标准。

这次我们的目标不是简单地“跑通一个 AI Demo”，而是最终做出一个你能独立讲清楚架构、理解核心代码、展示实际效果，并且敢于放到 GitHub 上的完整项目。