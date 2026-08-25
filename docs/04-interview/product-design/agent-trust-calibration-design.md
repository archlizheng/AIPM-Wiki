---
title: agent-trust-calibration-design
type: note
permalink: aipmwiki/docs/04-interview/product-design/agent-trust-calibration-design
---

# 怎么设计 Agent 的信任校准，避免过度信任或不信任？

**难度**:⭐⭐⭐ 进阶
**出现频率**:中偏高（Agent / Agentic UX 轮次常见）

> **与黑箱题分工**：本题聚焦 **Agent 改世界状态时**的 UX——确认分级、进度可见、打断接管、可撤销与完成证据。黑箱与可解释性见 [AI 产品的黑箱问题](../basics/ai-blackbox-trust.md)。正课展开见 [Agentic UX 与信任校准](../../02-pm-skills/prd-and-design/agentic-ux-and-trust-calibration.md)。
>
> **选题启发**：磊叔《关于 AI 产品经理的 100 个问题》相关 Agentic UX / 信任主题（仅选题结构，正文为原创）。

## 考察点

- 是否把目标定为 **calibrated trust（校准）**，而不是「尽量让用户更相信 AI」
- 能否用 overtrust / undertrust 说清风险，并落到确认、进度、接管等控件
- 是否按 **副作用与可逆性** 设计默认策略，而不是所有工具调用一刀切弹窗

## 参考答案

**一句话**：信任校准不是拉高信任分，而是让用户的依赖强度跟着真实能力与风险走——低风险可放手，高后果必停；产品用进度、确认、接管、撤销把这条规则变成可感知控制面。

### 1. 先定义两端风险

| 状态 | 用户行为 | 产品风险 |
|------|----------|----------|
| **Overtrust** | 不审确认、授权过宽就走开、把输出当事实 | 误操作、合规/资金/声誉事故 |
| **Undertrust** | 每步重做、拒用 Agent、有能力也不开 | 采纳失败、效率沉没 |
| **Calibrated** | 低风险交给 Agent，高后果会停下来核验 | 效率与风险可同时谈 |

人因文献把目标写成 *appropriate / calibrated reliance*（Lee & See, 2004），对应 misuse / disuse（Parasuraman & Riley, 1997）——面试里点到「校准 vs 盲目拉信任」即可，细节链正课。

### 2. UX 四件套（本题主战场）

相对黑箱题的「引用 / 可解释 / 渐进信任」，Agent 侧优先讲这四块：

| 控件 | 设计要点 | 校准作用 |
|------|----------|----------|
| **进度可见** | 计划、当前步骤、卡点原因；文案对齐真实工具状态 | 降低「黑箱在干活」的 undertrust；避免假动画推高 overtrust |
| **确认分级** | 按可逆性：只读可自动 → 可撤销先做后告知 → 对外副作用必须事前确认 | 把确认预算留给高后果，防确认疲劳 |
| **打断与接管** | Stop / Pause / Take over 随时可用；取消后不重放副作用；登录支付等敏感输入强制人工 | 能力边界可见时，用户敢放手也敢收回 |
| **撤销与完成证据** | 可逆暴露 Undo；不可逆靠事前确认 + 活动日志；「完成了」要有工具回执/变更摘要 | 纠错成本低 → undertrust 下降；有证据 → 不敢盲信空话 |

确认卡片要有信息密度：动作、对象、范围、是否可撤销、拒绝/修改入口——避免只有「是否继续」。

### 3. 可逆性决定默认策略（答题用表）

| 动作类型 | 默认 UX |
|----------|---------|
| 只读 / 可丢弃草稿 | 可自动；保留来源与中间结果 |
| 可撤销写入 | 先做后告知 + 一键撤销 |
| 难撤销 / 对外副作用 | **必须事前确认**；必要时二次认证 |
| 高风险拒做 | 产品层拒绝或强制人工流程，不交给「点一次全权」 |

渐进授权路径（与黑箱题一致，实现落在 Agent 控制面）：

```text
建议（人决定） → 人确认后执行 → 白名单场景自动执行 → 高后果仍人工
```

每升一级：可观测成功率/事故率、可撤回权限、可回退默认策略。

### 4. 场景举例（任选一个讲透）

以「办公 Agent 代发邮件 / 改日历」为例：检索与起草可自动；发信、外部分享、删权限必须确认卡片展示收件人/正文摘要/可否撤回；长任务展示步骤并可 Take over；发出后给发送回执链接。用户早期翻车后报复性不信任，优先查交互失配（看不见、改不动）与确认过碎，而不是只会「换更强模型」。

「用完又回人工」可用能力缺口 / 交互失配 / 信任错位 / 成本时延 / 组织流程五类归因（见正课），面试说清「先归因再开需求」即可。

### 5. 指标挂钩（加分）

不只看任务成功率：监控「该确认时是否确认」「用户是否过早全权授权」「Pause/Take over 频率」「高后果事故后留存断崖」。评测与线上监控要对齐信任校准，而不是只对齐完成率。

## 追问延伸

- **追问 1**:"确认弹太多用户会烦，怎么平衡？"
  → 确认按副作用分级，不按工具调用次数；合并同批次只读步骤；高频低风险改「事后告知 + Undo」，把预算留给外发/支付/删改权限。
- **追问 2**:"和黑箱透明度有什么区别？"
  → 黑箱侧重「为什么这么答、能不能信这条信息」；本题侧重「它正在改什么状态、我何时必须介入」。引用溯源解决建议层；确认/进度/接管解决动作层。
- **追问 3**:"怎么判断是 overtrust 还是产品太难用？"
  → Overtrust：确认秒点、授权后离开、事故集中在高后果动作。Undertrust / 交互失配：频繁 Take over、重复改同一参数、找不到撤销。用埋点与复盘表区分，避免一律加大模型。

## 相关阅读

- [Agentic UX 与信任校准：用户何时该信、何时该介入](../../02-pm-skills/prd-and-design/agentic-ux-and-trust-calibration.md)
- [AI 产品的黑箱问题如何影响用户信任?产品层面怎么做?](../basics/ai-blackbox-trust.md)
- [兜底、反馈与预期管理:AI 产品的交互设计三件套](../../02-pm-skills/prd-and-design/fallback-and-feedback-design.md)
- [Agent 产品生产化：从会执行到可负责地完成任务](../../02-pm-skills/ai-product-operations/agent-product-design.md)
- [Agent 入门：规划、记忆、工具调用](../../01-ai-basics/llm/agent-basics.md)

## 参考资料

- Parasuraman, R., & Riley, V. (1997). Humans and automation: Use, misuse, disuse, abuse. *Human Factors*, 39(2), 230–253. —— misuse / disuse 与过度依赖、弃用；2026-08-06 核验引用信息
- Lee, J. D., & See, K. A. (2004). Trust in automation: Designing for appropriate reliance. *Human Factors*, 46(1), 50–80. —— appropriate / calibrated reliance；2026-08-06 核验引用信息
- [Introducing ChatGPT agent](https://openai.com/index/introducing-chatgpt-agent) —— 确认、打断、进度叙述等公开 UX 模式；2026-08-06 访问
- [Computer use tool](https://platform.claude.com/docs/en/agents-and-tools/tool-use/computer-use-tool) —— 人确认与提示注入后确认；2026-08-06 访问