---
title: context-window
type: note
permalink: aipmwiki/docs/01-ai-basics/llm/context-window
---

# 上下文窗口：限制、成本与产品设计影响

> **信息状态**：概念与核验方法｜**最后核验**：2026-09-14（Asia/Shanghai）

## 一句话说清

上下文窗口是一次模型调用可接收的输入与生成额度。系统提示、历史消息、用户问题、检索片段、工具结果和模型输出都要占用预算；不同 API 对“输入上限”“最大输出”和“上下文总量”的定义可能不同。窗口变大只代表上限提高，不代表模型会均匀利用所有内容，也不代表成本和延迟不变。

## 一次调用里到底装了什么

```text
[系统提示] + [历史消息] + [用户输入]
+ [RAG 片段] + [工具结果] + [预留输出]
≤ 该接口对本次请求的有效上限
```

产品设计时应把“预算”拆成输入预算和输出预算，并为工具调用、错误重试、摘要压缩预留空间。否则一次工具返回大 JSON 就可能挤掉后续任务所需的指令。

## 2026-09-14 的核验原则

不要继续维护一张容易过时的全行业数字表。官方页面的字段和状态变化很快，且不同平台口径并不相同。逐行核验时保存以下记录：

| 字段 | 从哪里读 | 需要标注 |
|---|---|---|
| 模型 ID/版本 | 官方 Models API 或模型详情 | alias、snapshot、发布日期 |
| 输入上限 | 官方模型目录/API 错误说明 | 是否包含输出预留、是否按模式变化 |
| 最大输出 | 模型详情或 API reference | 是否独立于上下文窗口 |
| 计费 | 官方 pricing 页 | 输入、输出、缓存、批量、长上下文阶梯 |
| 生命周期 | 官方 release/deprecation 页 | GA、预览、Beta、deprecated、retired |

可核验的例子：Anthropic 当前模型总览分别列出 1M 或 200K 的上下文和独立最大输出字段；Google 的 `models.get` 返回 token limits，`models.list` 返回可用模型和支持动作；Google 的 Gemini 2.5 Flash 定价页明确写出 1M 上下文，并把文本/图像/视频输入、音频输入、输出和缓存分别计价。它们说明“窗口、输出、计费”必须分栏记录，不应只抄一个“上下文 1M”。

## 窗口大小与有效上下文是两回事

长文本中存在“中间遗忘”风险：信息即使在窗口内，模型也可能因为位置、干扰和任务复杂度而没有正确引用。检索、排序、压缩和引用评测往往比单纯扩大窗口更能改善结果。设计上可以：

- 把不可违背的规则放在稳定的系统区，并在执行动作前重复关键约束；
- 将资料按问题检索，给每段带来源、时间和权限标签；
- 让工具只返回完成当前步骤所需的字段，超长结果保存到外部对象并返回引用；
- 在接近阈值前触发摘要或结构化压缩，保留未完成任务、已确认事实、待办和引用；
- 用“同一事实放在开头/中部/末尾”的小测试，测引用率而非凭直觉判断。

## 成本与延迟：窗口不是免费的仓库

```text
单次成本
= 输入 token × 输入价
+ 输出 token（按平台规则可能含思考 token）× 输出价
+ 缓存写入/命中
+ 检索、工具、运行时和重试
```

输入越长通常越慢，重放整段历史还会反复计费。不同厂商对超阈值、缓存和批处理的规则不同，因此不能拿一家的单价直接外推另一家。线上日志至少记录请求 token、输出 token、缓存命中、压缩次数、工具结果大小和最终任务状态。

## 长对话与记忆如何分工

上下文窗口不是跨会话记忆。每次调用都要重新组织输入；所谓长期记忆通常是外部存储、权限过滤和按需注入的组合。常见策略如下：

