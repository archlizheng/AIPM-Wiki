---
title: README
type: note
permalink: aipmwiki/docs/02-pm-skills/practice/readme
---

# 内部 IT 服务助手：贯穿实践路线

**层级：实践。** 用一个场景串起立项、PRD、权限、评测、成本与采用。四课均为虚构教学材料与文档推演，不提供应用、不包含真实业务运行结果。先读 [AI 需求发现](../prd-and-design/ai-demand-discovery.md)与 [Agent 入门](../../01-ai-basics/llm/agent-basics.md)。

## 目录与先修

| 顺序 | 正文 | 先修 | 完成后交付 |
|---|---|---|---|
| P1 | [需求发现](internal-service-discovery.md) | 需求发现正课 | 访谈分析、原流程、非 AI 基线、立项卡 |
| P2 | [PRD 与权限契约](internal-service-prd.md) | P1、[AI PRD](../prd-and-design/ai-prd-guide.md) | 六项契约、字段与工具职责、Prompt Spec、状态与确认 |
| P3 | [30 条评测与发布判断](internal-service-evaluation.md) | P2、[评测集建设](../model-evaluation/build-your-eval-set.md) | 六组各五条评测、四条扩展验收、逐项教学评分、发布决策 |
| P4 | [成本、采用与验收](internal-service-adoption.md) | P3、[指标](../ai-product-operations/ai-product-metrics.md)、[采用](../ai-product-operations/poc-to-production-adoption.md) | 可复算账本、试点设计、采用队列、验收报告 |

## 共用材料与边界

使用 [12 份制度与流程资料、四类角色及统一事件账本](../../../assets/internal-service-assistant/teaching-materials.md)。D02 是过期版本、D06 与正式制度冲突，不能在练习前悄悄删除它们。

流程为“制度问答 → 草稿 → 员工确认提交 → 主管人工审批 → 查询状态”，不含采购付款、账号提权或自动审批。申请已提交、审批通过、设备交付分别记录。

## 四道原创练习

以下均为原创练习，出现频率未统计，不代表真实公司面试来源。

- [PoC 达标为什么仍不能生产](../../04-interview/product-design/poc-production-readiness.md)：对应 P1/P4 与组织采用正课。
- [记忆纠错或删除后的行为](../../04-interview/product-design/agent-memory-correction-deletion.md)：对应 P2/P3 与长期记忆正课。
- [超时重试如何避免重复建单](../../04-interview/product-design/agent-retry-duplicate-request.md)：对应 P2/P3 与生产化正课。
- [语音打断后已发出的动作怎么办](../../04-interview/product-design/voice-interruption-action-boundary.md)：对应 P3 扩展验收与实时语音正课。

自查时，请另一位读者只拿你的四份作业，解释成功分母、角色权限、制度冲突、取消恢复和单位成本。没有真实运行证据时，作品集只能陈述设计与教学推演成果。
