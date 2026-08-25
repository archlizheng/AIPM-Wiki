---
title: mcp
type: note
permalink: aipmwiki/docs/01-ai-basics/llm/mcp
---

# MCP:给 Agent 接上外部世界的「USB-C」

> 时效说明:协议角色与三大原语相对稳定;公开服务器数量、厂商目录与企业鉴权能力截至 **2026-07**,请以 [modelcontextprotocol.io](https://modelcontextprotocol.io/) 与各厂商文档为准。

## 一句话说清

**MCP(Model Context Protocol,模型上下文协议)** 是一套开放标准,用来把 AI 应用(Claude、ChatGPT、Cursor、VS Code……)和外部系统(文件、数据库、SaaS、内部 API)用**同一种方式**连起来。官方文档的类比是:**USB-C for AI**——设备(Agent)和配件(工具/数据)各自实现标准接口,而不是每对组合都焊一根定制线。

它解决的是 Anthropic 所说的 **N×M 集成问题**:N 个 Agent × M 个数据源,若每对都写专用连接器,生态无法扩展;有了 MCP,理想情况变成 **N+M**(Agent 做客户端能力,系统做服务器能力)。

## 起源与发展

| 时间 | 事件 | 对产品的含义 |
|------|------|--------------|
| **2024-11-25** | Anthropic 开源发布 MCP(规范、SDK、Claude Desktop 本地服务器支持、参考服务器仓库) | 「接公司数据」从各厂私有插件,走向可共享的协议层 |
| **2025** | ChatGPT、Cursor、Gemini、Microsoft Copilot、VS Code 等广泛采纳;云厂商提供部署与托管能力;社区 Registry、连接器目录出现 | 选型时开始问「有没有现成 MCP」,而不是「要不要自研一套插件协议」 |
| **2025-12-09** | Anthropic 将 MCP 捐赠给 Linux 基金会下的 **Agentic AI Foundation(AAIF)**(与 Block、OpenAI 共同发起,Google、Microsoft、AWS、Cloudflare、Bloomberg 等支持) | 治理中立化,降低「绑死一家模型厂」的顾虑 |
| **2026** | 远程 MCP、企业鉴权、异步调用等能力持续演进;新闻与产品侧出现更多托管/远程服务器场景 | 从「本机插一个本地服务器」走向可规模化的企业连接 |

捐赠公告中的生态快照(2025-12,属高时效数字,仅作量级参考):公开活跃 MCP 服务器逾万、主流 AI 产品已接入、Python/TypeScript SDK 月下载量达数千万级。具体数字会变,趋势比数字更重要:**连接层正在标准化**。

设计灵感常被拿来和 **LSP(Language Server Protocol)** 类比:当年编辑器与语言服务解耦,今天 Agent 与工具/数据源解耦。

## 架构:Host / Client / Server

不必背 JSON-RPC 细节,先分清三个角色:

| 角色 | 是什么 | 例子 |
|------|--------|------|
| **MCP Host** | 使用 AI 的应用程序,负责协调 | Claude Desktop、Cursor、VS Code、ChatGPT |
| **MCP Client** | Host 内部、与**某一个** Server 保持连接的组件 | Host 连 Sentry 时实例化一个 Client,再连文件系统时再实例化一个 |
| **MCP Server** | 对外暴露能力的程序(本地或远程) | 本地 filesystem 服务器、远程 Sentry / 公司内部知识库服务器 |

```
用户 ↔ MCP Host(AI 应用)
          ├─ MCP Client 1 ── STDIO / HTTP ──► MCP Server A(本地文件)
          ├─ MCP Client 2 ─────────────────► MCP Server B(数据库)
          └─ MCP Client 3 ─────────────────► MCP Server C(远程 SaaS)
```

协议分两层理解即可:

- **数据层**:基于 JSON-RPC 2.0 的消息语义——生命周期、工具调用、资源读取等。
- **传输层**:怎么把消息送过去——常见是本地 **STDIO**(适合本机进程)和远程 **Streamable HTTP**(适合多客户端连同一远程服务)。

MCP **只规定上下文与工具如何交换**,不规定你怎么选模型、怎么做 Agent 循环——那是 Host 自己的事。

## 三大原语:Tools / Resources / Prompts

服务器侧最重要的是三类能力(官方称 primitives):

| 原语 | 做什么 | 谁更主导 | PM 直觉 |
|------|--------|----------|---------|
| **Tools** | 可执行动作:查库、发消息、改文件、调 API | 模型按需决定是否调用 | 「能办事」的按钮;通常要鉴权与确认 |
| **Resources** | 可读上下文:文档、schema、日历、知识片段 | 应用侧组织进上下文 | 「给模型看的材料」,偏只读 |
| **Prompts** | 预置交互模板,引导如何组合工具与资源 | 用户/产品触发 | 「一键工作流入口」,降低提示词门槛 |

客户端侧还有 **Sampling** 等能力(服务器可请求 Host 侧模型补全),让服务器作者不必内嵌某个厂商的模型 SDK——了解即可,面试一般问到 Tools/Resources 就够。

一次典型调用可以想成:

1. Client 向 Server `tools/list`,拿到工具名、描述与输入 schema  
2. Host 把工具列表交给模型  
3. 模型决定调用某个工具 → Client `tools/call`  
4. Server 执行并返回结果 → Host 把结果写回对话,模型继续推理  

这和 [Function Calling](agent-basics.md) 是同一类「模型调工具」故事;**MCP 把「工具从哪来、怎么发现、怎么连」标准化了**,而不是取代 Function Calling 本身。

## 和 Skills、Function Calling 怎么分工

| | **Function Calling / Tools** | **MCP** | **Agent Skills** |
|--|------------------------------|---------|------------------|
| 层级 | 模型输出结构化调用的能力 | Agent ↔ 外部系统的**连接协议** | 按需加载的**操作手册** |
| 解决的问题 | 「这一步调哪个函数」 | 「函数/数据从哪接、如何复用」 | 「这类任务按什么规范做」 |
| 类比 | 会按开关 | USB-C 与线材标准 | 入职手册 / SOP |

可以记:**Skills 规定怎么做,MCP 接到外部世界,Function Calling 是模型按下开关的那一下。** 三者叠用,而不是三选一。Skills 与 MCP 的详细对照见 [Agent Skills](agent-skills.md)。

社区里偶有「Skills 是否取代 MCP」的争论——对 PM 更有用的结论是:**本地剧本替代不了带鉴权的实时系统连接**;反过来,光有连接没有规范,Agent 仍会「接得上却做不对」。

## 产品价值与边界

**值得上 MCP 的信号**

- 多个 Agent / IDE / 助手都要碰同一批内部系统  
- 希望供应商生态(官方/社区服务器)降低自研连接器成本  
- 需要把「读上下文」和「执行动作」分开治理(Resources vs Tools)

**要注意的边界**

- **工具爆炸**:一次挂载过多 Server,工具列表挤占上下文、拖慢决策——要做目录治理、按场景启用,而不是「能接尽接」。  
- **权限与安全**:Tools 可以写数据、调外部 API;远程 Server、OAuth/企业鉴权、人工确认高风险动作,是产品必选项。  
- **延迟与确定性**:每次调用可能是网络往返;结果质量取决于 Server 实现,协议本身不保证业务正确。  
- **维护责任**:接了别人的 Server,就要跟对方的版本、配额与故障模式——和接任何第三方 API 一样。

## 给 AI PM 的启示

1. **立项话术**:别只说「我们要做 Agent」,要说清「Agent 要碰哪些系统、哪些走现成 MCP、哪些必须自建 Server」。  
2. **能力地图**:把需求拆成 Resources(只读上下文)vs Tools(有副作用动作),分别设计权限与确认策略。  
3. **生态优先**:先查官方连接器目录与 MCP Registry,再评估自研;自研时按 MCP 暴露,避免做成只能在一个 Host 里用的私有插件。  
4. **与 Skills 打包交付**:对外连接用 MCP,对内规范用 Skills——例如「按公司报销流程填单」= Skill 写步骤 + MCP 读日历/写表格。  
5. **治理当成功能**:谁能安装 Server、默认开哪些、审计日志怎么记——这些是企业客户买单点,不是上线后的边角料。

## 相关阅读

- B站:[MCP 终极指南](https://www.bilibili.com/video/BV1uronYREWR)（马克的技术工作坊，~24 万播放）
- B站:[Function Calling、MCP 和 A2A 的核心原理与区别](https://www.bilibili.com/video/BV1XFhPzoEBx)（技术蛋老师，~5.5 万播放）
- B站:[一口气拆穿 Skill/MCP/RAG/Agent 底层逻辑](https://www.bilibili.com/video/BV1ojfDBSEPv)（飞天闪客，~88 万播放）
- [Agent 入门:规划、记忆、工具调用](agent-basics.md)
- [Agent Skills:给 Agent 装上可复用的「操作手册」](agent-skills.md)
- [什么是 Agent?它和 Chatbot 的区别是什么?](../../04-interview/basics/agent-vs-chatbot.md)
- [海信集团 AI 产品校招面经](../../04-interview/experiences/hisense-ai-pm-202607.md)
- [AI 术语速查表](../glossary.md)

## 参考资料

- [Introducing the Model Context Protocol](https://www.anthropic.com/news/model-context-protocol) — Anthropic,2024-11-25
- [Donating the Model Context Protocol and establishing the Agentic AI Foundation](https://www.anthropic.com/news/donating-the-model-context-protocol-and-establishing-of-the-agentic-ai-foundation) — Anthropic,2025-12-09
- [What is the Model Context Protocol (MCP)?](https://modelcontextprotocol.io/docs/getting-started/intro) — 官方文档,2026-07 访问
- [Architecture overview](https://modelcontextprotocol.io/docs/learn/architecture) — 官方文档,2026-07 访问
- [Understanding MCP servers](https://modelcontextprotocol.io/docs/learn/server-concepts) — 官方文档,2026-07 访问
- [Building MCP servers for ChatGPT Apps and API integrations](https://developers.openai.com/api/docs/mcp) — OpenAI 开发者文档,2026-07 访问
- [Model Context Protocol](https://en.wikipedia.org/wiki/Model_Context_Protocol) — Wikipedia,2026-07 访问