---
title: what-is-agents-md
type: note
permalink: aipmwiki/docs/04-interview/basics/what-is-agents-md
---

# 什么是 AGENTS.md / Agent.md?产品经理为什么要关心?

**难度**:⭐⭐ 入门必备
**出现频率**:中高,Coding Agent、开源协作与「Agent 原生仓库」相关面试渐多

## 考察点

- 能否说清「项目级 Agent 说明书」解决什么问题
- 是否区分 AGENTS.md、Rules、Skill、README 的职责
- 能否从协作与治理角度说明 PM/TL 为何要维护它

## 参考答案

**一句话**:`AGENTS.md`(亦常见 `Agent.md` 等命名)是放在仓库里的**给编码/操作 Agent 读的项目说明书**——告诉 Agent「这是什么项目、怎么改、禁区在哪、验证怎么跑」,减少每次对话从头猜。

它不是给人看的营销 README 替代品,而是 **Agent 的入职手册(项目级)**:

| 文档 | 主要读者 | 典型内容 |
|------|----------|----------|
| `README.md` | 人类贡献者 | 快速开始、架构鸟瞰 |
| `AGENTS.md` / Agent 约定 | Coding Agent | 目录约定、改动纪律、测试命令、禁改区域 |
| Rules / 系统提示 | 当前会话 Agent | 语气、全局安全、工具权限 |
| Skill | 某类任务 | 可复用 SOP(可跨仓) |

**PM 为什么要关心**(即使自己不写代码):

1. **行为可预期**:没有项目约定时,Agent 会按「通用开源习惯」乱改——扩 scope、改格式、动不相关文件。
2. **协作契约**:人和 Agent 共用同一套「怎么贡献」规则,Code Review 标准一致。
3. **安全与合规**:可写明「禁止提交密钥」「禁止改计费逻辑」等红线。
4. **降本**:少绕弯、少返工,等于少烧探索性 token。

写什么(最小集):项目目标与边界、目录地图、允许/禁止的改动类型、如何跑测试/预览、PR/提交约定、联系人。保持短、可执行、常更新——过期的 AGENTS.md 比没有更危险。

## 追问延伸

- **追问 1**:"AGENTS.md 和 Cursor Rules / CLAUDE.md 是什么关系?"
  → 同属「给 Agent 的项目上下文」;不同工具文件名不同。产品上应追求**一份真相、多工具适配**,避免三份互相矛盾的规矩。
- **追问 2**:"要不要把全部业务知识写进 AGENTS.md?"
  → 不要。只放「改这个仓必须知道」的约束与流程;长 SOP 用 Skill 或 docs/,按需加载。
- **追问 3**:"开源项目没有 AGENTS.md,Agent 还能用吗?"
  → 能,但效果看 README 质量。对内仓建议补上,作为 Agent 落地的最低治理件。

## 相关阅读

- [Agent Skills:给 Agent 装上可复用的「操作手册」](../../01-ai-basics/llm/agent-skills.md)
- [Skill 和 Rules 到底有什么区别?](skill-vs-rules.md)
- [Agent 入门:规划、记忆、工具调用](../../01-ai-basics/llm/agent-basics.md)
- [什么是 Vibe Coding](../../02-pm-skills/vibe-coding/what-is-vibe-coding.md)