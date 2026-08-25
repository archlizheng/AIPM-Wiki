---
title: agent-basics
type: note
permalink: aipmwiki/docs/01-ai-basics/llm/agent-basics
---

# Agent 入门:规划、记忆、工具调用

2024-2025 年"Agent"还带着概念炒作的味道;到 2026 年年中,它已是日常工具——写代码用 Claude Code、Codex、Antigravity,做调研用 Deep Research,订票、填表单也开始交给能操作电脑的 Agent。理解 Agent 不是为了背定义,而是因为"这个功能该做成 Chatbot 还是 Agent"是几乎所有 AI 产品立项都要过的一道坎。

## 与 Chatbot 的本质区别

速答版见 [什么是 Agent?它和 Chatbot 的区别是什么?](../../04-interview/basics/agent-vs-chatbot.md)。更根本的视角:**Chatbot 是"一次 LLM 调用"的产品化,Agent 是"多次 LLM 调用 + 工具执行"组成的循环系统。** Chatbot 路径很短:提问 → 生成回答 → 结束。Agent 是一个循环:模型不仅生成语言,还要决定下一步做什么——直接回答,还是先调用工具、拿到结果后再判断是否继续,直到任务完成。

Anthropic 在《Building Effective Agents》中给出关键区分:**工作流是通过预定义代码路径编排 LLM 和工具的系统,Agent 则是 LLM 动态指挥自己的流程和工具使用、自主掌控完成方式的系统。** 工作流每一步是人写死的,Agent 每一步是模型自己决定的,这也是它更灵活但更难预测的原因。多数上线产品其实是两者的混合体。

## 核心组件

- **规划(Planning)**:把模糊目标拆解成可执行步骤,通常"边执行边根据新信息调整计划",而非一次性想全。
- **记忆(Memory)**:短期记忆(当前任务的中间结果,放在上下文窗口里)+ 长期记忆(跨会话的用户偏好、历史经验,存在外部库里按需检索,机制上与 RAG 相通)。
- **工具调用(Tool Use)**:调用外部函数、API、代码执行环境,把"知道什么"扩展为"能做什么",是 Agent 区别于 Chatbot 最直观的能力。
- **环境反馈(Observation)**:执行后观察结果是否符合预期,据此决定继续、重试还是调整计划——这是 Agent 处理不确定性任务的关键,也是可靠性风险最集中的地方。

```
用户目标 → 规划拆解步骤 → 调用工具执行 → 观察结果
                ↑                              │
                └── 未完成,更新记忆、重新规划 ──┘
                              │
                             完成 → 返回结果
```

## 2026 年中的主流 Agent 形态

- **编码 Agent**:落地最成熟的品类,如 Claude Code(终端起步,现有桌面端)、OpenAI Codex(独立桌面端 / CLI / 云端异步)、Google Antigravity(2.0 起以独立桌面端做多 Agent 编排,另有 CLI 与可选 IDE)。任务边界明确、有客观验证标准(测试是否通过),是自主性风险最可控的场景。
- **电脑操作 Agent(Computer Use)**:让模型直接看屏幕、操作浏览器/桌面应用完成任务。可靠性是最大挑战,产品普遍在支付、发送、删除等关键动作前设人工确认。
- **深度研究 Agent(Deep Research)**:自主完成多轮搜索、阅读、交叉验证,产出带引用的报告,单次任务可自主运行数十分钟。
- **多 Agent 协作**:复杂任务拆给多个专精子 Agent 并行处理再汇总,是 2026 年的另一重要方向。

## 工具生态:MCP 与 Skills

给 Agent 接入新工具曾是"N 个 Agent 对接 M 个工具"的重复开发难题。Anthropic 2024 年底推出的 **MCP(Model Context Protocol)** 把它标准化为"N+M":任意支持 MCP 的 Agent 都能连接任意支持 MCP 的工具。此后 MCP 被主流 AI 产品广泛采纳,并于 2025 年底捐赠给 Linux 基金会下的 Agentic AI Foundation 中立治理。对 PM 而言,这意味着"给 Agent 接入新能力"很多时候变成了配置现成 MCP 服务器,而非专项开发——机制、原语与选型见 [MCP 专文](mcp.md)。