| 策略 | 优点 | 风险 | 适合 |
|---|---|---|---|
| 原样拼历史 | 实现简单 | 成本和噪声随轮数增加 | 短对话原型 |
| 摘要 + 最近消息 | 控制长度 | 摘要可能丢约束或混淆事实 | 中长对话 |
| 外部记忆 + 检索 | 可跨会话、可审计 | 需要写入门槛、权限、删除和纠错 | 企业 Agent |
| 压缩/折叠上下文 | 适合长任务 | 压缩失真会影响恢复 | 工具型长任务 |

内部 IT 服务助手的教学契约只允许记忆用户明确要求保存的界面语言；工单状态必须以系统记录为准，提交不等于批准或履行。任务 A 是制度问答，任务 B 是员工确认后的受控申请提交，任务 C 是授权状态查询。这个边界要同时写进存储策略、注入模板和评测样本；任务定义与练习见[内部服务需求发现实践](../../02-pm-skills/practice/internal-service-discovery.md)、[内部服务助手 PRD](../../02-pm-skills/practice/internal-service-prd.md)。

## 2026 年运行时变化带来的设计要求

OpenAI Agents API 的官方材料已把 compaction、会话续跑、工具和长任务作为运行时能力；MCP 2026-07-28 规范则移除初始化握手并强调无状态请求。产品因此要把上下文视为可重建的运行时状态：每一步都带任务 ID、版本、来源和权限；压缩后能校验关键事实；重启后能从持久化状态恢复，而不是假设内存中永远有完整对话。上下文压缩或 MCP 的具体支持仍需按所用 SDK/服务器版本核对。

## 练习：画出一个上下文预算表

对 E01/M01/IT01/ADM01 的内部 IT 服务助手，任选任务 A/B/C，填写：

| 区块 | 预算/来源 | 是否可丢弃 | 校验 |
|---|---|---|---|
| 系统规则 | 角色、权限、提交/批准/履行语义 | 否 | 每次执行前存在 |
| 最近对话 | 当前澄清和用户确认 | 部分 | 与任务 ID 绑定 |
| 记忆 | 仅明确允许的界面语言 | 是，需按权限 | 读写门记录 |
| 检索 | 工单/知识库片段 | 是 | 来源、时间、租户 |
| 工具结果 | 当前步骤必要字段 | 是 | schema、大小、错误 |
| 输出预留 | 回复或结构化动作 | 否 | 不超过 API 上限 |

再制造一个超长工具返回和一次失败重试，写出何时压缩、压缩后必须保留什么、何时转人工。成本数字只用于教学算术，不能当线上账单。

## 相关阅读

- [Context Engineering：从写 Prompt 到拼上下文系统](../prompt-engineering/context-engineering.md)
- [Agent 记忆设计](../../02-pm-skills/ai-product-operations/agent-memory-design.md)
- [Agent 评测与可观测性](../../02-pm-skills/ai-product-operations/agent-evaluation-and-observability.md)
- [主流大模型盘点与选型思路](model-landscape.md)
- 面试题：[大模型的上下文窗口和记忆机制是什么？](../../04-interview/basics/context-window-and-memory.md)

## 官方资料（访问 2026-09-14）

- [OpenAI API Models](https://developers.openai.com/api/docs/models)；[OpenAI Compare models](https://developers.openai.com/api/docs/models/compare)；[Compaction](https://developers.openai.com/api/docs/guides/compaction)
- [Anthropic Models overview](https://platform.claude.com/docs/en/models/overview)；[Pricing](https://platform.claude.com/docs/en/about-claude/pricing)
- [Gemini API Models API](https://ai.google.dev/api/models)；[Gemini API Pricing](https://ai.google.dev/gemini-api/docs/pricing)
- [MCP 2026-07-28 Changelog](https://github.com/modelcontextprotocol/modelcontextprotocol/blob/main/docs/specification/2026-07-28/changelog.mdx)
- [The Compaction Cliff: How Memory Compression Impacts Long-Horizon Agent Performance](https://arxiv.org/abs/2608.22752)（2026-08-24 预印本，研究证据，不是生产规范）
