---
title: what-is-context-engineering
type: note
permalink: aipmwiki/docs/04-interview/basics/what-is-context-engineering
---

# 什么是 Context Engineering？和 Prompt Engineering 有什么区别？

**难度**:⭐⭐ 入门必备
**出现频率**:高,2025 年后 Agent / 长任务场景面试常见

> 选题启发自磊叔《关于 AI 产品经理的 100 个问题》Q4 / Q10；正文为本库原创，未摘录该 PDF 答案原文。

## 考察点

- 能否说清 Context Engineering 管的是「窗口里装什么、怎么持续维护」，而不只是「指令怎么写」
- 能否用对比表区分 Prompt Engineering 与 Context Engineering 的对象、时间尺度与失败形态
- 能否落到 PM 抓手：上下文清单与优先级、成本/延迟预算、失败归因（指令问题 vs 材料/历史/工具结果问题）

## 参考答案

**一句话**：Prompt Engineering 管「怎么吩咐模型」；Context Engineering 管「这一次推理里窗口里到底有什么、以及这套状态怎么随轮次更新」——后者是前者的自然延伸，而不是换个名字的同义词。

厂商口径（Anthropic）：Context Engineering 是在推理时**策展并维持最优 token 集合**的策略集合；Prompt 工程侧重写与组织指令，Context 工程覆盖系统指令之外还会进窗的检索结果、工具定义与返回、消息历史等整份上下文状态。（细节与出处见正课。）

| 维度 | Prompt Engineering | Context Engineering |
|------|--------------------|---------------------|
| 核心问题 | 指令怎么写才稳、才可测 | 这一步该装哪些 token、以什么形态装 |
| 典型对象 | System / User 文案、示例、输出格式 | 检索片段、对话/工具历史、记忆条目、工具 schema |
| 时间尺度 | 一次写好 + 版本迭代 | 每轮/每步动态策展；长任务还要压缩与外置记忆 |
| 失败形态 | 歧义、格式漂移、边界不清 | 该进的没进、噪声挤爆窗口、tool_result 堆积、缓存打不中 |
| PM 抓手 | 人格、能力边界、验收标准、示例质量 | **上下文清单与优先级**、即时拉取 vs 预取、成本/延迟预算、降级口径 |

**答题展开三步**（别背定义，按框架说）：

1. **先拆失败**：答得差，更像指令写糊了，还是该进窗的知识/历史/工具结果没进对、或不该进的塞满了？前者回 Prompt 工程化；后者开 Context 复盘。
2. **再列窗口部件**：系统指令只是一小块；生产里还有工具定义、RAG 片段、多轮历史、tool_result、被选中的记忆、本轮用户输入——「能塞」≠「该塞」。
3. **最后划职责**：PM 管「要什么上下文、在什么约束下拼、怎样算拼对了」；工程管 caching breakpoint、compaction 触发、清理实现等——你可参与设计，但不代替调参。

**产品场景举例**：客服 Agent 多轮后越答越飘——常见不是「再改一句 System Prompt」，而是历史 tool_result / 日志全文每轮复读把高信号指令挤到边缘；产品侧应要求可观测窗口占比，并约定长任务触顶是摘要、外置记忆还是转人工。

## 追问延伸

- **追问 1**:"你们优化过 Prompt，算不算做了 Context Engineering？"
  → 不算自动等价。改文案是 Prompt 资产；有没有按场景策展检索/历史/工具结果、有没有长任务压缩与成本预算，才是 Context 系统。两者常一起做，但面试里要分得清。
- **追问 2**:"窗口都 1M 了，还需要 Context Engineering 吗？"
  → 需要。窗口变大降低「塞不下」的硬约束，但不消除噪声、成本和「中间遗忘」类风险；原则仍是高信号、少冗余。细节链 [上下文窗口](../../01-ai-basics/llm/context-window.md) 与正课。
- **追问 3**:"PM 在 Context 这件事上具体写什么进 PRD？"
  → 上下文清单（必须有 / 可以有 / 禁止进窗）、单次任务 token/延迟上限、工具最小必要集与高风险确认、触顶与失败降级口径；实现参数留给工程，策略变更要进评测/灰度。

## 相关阅读

- [Context Engineering：从写 Prompt 到拼上下文系统](../../01-ai-basics/prompt-engineering/context-engineering.md)（本题正课）
- [Prompt 基本功:角色、任务、约束、示例、输出格式](../../01-ai-basics/prompt-engineering/prompt-basics.md)
- [系统提示词设计与 Prompt 工程化:把玄学变工程](../../01-ai-basics/prompt-engineering/system-prompt-and-prompt-ops.md)
- [上下文窗口:限制、成本与产品设计影响](../../01-ai-basics/llm/context-window.md)
- [什么是 RAG:检索增强生成入门](../../01-ai-basics/llm/what-is-rag.md)
- [Agent 入门:规划、记忆、工具调用](../../01-ai-basics/llm/agent-basics.md)
- [大模型的上下文窗口和记忆机制是什么?](context-window-and-memory.md)
- [Prompt、RAG、SFT 分别是什么?有什么区别?](prompt-rag-sft-difference.md)