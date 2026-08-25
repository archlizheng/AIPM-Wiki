---
title: skill-vs-rules
type: note
permalink: aipmwiki/docs/04-interview/basics/skill-vs-rules
---

# Skill 和 Rules 到底有什么区别?

**难度**:⭐⭐ 入门必备
**出现频率**:高,2025–2026 Agent / Coding Agent 相关面试常见;社区面经亦多

## 考察点

- 能否分清「全局行为约束」与「按需加载的任务手册」
- 是否理解两者对上下文成本、可维护性、跨工具复用的产品含义
- 能否给出选型:什么进 Rules,什么做成 Skill

## 参考答案

**一句话对比**:Rules(含系统提示词/项目约定)是「这个 Agent 一直要遵守的规矩」;Skill 是「这类任务临时打开的操作手册」。

| 维度 | Rules / 系统提示词 | Agent Skills |
|------|-------------------|--------------|
| 作用范围 | 全局或会话级,长期生效 | 按任务匹配触发 |
| 加载方式 | 通常整段进入上下文 | 渐进披露:先描述,匹配后再读全文 |
| 形态 | 一段或几段文本 | 文件夹 + `SKILL.md`(+ 脚本/资料) |
| 适合放什么 | 安全红线、语气人格、仓库默认规范 | 领域 SOP、可复用工作流、专用检查清单 |
| 失败模式 | 越写越长,互相打架、又贵又钝 | description 写糊导致触发不准 |

**产品选型口诀**:
- 每轮对话都该生效、且很短 → **Rules**
- 只在某类任务才需要、内容会长 → **Skill**
- 要跨 Cursor / Codex / Claude Code 等带走 → 优先标准化 **Skill** 资产

可记:Rules 管「人设与红线」,Skill 管「怎么把某类活干对」。二者叠加,不是二选一。更完整的机制见正课 [Agent Skills](../../01-ai-basics/llm/agent-skills.md)。

## 追问延伸

- **追问 1**:"团队把所有规范都塞进 Rules,有什么问题?"
  → 上下文被长期占用、token 成本升、无关任务也被「规范噪音」干扰;规范一改要动全局配置,回归面大。
- **追问 2**:"Skill 会不会取代 MCP?"
  → 不会。Skill 是知识/剧本层,MCP 是连接外部系统的协议层;接得上不等于做得对,两者互补。
- **追问 3**:"你怎么验收一条 Skill 写得好不好?"
  → 看 description 能否让 Agent「该触发时触发、不该时不触发」;看步骤是否可执行、是否有边界案例;看无关任务是否被误触发。

## 相关阅读

- [Agent Skills:给 Agent 装上可复用的「操作手册」](../../01-ai-basics/llm/agent-skills.md)
- [Skill 怎么做版本管理与依赖治理?](skill-versioning-and-dependencies.md)
- [如何设计 Skill 来降低 token 消耗?](skill-token-efficiency.md)
- [MCP:给 Agent 接上外部世界的「USB-C」](../../01-ai-basics/llm/mcp.md)