与之互补的是 **Agent Skills**:用可版本管理的文件夹打包「这类任务该怎么做」的程序性知识,按需加载进上下文。MCP 负责接到外部世界,Skills 负责沉淀操作手册——详见 [Agent Skills 一文](agent-skills.md)。

## 失败模式与设计考量

- **失控风险**:执行了不该执行的操作——对高风险动作设人工确认关卡。
- **错误累积**:前面步骤的小错误在长链路里被放大——限制最大步骤数、关键节点加校验。
- **成本不可控**:多轮工具调用的成本和延迟远高于单轮问答且难预估——设工具调用预算上限,简单任务走固定流程。
- **过度工程化**:不是所有场景都需要 Agent。先找最简单的方案,很多时候一次优化好的单轮调用(配检索和示例)就够了,判断标准可参考 [Agentic Workflows 的适用边界](../../04-interview/basics/agentic-workflows.md)。
- **可观测性缺失**:多步骤决策链条一旦出错很难排查,需要完整的执行日志和轨迹追踪("Agent Ops")。

## 给 AI PM 的判断框架

1. 任务是否天然多步骤、需要调用外部工具?——否则不需要 Agent。
2. 每一步能否被验证?——有明确验证标准的任务,自主性风险更可控。
3. 出错代价多大?——不可逆、高代价操作必须设人工确认。
4. 能否接受成本和延迟的不确定性?——多轮工具调用意味着两者都更难预估。

## 相关阅读

- B站:[从 LLM 到 Agent Skill](https://www.bilibili.com/video/BV1E7wtzaEdq)（马克的技术工作坊，~135 万播放）— 一条线串起 Agent 核心概念
- B站:[Agent、Skill、Harness 一次讲明白](https://www.bilibili.com/video/BV1YRG46eE1n)（通义实验室，~21 万播放）
- B站:[一口气拆穿 Skill/MCP/RAG/Agent 底层逻辑](https://www.bilibili.com/video/BV1ojfDBSEPv)（飞天闪客，~88 万播放）
- [Context Engineering：从写 Prompt 到拼上下文系统](../prompt-engineering/context-engineering.md)
- [MCP:给 Agent 接上外部世界的「USB-C」](mcp.md)
- [Agent Skills:给 Agent 装上可复用的「操作手册」](agent-skills.md)
- [什么是 Agent?它和 Chatbot 的区别是什么?](../../04-interview/basics/agent-vs-chatbot.md)
- [什么是 Agentic Workflows?](../../04-interview/basics/agentic-workflows.md)
- [什么是 RAG:检索增强生成入门](what-is-rag.md)
- [Agentic Retrieval:检索如何变成 Agent 的工具](agentic-retrieval.md)
- [Agent-search 与 Search as Code:搜索栈如何可编程](agentic-search-search-as-code.md)
- [AI 术语速查表](../glossary.md)

## 参考资料

- [Building Effective Agents](https://www.anthropic.com/research/building-effective-agents) — Anthropic 工程博客,2026-07 访问
- [A practical guide to building agents](https://openai.com/business/guides-and-resources/a-practical-guide-to-building-ai-agents/) — OpenAI 官方指南,2026-07 访问
- [Introducing the Model Context Protocol](https://www.anthropic.com/news/model-context-protocol) — Anthropic 官方公告,2026-07 访问
- [Donating the Model Context Protocol and establishing the Agentic AI Foundation](https://www.anthropic.com/news/donating-the-model-context-protocol-and-establishing-of-the-agentic-ai-foundation) — Anthropic 官方公告,2026-07 访问
- [Agents Companion](https://www.kaggle.com/whitepaper-agent-companion) — Google 官方白皮书(第二辑),2026-07 访问