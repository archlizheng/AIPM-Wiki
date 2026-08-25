---
title: skill-versioning-and-dependencies
type: note
permalink: aipmwiki/docs/04-interview/basics/skill-versioning-and-dependencies
---

# Skill 怎么做版本管理?Skill 之间的依赖怎么解?

**难度**:⭐⭐⭐ 进阶
**出现频率**:中高,Agent 平台 / 工具链 / 企业落地岗常见追问

## 考察点

- 是否把 Skill 当成「可版本管理的产品资产」,而不是聊天里的临时提示
- 能否说清发布、回滚、灰度与评测回归
- 能否处理多 Skill 冲突、依赖与组合爆炸

## 参考答案

**一句话**:Skill 应按仓库资产治理——**有版本、有评测、有回滚**;依赖用显式声明 + 最小权限组合,而不是互相隐式引用。

### 版本管理(PM 可落地的最小集)

| 环节 | 做什么 | 产品含义 |
|------|--------|----------|
| 标识 | `name` + semver(或 git tag) | 线上能回答「现在跑的是哪一版手册」 |
| 变更说明 | changelog:触发条件、步骤、禁区改了什么 | 方便坏 case 归因 |
| 发布通道 | draft → staging → prod;可按租户/项目灰度 | 避免一次推全站翻车 |
| 回归 | 固定评测集:该触发/不该触发、关键路径 golden | 「改了一句 description」也要跑 |
| 回滚 | 一键切回上一稳定版 | Agent 行为可撤销 |

Skill 一旦进入组织目录,它就和 Feature Flag、Prompt 版本一样,属于**行为变更**,不是文档编辑。

### 依赖与冲突怎么解

常见坏味道:Skill A 正文写「请先读 Skill B 全文」、多个 Skill 抢同一触发词、循环依赖。

更稳的产品规则:

1. **显式依赖,禁止隐式**：若 B 依赖 A,在元数据声明 `depends_on`,由运行时保证加载顺序;不要在正文里「口头点名」。
2. **依赖要短、要稳**：只依赖稳定基础 Skill(如「公司安全红线」),不要依赖高频实验 Skill。
3. **冲突用优先级 + 互斥组**：同一任务匹配多个 Skill 时,按优先级或让用户/路由选一个;互斥能力不要并行激活。
4. **组合用编排,不靠堆叠**：复杂流程用上层 Workflow/路由决定「启用哪几个 Skill」,而不是一次塞十本手册。
5. **共享能力下沉**：重复段落抽成公共 reference 或基础 Skill,避免复制粘贴分叉。

## 追问延伸

- **追问 1**:"没有自动评测,能不能先上 Skill?"
  → 能,但至少要有手工验收清单(触发样例 + 禁触发样例 + 1–2 条主路径)。规模化前必须补回归,否则版本管理是空壳。
- **追问 2**:"用户私有 Skill 和组织官方 Skill 冲突怎么办?"
  → 明确覆盖策略:组织红线不可覆盖;其余可项目级覆盖并留审计日志。
- **追问 3**:"Skill 依赖 MCP 工具,算不算依赖?"
  → 算运行时依赖。Skill 文档应写清「需要哪些工具/权限」;缺工具时应降级或明确失败,而不是假装做完。

## 相关阅读

- [Agent Skills:给 Agent 装上可复用的「操作手册」](../../01-ai-basics/llm/agent-skills.md)
- [Skill 和 Rules 到底有什么区别?](skill-vs-rules.md)
- [如何设计 Skill 来降低 token 消耗?](skill-token-efficiency.md)
- [Prompt 的版本管理与回归测试](../../01-ai-basics/prompt-engineering/system-prompt-and-prompt-ops.md)