---
title: agent-memory-design
type: note
permalink: aipmwiki/docs/02-pm-skills/ai-product-operations/agent-memory-design
---

# Agent 记忆怎么设计：写入、读取、更正与删除

> **层级：进阶。** 先修：[上下文工程](../../01-ai-basics/prompt-engineering/context-engineering.md)、[生产化](agent-product-design.md)、[安全与权限](guardrails-and-agent-security.md)。

Agent 的“记忆”不是把所有历史对话永久塞回 Prompt。生产系统至少要区分当前任务状态、跨会话记忆、制度/知识库和业务系统事实。读完本篇，读者应能给一条信息分类，决定是否写入、何时读取、多久保留，以及用户要求更正或删除时怎样留下证据。

## 一、先回答四个问题

1. 这条信息是完成当前任务所需的临时状态，还是未来任务也有价值？
2. 来源是用户明确表达、业务系统回执、制度文档，还是模型推断？
3. 它是否会随时间、租户、角色或业务状态变化？
4. 用户能否查看、纠正和删除？删除后系统是否还会从缓存、索引或日志读回？

只要来源不清、变化快或涉及权限，就不要把它当作无条件长期记忆。

## 二、四层数据边界

| 层 | 示例 | 保存方式 | 读取规则 |
|---|---|---|---|
| 当前任务状态 | 当前步骤、待确认字段、request_key、工具回执 | 任务状态库 | 只给本任务和授权角色 |
| 长期记忆 | 用户明确保存的界面语言、稳定偏好 | 结构化记忆条目 | 按用户/租户/用途过滤 |
| 制度与知识 | D01 现行设备标准、FAQ、版本规则 | 有版本的知识库/RAG | 每次按生效日和权限检索 |
| 业务事实 | 申请状态、审批结果、交付状态 | 业务系统 | 每次实时读取，不靠记忆猜 |

教学契约规定：内部 IT 助手的长期记忆默认只保存用户明确选择的界面语言，不保存制度事实和审批资格。申请提交返回 `status=pending_approval` 与业务申请 ID；`approved` 不等于 `fulfilled`。这类状态必须实时查业务系统。

## 三、记忆条目最小结构

```json
{
  "memory_id": "mem_001",
  "subject": "E01",
  "tenant": "tenant-A",
  "type": "explicit_preference",
  "content": "界面语言：中文",
  "source": "user_confirmation",
  "created_at": "2026-09-14T10:00:00+08:00",
  "last_verified_at": "2026-09-14T10:00:00+08:00",
  "expires_at": null,
  "status": "active",
  "consent": "explicit",
  "supersedes": null
}
```

不要只存一段自然语言。`type` 区分 explicit preference、episodic event、semantic fact、procedural knowledge；`source` 说明由谁、哪个系统、哪次任务产生；`status` 支持 active、superseded、corrected、deleted；`tenant` 和 subject 用于隔离。

## 四、什么时候写入和读取

### 写入闸门

只有满足以下条件才进入长期记忆：用户明确要求保存，或产品在首次使用时展示用途并获得同意；信息对未来任务确有复用价值；来源和时间可记录；不会替代每次应实时读取的权限/状态。

模型推断“用户可能喜欢某型号”不能自动写成偏好。制度文档不能写进个人记忆。敏感信息没有明确用途和同意时不保存。

### 读取闸门

读取前先按 subject、tenant、用途和时间过滤，再做相关性检索。记忆只是候选上下文，不能覆盖当前用户输入、最新业务回执或生效制度。冲突时把冲突显式交给规则或人工，而不是让模型自行选择“看起来最新”的一条。

## 五、长期记忆需要生命周期

AWS 2026-09-04 的 AgentCore 技术文章把记忆分成 episodic、semantic、procedural，并用 TTL、评分、合并和剪枝组成 nightly workflow；文章同时明确 AgentCore 没有内置 auto-delete TTL，示例由 Step Functions、过滤器和自建 pruner 完成。这个边界很关键：有 Memory API 不等于已经有正确的“遗忘”机制。

下表包含一般产品可能采用的数据分类；本库内部助手仅启用界面语言长期记忆，episodic/procedural 不在本次记忆范围。建议按类型制定规则，而不是一个全局保留期限：

| 记忆类型 | 默认保留思路 | 失效信号 | 处理 |
|---|---|---|---|
| 当前任务状态 | 直到成功、取消、过期或转人工 | 任务终态、用户改关键字段 | 归档或按规则删除 |
| episodic 事件 | 短期、按业务窗口 | 过期、重复、无后续价值 | TTL/合并/删除 |
| semantic 偏好 | 较长，但需可验证 | 用户新选择、来源撤回 | supersede，不覆盖历史审计 |
| procedural 流程 | 直到制度/流程版本变化 | 新版 SOP、工具 schema 变化 | 重新审核并换版本 |

