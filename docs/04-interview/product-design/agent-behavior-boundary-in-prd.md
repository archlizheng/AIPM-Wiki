---
title: agent-behavior-boundary-in-prd
type: note
permalink: aipmwiki/docs/04-interview/product-design/agent-behavior-boundary-in-prd
---

# 怎么在 PRD 里写清楚 Agent 的行为边界？

**难度**:⭐⭐ 常规
**出现频率**:高,Agent / AI PRD / 工具调用类面试常见

> 选题启发自磊叔《关于 AI 产品经理的 100 个问题》Q72；正文为本库原创，未摘录该 PDF 答案原文。细节与模板以正课为准。

## 考察点

- 能否把「能力边界」(答什么 / 不答什么)和「行为边界」(能动什么 / 做到哪必须停)分开讲,不混成一张空表
- 能否把边界写成**可验收条目**(工具白名单、确认节点、完成证据、预算上限),而不是「要小心」「要合规」
- 能否说明硬边界落在工具鉴权与产品状态机,Prompt Spec / Eval Spec 如何咬合,而不是只靠 System Prompt

## 参考答案

**一句话**:有工具、会改外部状态时,PRD 里的 Agent 行为边界 = 团队共同认可的**授权合同**——写清允许调用什么、完成靠什么证据、风险动作何时必须停、失败怎么退、预算触顶怎么办;形容词不算边界。

先和「能力边界」划清线(正课两张表并存):

| | 能力边界 / 不承诺清单 | Agent 行为边界 |
|--|----------------------|----------------|
| 管什么 | 对用户承诺的场景范围 | 对系统动作的授权范围 |
| 典型问题 | 答什么、拒什么、已知失败模式 | 可调哪些工具、写到哪必须确认、预算与出口 |
| 缺了会怎样 | 预期漂移、幻觉硬撑 | 越权调用、误发/误付、静默烧额度 |

### 答题展开:PRD 里固定六块(浓缩自正课)

不必背状态机全文;面试官要听的是你能不能把下面六块说成**可验收写法**:

| 块 | 面试口径(可验收) |
|----|------------------|
| 任务目标与完成证据 | 「完成」≠ 模型说「好了」;写清工具回执 / 业务校验 / 人确认里哪几条必须满足 |
| 允许的工具 / 资源 | 白名单(工具名、读写级别、租户与数据范围)+ 单独黑名单 |
| 风险分级与默认策略 | 只读可自动;可逆写入要可预览/可撤销;付款、删除、外发、改权限等默认**先确认** |
| 必须停下来的节点 | 金额阈值、批量上限、置信度不足、策略冲突、预算用尽——每条对应唯一下一步 |
| 失败与降级出口 | 重试 / 降级 / 转人工 / 暂停;禁止「静默继续烧额度」 |
| 预算上限 | 模型轮数、工具次数、时延、费用——触顶即 `blocked` 或交接,并说明已完成/未完成 |

### 弱写法 vs 可验收写法(面试加分点)

| 弱写法 | 可验收写法 |
|--------|------------|
| 「高风险操作要小心」 | 「单笔 ≥ ¥X 或批量 ≥ N 条的写操作:展示对象/范围/后果后,当前用户确认才执行」 |
| 「可以调用内部 API」 | 「仅 `ticket.read` / `ticket.draft_reply`;禁止 `ticket.close`、`refund.create`」 |
| 「失败就重试」 | 「仅对幂等只读超时重试 ≤ 2 次;非幂等写操作不自动重试,进入人工确认」 |
| 「注意成本」 | 「单任务模型调用 ≤ 8 轮或费用 ≤ ¥Y,先到先停,并向用户说明进度」 |

数字用业务定阈值即可;面试报的是**结构**,不是普适红线。

### 和 Prompt Spec / Eval Spec 怎么咬合

- **Prompt Spec**:自然语言重申「不得绕过确认、不得扩大工具范围」——但这是软约束。
- **硬边界**:工具服务端鉴权 + 产品状态机(确认态、取消后不得重放副作用)。只写在 Prompt 里等于把授权交给模型临场发挥。
- **Eval Spec**:准备「诱导越权 / 跳过确认 / 预算耗尽」类用例;轨迹违规可设为一票否决(细节见 Agent 评测正课)。

**产品场景举例**(客服 Agent 可写草稿):允许读工单与生成回复草稿;关闭工单、发起退款进「先确认」;单任务工具调用触顶则转人工并带上下文——能力边界仍单独写「不承诺法律裁决 / 不做最终赔付承诺」。

## 追问延伸

- **追问 1**:"这和能力边界清单有什么区别?能不能合并成一张表?"
  → 能放在同一章,但职责不同:能力边界管对用户的场景承诺;行为边界管对系统动作的授权。合并成「注意安全」一句等于两张都没写。
- **追问 2**:"边界写进 System Prompt 够不够?"
  → 不够。Prompt 可重申规则,真正拦越权靠工具鉴权、参数校验与确认态;Prompt 被诱导绕过时,服务端仍应拒绝。
- **追问 3**:"怎么验收行为边界有没有写清楚 / 有没有生效?"
  → PRD 侧:六块是否可测;Eval 侧:越权与跳过确认是否一票否决;线上:轨迹日志能否关联任务、工具、确认人与完成证据。

## 相关阅读

- [AI 产品 PRD 怎么写:与传统 PRD 的核心差异](../../02-pm-skills/prd-and-design/ai-prd-guide.md)(本题正课:Agent 行为边界 / Prompt Spec / Eval Spec)
- [Agent 产品生产化:从会执行到可负责地完成任务](../../02-pm-skills/ai-product-operations/agent-product-design.md)(任务卡、工具契约、风险分级)
- [Agentic UX 与信任校准:用户何时该信、何时该介入](../../02-pm-skills/prd-and-design/agentic-ux-and-trust-calibration.md)
- [Agent 评测与可观测性:从最终答案追到完整轨迹](../../02-pm-skills/ai-product-operations/agent-evaluation-and-observability.md)
- [AI 安全与 Agent 权限:把「能做什么」变成可治理的边界](../../02-pm-skills/ai-product-operations/guardrails-and-agent-security.md)
- [系统提示词设计与 Prompt 工程化:把玄学变工程](../../01-ai-basics/prompt-engineering/system-prompt-and-prompt-ops.md)
- [兜底、反馈与预期管理:AI 产品的交互设计三件套](../../02-pm-skills/prd-and-design/fallback-and-feedback-design.md)
- [什么是 Agentic Workflows?](../basics/agentic-workflows.md)

## 参考资料

- [Building effective agents](https://www.anthropic.com/engineering/building-effective-agents) —— Anthropic 工程文章(Workflow vs Agent),截至 2026-08-06 访问
- [A practical guide to building AI agents](https://openai.com/business/guides-and-resources/a-practical-guide-to-building-ai-agents/) —— OpenAI 官方指南,截至 2026-08-06 访问
- [Function calling](https://developers.openai.com/api/docs/guides/function-calling) —— OpenAI 开发者文档,截至 2026-08-06 访问