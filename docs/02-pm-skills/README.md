---
title: README
type: note
permalink: aipmwiki/docs/02-pm-skills/readme
---

# 02 · AI 产品经理核心技能

AI PM 和传统 PM 的差异集中在这里:你需要为「不确定性」做产品——模型输出是概率性的,效果需要定义、评估和持续迭代。

## 目录

- [prd-and-design/](prd-and-design/README.md) —— AI 产品的 PRD 与交互设计（含 Agentic UX 与信任校准）
- [model-evaluation/](model-evaluation/README.md) —— 模型与产品效果评估
- [data-annotation/](data-annotation/README.md) —— 数据标注与冷启动
- [cost-and-tech/](cost-and-tech/README.md) —— 成本测算与技术协作
- [ai-product-operations/](ai-product-operations/README.md) —— Agent 与 AI 功能的生产化、评测、安全、指标和上线运营
- [vibe-coding/](vibe-coding/README.md) —— 不写代码,用 AI 编程工具亲手做出可运行原型

## 核心心法

传统 PM 交付的是「确定的功能」,AI PM 交付的是「一个效果可衡量、可持续优化的系统」。前六个子板块分别对应:怎么定义(PRD)、怎么衡量(评估)、拿什么优化(数据)、花多少钱(成本)、怎么把能力生产化并持续运营(Agent 与 AI 产品运营)、怎么亲手验证(Vibe Coding)。

## 分层学习入口

- **入门**：先读 [需求发现](prd-and-design/ai-demand-discovery.md)，再写 [AI PRD](prd-and-design/ai-prd-guide.md)，补 [评测集](model-evaluation/build-your-eval-set.md)和 [成本入门](cost-and-tech/llm-cost-101.md)。
- **进阶**：沿 [平台选型](cost-and-tech/agent-platform-selection-and-governance.md) → [生产化](ai-product-operations/agent-product-design.md) → [评测](ai-product-operations/agent-evaluation-and-observability.md) → [安全](ai-product-operations/guardrails-and-agent-security.md) → [采用](ai-product-operations/poc-to-production-adoption.md)阅读；记忆与文档/语音/电脑操作按任务选读。
- **实践**：[practice/ 内部服务助手四课](practice/README.md)——先修需求发现与 Agent 入门；交付从问题证据到验收报告的完整文档链。

七个子板块仍服务同一学习顺序：定义需求、评估效果、准备数据、估算成本、生产运营、原型验证，再用贯穿实践检验这些判断是否一致。
