---
title: README
type: note
permalink: aipmwiki/docs/01-ai-basics/llm/readme
---

# 大语言模型(LLM)

AI PM 的核心必修课:大模型的原理、能力边界、应用架构。

## 目录

**原理篇**

- [大模型是怎么"想"的:预测下一个 token 的直观解释](how-llm-works.md)
- [Transformer 架构:大模型的地基(PM 版)](transformer-architecture.md)
- [上下文窗口:限制、成本与产品设计影响](context-window.md)
- [幻觉问题:成因与产品层面的缓解手段](hallucination.md)

**应用篇**

- [什么是 RAG:检索增强生成入门](what-is-rag.md)
- [RAG 没死:死的是 Naive 固定流水线](rag-evolution-naive-to-agentic.md)
- [长上下文 vs RAG:什么时候塞全库,什么时候检索](long-context-vs-rag.md)
- [Agentic Retrieval:检索如何变成 Agent 的工具](agentic-retrieval.md)
- [Agent-search 与 Search as Code:搜索栈如何可编程](agentic-search-search-as-code.md)
- [工业级 RAG 清单:混合检索、重排、评测与知识治理](production-rag-checklist.md) —— 另含矛盾文档、隐性知识、「相关但没用」归因
- [NL2SQL:自然语言查库,为什么学术高分上不了生产](nl2sql-for-ai-pm.md)
- [Prompt / RAG / 微调:三种让模型更懂你的方式怎么选](prompt-rag-finetuning.md)
- [Agent 入门:规划、记忆、工具调用](agent-basics.md)
- [MCP:给 Agent 接上外部世界的「USB-C」](mcp.md)
- [Agent Skills:给 Agent 装上可复用的「操作手册」](agent-skills.md)
- [主流大模型盘点与选型思路(2026 年中)](model-landscape.md)

## 阅读建议

零基础按"原理篇 → 应用篇"顺序读。若正在做知识库/检索选型,建议路径:`what-is-rag` → `rag-evolution-naive-to-agentic` → `long-context-vs-rag` → `agentic-retrieval` →(`nl2sql-for-ai-pm` 或 `production-rag-checklist`)。模型盘点一文时效性强,选型前务必核对官方最新数据。