---
title: agent-routing-and-large-files
type: note
permalink: aipmwiki/docs/04-interview/basics/agent-routing-and-large-files
---

# Agent 路由选错了怎么办?超大文件(如 1G)又该怎么接?

**难度**:⭐⭐⭐ 进阶
**出现频率**:中高,Agent 平台、客服/办公 Agent、Coding Agent 二面常见

## 考察点

- 能否把「路由」当成产品能力:错了如何发现、降级、学习
- 是否理解超大材料不能「整份塞进上下文」
- 能否给出可落地的架构选项与验收指标

## 参考答案

这两问常一起出现,本质都是:**Agent 不能假设自己一次看对、一次看全**。

### 一、路由选错了怎么办

路由=决定「交给哪个子 Agent / 哪条 Skill / 哪类工具」。选错的代价是答非所问、空转工具、成本飙升。

**产品应对分层**:

| 层 | 做法 |
|----|------|
| 事前 | 路由前澄清意图;低置信要求用户点选;白名单高风险链路 |
| 事中 | 子 Agent 返回「能力不匹配」信号;允许重路由/回主控;步数与费用熔断 |
| 事后 | 记录错路由样本进评测集;定期改 description/分类器;人工抽检 |

可对外的体验策略:明确告诉用户「我可能理解偏了,已改用某某能力」,比沉默重试更建立信任。

判断标准:路由准确率、错路由后的恢复率、单任务平均步数与成本、用户手动纠错率。

### 二、超大文件(到 1G 量级)怎么接

**禁止默认方案**:把 1G 原文一次性塞进 Prompt。

可行路径(常组合使用):

1. **外置存储 + 工具读取**:文件进对象存储;Agent 用工具按页/按段/按查询取片段。
2. **索引后再问**:先切块、嵌向量/建全文索引,走 RAG;需要时再打开原文段落。
3. **分层摘要**:地图式目录 → 章节摘要 → 按需深潜;适合「先了解再深挖」。
4. **专用解析流水线**:音视频转写、表格抽库、代码建符号索引——在进 LLM 前变成可检索结构。
5. **人机协同**:先让用户圈定范围(章节、时间段、关键词)再分析。

Coding Agent 场景同理:大仓库靠检索/符号工具/测试命令,而不是把整个 monorepo 贴进上下文。另见 [AGENTS.md](what-is-agents-md.md) 与上下文纪律。

## 追问延伸

- **追问 1**:"多 Agent 一定比单 Agent 强吗?"
  → 不一定。多 Agent 增加路由与协调成本;任务边界清晰、需要隔离上下文时才划算。展开见 [什么场景不该用 Agent](when-not-to-use-agent.md) 与正课 [何时不该上多 Agent](../../02-pm-skills/ai-product-operations/when-not-to-use-multi-agent.md)。
- **追问 2**:"超大文件场景的北极星指标是什么?"
  → 任务完成率、关键事实召回/引用正确率、端到端时延与成本、用户是否需要反复重传文件。
- **追问 3**:"权限上要注意什么?"
  → 文件级 ACL、租户隔离、日志脱敏;工具读取也要鉴权,防止 Agent 被诱导读出不该读的盘。

## 相关阅读

- [Agent 入门:规划、记忆、工具调用](../../01-ai-basics/llm/agent-basics.md)
- [什么是 Agentic Workflows?](agentic-workflows.md)
- [大模型的上下文窗口和记忆机制是什么?](context-window-and-memory.md)
- [RAG 系统有哪几种检索路径?](rag-multi-retrieval-paths.md)
- [兜底、反馈与预期管理](../../02-pm-skills/prd-and-design/fallback-and-feedback-design.md)
- [Agent 产品设计](../../02-pm-skills/ai-product-operations/agent-product-design.md)
- [何时不该上多 Agent：延迟、成本、工具膨胀与劝退](../../02-pm-skills/ai-product-operations/when-not-to-use-multi-agent.md)
- [所有人都在卷 Agent，什么场景不该用？怎么说服老板？](when-not-to-use-agent.md)