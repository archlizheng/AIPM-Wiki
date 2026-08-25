---
title: agent-vs-chatbot
type: note
permalink: aipmwiki/docs/04-interview/basics/agent-vs-chatbot
---

# 什么是 Agent?它和 Chatbot 的区别是什么?

**难度**:⭐⭐ 入门必备
**出现频率**:极高,字节多轮面经、小红书 prep 帖、大厂常见题均有出现

## 考察点

- 能否准确描述 Agent 的核心特征(感知、规划、行动)
- 是否理解 Chatbot 是 Agent 的子集或简化形态
- 能否从产品视角判断何时需要 Agent,何时 Chatbot 就够

## 参考答案

**一句话对比**:Chatbot 是"能聊天的机器人",Agent 是"能自主完成任务的智能体"。

| 维度 | Chatbot | Agent |
|------|---------|-------|
| 核心能力 | 理解问题 → 生成回答 | 理解目标 → 规划步骤 → 调用工具 → 执行 → 反馈 |
| 交互模式 | 单轮/多轮对话 | 多步骤任务编排,可能跨多个工具 |
| 自主性 | 低,等用户提问 | 高,可主动规划和执行子任务 |
| 工具使用 | 通常不具备 | 核心能力(搜索、计算、API 调用等) |
| 典型场景 | 客服问答、闲聊、信息查询 | 自动化办公、数据分析、复杂流程执行 |

**Agent 的关键组成**:
1. **规划(Planning)**:把复杂目标拆解为可执行步骤
2. **工具调用(Tool Use)**:调用外部 API、数据库、搜索引擎等
3. **记忆(Memory)**:跨步骤保持上下文和中间结果
4. **反思(Reflection)**:检查执行结果,必要时调整策略

**选型思路**:
- 用户只是"问问题得答案" → Chatbot 足够
- 用户要"完成一件事"(订机票、写报告、分析数据) → 需要 Agent
- 很多产品是渐进式演进:从 Chatbot 开始,逐步增加工具调用能力

## 追问延伸

- **追问 1**:"Agent 和普通工作流(Workflow)有什么区别?"
  → 工作流步骤固定、按预设路径执行;Agent 能根据中间结果动态调整计划,灵活性更高但可控性更低。详见 [什么是 Agentic Workflows?](agentic-workflows.md)。
- **追问 2**:"设计 Agent 产品时最大的风险是什么?"
  → 失控风险(执行了不该执行的操作)、成本不可控(多轮工具调用)、错误累积(一步错步步错)。路由选错与超大输入的接法见 [Agent 路由与超大文件](agent-routing-and-large-files.md)。
- **追问 3**:"你怎么判断一个场景适不适合做 Agent?"
  → 看任务是否多步骤、是否需要调用外部工具、是否有明确的完成标准;简单问答不适合过度工程化。

## 相关阅读

- [什么是 Agentic Workflows?](agentic-workflows.md)
- [Agent 路由选错了怎么办?超大文件又该怎么接?](agent-routing-and-large-files.md)
- [Skill 和 Rules 到底有什么区别?](skill-vs-rules.md)
- [RAG 和微调有什么区别?什么场景该用哪个?](rag-vs-finetuning.md)
- [什么是 RAG:检索增强生成入门](../../01-ai-basics/llm/what-is-rag.md)
- [AI 术语速查表](../../01-ai-basics/glossary.md)