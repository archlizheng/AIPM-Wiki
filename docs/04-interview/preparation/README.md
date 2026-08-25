---
title: README
type: note
permalink: aipmwiki/docs/04-interview/preparation/readme
---

# 面试准备方法

这里不是新的题型分类，而是帮助你把已有项目、作品集和 Demo 准备成**可讲清、可验证、经得起追问**的面试材料。它和 [basics/](../basics/README.md)、[product-design/](../product-design/README.md)、[case-analysis/](../case-analysis/README.md) 的关系是：题型目录告诉你面试官可能问什么，本目录告诉你如何准备自己的证据和表达。

## 目录

- [AI PM 项目深挖的准备方法](project-deep-dive.md) —— 背景、目标、个人贡献、关键取舍、数据结果、失败复盘、技术边界和常见追问
- [AI PM 作品集与 Demo 准备](portfolio-and-demo.md) —— RAG、Agent、Vibe Coding 项目的展示结构、评测集、成本测算、演示风险和讲解顺序
- [JD 写「熟悉大模型」，面试官的三档标准是什么？怎么自测？](llm-familiarity-three-levels.md) —— L1/L2/L3 定档、30 秒自测与简历措辞防越档

## 使用建议

1. 先选一个你真正参与过、能拿出过程证据的项目，按[项目深挖](project-deep-dive.md)整理事实卡和追问。
2. 如果项目需要现场展示，用[作品集与 Demo 准备](portfolio-and-demo.md)补齐评测集、成本、边界和演示兜底。
3. 再回到[模型评测题](../basics/model-evaluation-system.md)、[Agent 基础题](../basics/agent-vs-chatbot.md)和[产品设计题](../product-design/README.md)，把项目经验连接到通用方法。

## 配套 Skills

如果希望把准备过程进一步工具化，可以安装以下两个面试 Skill：

- [interview-self-introduce](https://github.com/archlizheng/interview-self-introduce)：根据 JD、简历和面试轮次生成并迭代自我介绍。
- [interview-assessment](https://github.com/archlizheng/interview-assessment)：用于 JD/简历匹配、面试准备和面试后复盘。

安装命令：

```bash
npx skills add archlizheng/interview-self-introduce
npx skills add archlizheng/interview-assessment
```

这两个 Skill 是执行工具；本目录的文章仍负责提供项目深挖、作品集和 Demo 准备的方法论与检查框架。

## 相关阅读

- [AI 产品 PRD 怎么写：与传统 PRD 的核心差异](../../02-pm-skills/prd-and-design/ai-prd-guide.md)
- [Agent 产品生产化：从会执行到可负责地完成任务](../../02-pm-skills/ai-product-operations/agent-product-design.md)
- [Agent 评测与可观测性：从最终答案追到完整轨迹](../../02-pm-skills/ai-product-operations/agent-evaluation-and-observability.md)
- [AI 产品指标体系：从业务结果到成本与风险](../../02-pm-skills/ai-product-operations/ai-product-metrics.md)
- [成本测算与技术协作](../../02-pm-skills/cost-and-tech/README.md)
- [Vibe Coding：AI PM 亲手做原型](../../02-pm-skills/vibe-coding/README.md)