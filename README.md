# MiniCode

MiniCode 是一个面向开发者的个人 AI 编程助手项目，基于 Java 17 实现，提供类似 Claude Code 的命令行交互体验。项目围绕“让大模型可靠地理解、修改和验证代码”展开，完整实现了从单 Agent 对话到多 Agent 协作、从工具调用到 MCP 扩展、从终端交互到 Runtime API 的能力演进。

项目地址：[https://github.com/dirge2024/MiniCode](https://github.com/dirge2024/MiniCode)

## 适合简历的项目描述

> 独立设计并实现 MiniCode Java AI 编程助手：基于 ReAct、Plan-and-Execute 和 Multi-Agent 架构，抽象统一工具注册与并行执行框架；接入 MCP、RAG、长期记忆、Prompt 分层、Skill、浏览器自动化和多模型适配；通过 HITL、路径围栏、命令策略和审计日志构建工具调用安全链路，并提供 JLine TUI 与 Runtime API，支持复杂代码任务的规划、执行、验证和恢复。

## 项目亮点

- 构建 ReAct Agent、Plan-and-Execute 和 Multi-Agent 三条执行链路，支持任务拆解、依赖编排、并行执行和结果审查。
- 设计统一 ToolRegistry，内置文件读写、代码搜索、命令执行、RAG 检索、联网搜索和项目创建等工具。
- 实现 MCP 客户端能力，支持 stdio / Streamable HTTP Server、动态工具注册、resources、prompts 和运行时重连。
- 实现分层上下文工程：项目记忆、长期记忆、会话压缩、模型上下文预算、Prompt 分层组装和 Skill 按需加载。
- 构建 HITL + PathGuard + CommandGuard + AuditLog 安全链路，对文件操作、命令执行、浏览器和 MCP 工具进行分级控制与审计。
- 支持 JLine inline 流式 TUI、Lanterna 全屏 TUI、Markdown 渲染、图片输入、浏览器 CDP 登录态复用和微信 iLink 文本通道。
- 提供 SQLite 持久化的 RAG 代码索引、Side-Git 任务快照与回滚，以及可供外部系统接入的 Runtime API。

## 核心能力

### Agent 执行

- ReAct：根据模型输出循环执行工具并回灌结果。
- Plan-and-Execute：生成带依赖关系的任务 DAG，审阅通过后分批执行。
- Multi-Agent：Planner、Worker、Reviewer 分工协作，支持失败反馈和有限重试。
- 并行工具：同一轮独立工具调用最多并发执行，结果按原始调用顺序回灌。

### 代码理解与上下文

- 基于 Embedding、SQLite 和余弦相似度实现代码语义检索。
- 使用 JavaParser 分析类、方法和代码关系，支持代码索引与关系图谱。
- 支持项目级 `PAI.md`、长期记忆、对话摘要压缩和 short / balanced / long 上下文模式。
- Prompt 按 base、personality、mode、approval、runtime context、project context、skills 等层级组装。

### 工具与扩展

- 内置文件读写、目录遍历、glob、grep、Shell、项目创建、Web Search、Web Fetch 和 RAG 工具。
- MCP 支持动态工具、resources、prompts、通知监听和 server 生命周期管理。
- 支持 GLM、DeepSeek、StepFun、Kimi、FreeLLMAPI、Agnes 等 OpenAI-compatible 模型接入。
- 支持 Skill 目录化扩展，按需加载专家手册，减少系统提示词硬编码。

### 安全与工程化

- HITL 审批：支持批准、全部放行、拒绝、跳过和修改参数后执行。
- PathGuard：限制文件类操作在项目根目录内，拦截路径穿越和符号链接逃逸。
- CommandGuard：拦截高风险 Shell 片段；AuditLog 记录危险工具调用、结果和审批来源。
- Side-Git：每轮任务前后创建独立快照，支持回滚，不污染用户项目的 `.git`。
- Runtime API：提供线程、任务和事件流接口，支持后台任务持久化和恢复。

## 技术栈

`Java 17` · `Maven` · `JLine 4` · `Lanterna` · `OkHttp` · `Jackson` · `SQLite` · `JGit` · `JavaParser` · `MCP` · `SSE`

## 项目结构

```text
src/main/java/com/paicli/
├── agent/       ReAct、Plan-and-Execute、Multi-Agent
├── cli/         CLI 入口与斜杠命令解析
├── llm/         多模型客户端与统一接口
├── tool/        内置工具注册与批量执行
├── rag/         代码索引、Embedding、向量检索与关系分析
├── memory/      会话记忆、长期记忆与上下文压缩
├── prompt/      Prompt 分层组装与项目上下文
├── mcp/         MCP Client、Server 管理与 resources
├── hitl/        审批策略与交互式确认
├── policy/      路径、命令和审计策略
├── render/      inline / lanterna / plain 渲染器
├── snapshot/    Side-Git 快照与回滚
└── runtime/     后台任务与 Runtime API
```

## 快速开始

环境要求：Java 17+、Maven，以及至少一个模型 API Key。

```bash
git clone https://github.com/dirge2024/MiniCode.git
cd MiniCode
cp .env.example .env
mvn clean package
java -jar target/paicli-1.0-SNAPSHOT.jar
```

可配置的 API Key 包括 `GLM_API_KEY`、`DEEPSEEK_API_KEY`、`STEP_API_KEY`、`KIMI_API_KEY`、`FREELLMAPI_API_KEY` 和 `AGNES_API_KEY`。

## 常用命令

```text
/plan <任务>                  规划并执行复杂任务
/team <任务>                  启动 Multi-Agent 协作
/model <provider>             切换模型或 provider
/index [路径]                 建立代码索引
/search <查询>                语义检索代码
/mcp                          查看 MCP 状态
/memory                       查看记忆状态
/snapshot                     查看任务快照
/context                      查看上下文预算
/exit                         退出 MiniCode
```

## 测试

```bash
mvn test -Pquick
mvn test -Pphase16-smoke
mvn test -DskipTests=false
```

## 项目说明

MiniCode 是个人独立开发项目，重点关注 Agent 执行可靠性、上下文管理、工具安全和终端产品化体验。项目中的 `com.paicli` 包名和 `paicli` Maven 产物名称是历史兼容信息，不影响项目名称为 MiniCode。
