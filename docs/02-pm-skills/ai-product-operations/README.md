---
title: README
type: note
permalink: aipmwiki/docs/02-pm-skills/ai-product-operations/readme
---

# AI 产品运营：Agent 与 AI 功能的生产化

AI 功能从 Demo 走到生产，难点不只是把模型接进来，而是把不确定的模型行为包进一套**可完成、可观察、可约束、可复盘**的产品系统。本板块聚焦 AI PM 在上线前后需要持续负责的生产化问题。

## 目录

- [Agent 产品生产化：从会执行到可负责地完成任务](agent-product-design.md) —— 任务拆解、工具调用、状态与记忆、人工接管、权限、重试、成本上限和完成标准
- [Agent 评测与可观测性：从最终答案追到完整轨迹](agent-evaluation-and-observability.md) —— 任务/步骤/工具指标、轨迹追踪、线上 Badcase、回归、灰度、回滚、成本和延迟
- [AI 安全与 Agent 权限：把“能做什么”变成可治理的边界](guardrails-and-agent-security.md) —— Prompt Injection、越权调用、数据泄露、恶意网页/文件、确认机制与人工兜底
- [AI 产品指标体系：从业务结果到成本与风险](ai-product-metrics.md) —— 业务、AI 效果、系统、成本四层指标树，以及指标冲突和上线后监控
- [AI 功能留存与成功指标：死亡路径与 DAU 误判](ai-feature-retention-and-success-metrics.md) —— 队列留存口径、死亡路径分诊、为何单看 DAU 会误判 AI 功能成功
- [何时不该上多 Agent：延迟、成本、工具膨胀与劝退](when-not-to-use-multi-agent.md) —— 多 Agent 协调税、工具面膨胀、延迟暴增归因，以及劝退老板的一页纸结构

## 建议阅读顺序

1. 先用[Agent 入门：规划、记忆、工具调用](../../01-ai-basics/llm/agent-basics.md)建立概念，再阅读[Agent 产品生产化](agent-product-design.md)把概念变成产品契约。
2. 用[Agent 评测与可观测性](agent-evaluation-and-observability.md)定义“完成”和“出了什么问题”，再用[AI 安全与 Agent 权限](guardrails-and-agent-security.md)给工具和数据划边界。
3. 最后用[AI 产品指标体系](ai-product-metrics.md)把业务价值、效果、系统稳定性和成本放进同一张监控表；功能上线后的留存与 DAU 误判见[AI 功能留存与成功指标](ai-feature-retention-and-success-metrics.md)。

## 与其他板块的关系

- [AI 产品 PRD 怎么写](../prd-and-design/ai-prd-guide.md)负责把效果定义、能力边界、数据回流和成本预算写进需求；本板块继续展开上线后的运行机制。
- [模型与产品效果评估](../model-evaluation/README.md)负责评测集、人工评分和 Badcase 闭环；本板块补充 Agent 的步骤、工具和轨迹维度。
- [成本测算与技术协作](../cost-and-tech/README.md)负责模型调用和基础设施成本；本板块把成本上限、任务预算和成本告警放回 Agent 生命周期。
- [Vibe Coding](../vibe-coding/README.md)适合验证原型假设；涉及真实用户、真实数据和持续稳定性时，仍需回到生产化的权限、评测和监控要求。