对长任务还要警惕压缩丢失规则。2026-08-24 的 arXiv 预印本《The Compaction Cliff》报告了特定配置下压缩多轮后安全规则保留率下降；它是研究提示，不能推广为所有模型的固定比例。产品上应把不可丢的安全规则置于可重新加载的策略源，而不是只依赖摘要。

## 六、更正与删除流程

```text
用户请求查看/更正/删除
  → 校验身份与租户
  → 定位 memory_id、来源和派生副本
  → 暂停读取旧条目
  → 更新为 corrected/superseded 或执行删除
  → 清理检索索引、缓存和可删除副本
  → 返回处理结果与审计 ID
```

删除长期记忆不等于删除依法必须保留的安全审计日志；两者的用途和访问权限要分开。若不能确认某条信息的派生副本已清理，应返回“处理中/待人工”，不要声称彻底删除。

## 七、内部助手填写练习

| 输入 | 分类 | 是否写长期记忆 | 本次读取来源 | 预期结果 |
|---|---|---|---|---|
| “以后界面都用中文” | explicit preference | 是，需记录同意 | 记忆库 | 可复用界面语言 |
| “我有审批权限” | 权限主张 | 否 | 业务系统/权限服务 | 每次实时鉴权 |
| D01 设备标准 | 制度知识 | 否 | 有效期过滤后的知识库 | 带引用回答 |
| 申请已审批 | 业务状态 | 否 | 申请系统 | 返回当前状态 |
| “删掉我之前的语言偏好” | 删除请求 | 删除对应条目 | 记忆索引/缓存 | 返回删除或待处理状态 |

## 八、把一次纠错走完整

以下均为虚构教学：E01 显式保存中文界面偏好，产生 `mem_001 v1`。次日明确改成英语，先使 v1 不再参与读取，建立 v2 并保留最小变更事件；旧会话恢复时，按当前记忆版本读 v2，不能让旧摘要覆盖。随后用户删除偏好，状态进入删除处理中，清除活动条目与派生检索副本，完成后恢复默认界面。后台审计有独立用途和期限，向用户说明，不再用于个性化注入。

```text
候选信息 → 资格检查 → 待确认 → active
                                ├→ 新选择 → superseded → 新版本active
                                ├→ 到期 → expired → 停止读取/复核/按策略删除
                                └→ 删除请求 → deleting → deleted
                                              └→ 副本未清 → 待处理，不假报完成
```

示例 `expires_at=null` 仅表示此教学偏好没有定时到期值，不代表不可删除或永久保存所有原文。真正采用TTL时，应验证时间到达后读取立即失效，清理任务随后可追踪；不能仅删除数据库一行却继续从向量索引读回。

| 用例 | 预期行为 | 可见状态与评分依据 |
|---|---|---|
| 同一用户新选择与旧摘要冲突 | 当前明确选择优先 | 展示已更新；后续界面/注入均为新值 |
| 删除后旧会话恢复 | 不复活旧项 | 已删除；活动索引无旧项，默认设置生效 |
| 跨租户同名用户读取 | 不能共用偏好 | 无泄露；读取过滤记录正确 |
| 到期但清理任务失败 | 停止使用并重试清理 | 过期/清理中；不能宣称物理副本已全删 |

可对应[实践三EX01/EX02](../practice/internal-service-evaluation.md#七扩展场景验收不混入30条总分)进行纸面验收。它们不是真实模型运行结果。

## 相关阅读

- [Context Engineering：从写 Prompt 到拼上下文系统](../../01-ai-basics/prompt-engineering/context-engineering.md)
- [Agent 产品生产化：从会执行到可负责地完成任务](agent-product-design.md)
- [AI 安全与 Agent 权限](guardrails-and-agent-security.md)
- [Agent 评测与可观测性](agent-evaluation-and-observability.md)

## 参考资料

- [What’s new in Gemini Enterprise Agent Platform](https://cloud.google.com/blog/products/ai-machine-learning/whats-new-in-gemini-enterprise-agent-platform) — Google Cloud，2026-07-29，厂商称 Memory Bank 面向所有用户，访问 2026-09-14。
- [AgentCore Memory JSON payloads](https://aws.amazon.com/about-aws/whats-new/2026/08/agentcore-memory-json-payloads/) — AWS，2026-08-20，已发布，访问 2026-09-14。
- [Designing lifecycle policies for AgentCore memory](https://aws.amazon.com/blogs/machine-learning/designing-lifecycle-policies-for-agentcore-memory/) — AWS，2026-09-04，参考架构，访问 2026-09-14。
- [Claude Release Notes](https://support.claude.com/en/articles/12138966-release-notes) — Anthropic，2026-07-10/08-25，产品更新，访问 2026-09-14。
- [The Compaction Cliff in Long-Running AI Agent Memory](https://arxiv.org/abs/2608.22752) — arXiv，2026-08-24，预印本，访问 2026-09-14。
