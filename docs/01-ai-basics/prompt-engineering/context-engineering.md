---
title: context-engineering
type: note
permalink: aipmwiki/docs/01-ai-basics/prompt-engineering/context-engineering
---

# Context Engineering：从写 Prompt 到拼上下文系统

> 选题启发自磊叔《关于 AI 产品经理的 100 个问题》Q4 / Q10；正文为本库原创，未摘录该 PDF 答案原文。

写好一条 Prompt，只能决定「模型被怎么吩咐」；真正决定线上效果的，往往是**这一次调用里窗口里到底有什么**——系统指令、检索片段、对话历史、工具定义与工具返回、用户画像、业务规则……这些拼在一起，才是模型「看见」的世界。

业界把这件事叫做 **Context Engineering（上下文工程）**：不是再写一句更巧的指令，而是设计一套**动态拼装与维护上下文**的系统。Anthropic 在工程博客中将其定义为：在推理时**策展并维持最优 token 集合**的策略集合，且明确把它视为 Prompt Engineering 的自然延伸——Prompt 工程主要管「怎么写指令」，Context 工程管「窗口里还有哪些会落地的信息、以及如何持续更新这套状态」。（来源见文末参考资料；访问日期 2026-08-06。）

本篇面向 AI PM：讲清概念边界、和 Prompt 工程的对比、窗口里常见部件、生产里常用的工程手段，以及**你该管什么、不该代替工程调参什么**。窗口容量与成本细节见 [上下文窗口](../llm/context-window.md)；单条 Prompt 写法见 [Prompt 基本功](prompt-basics.md)。

## Prompt Engineering vs Context Engineering

| 维度 | Prompt Engineering | Context Engineering |
|------|--------------------|---------------------|
| 核心问题 | 指令怎么写才稳、才可测 | 这一步推理应装进哪些 token、以什么形态装 |
| 典型对象 | System / User 文案、示例、输出格式 | 检索结果、历史消息、工具定义与返回、记忆条目、MCP/Skills 注入物 |
| 时间尺度 | 偏「一次写好 + 版本迭代」 | 偏「每轮/每步动态策展」，长任务里还要压缩与外置记忆 |
| 失败形态 | 指令歧义、格式漂移、边界不清 | 该进的没进、不该进的挤爆窗口、工具结果堆积、缓存失效导致成本飙升 |
| 与工程的交界 | Prompt 模板、变量拆分、评测回归（见 [Prompt 工程化](system-prompt-and-prompt-ops.md)） | 检索策略、工具面设计、compaction / 清理、prompt caching 布局 |
| 对 PM 的抓手 | 产品人格、能力边界、验收标准、示例质量 | **上下文清单与优先级**、哪些场景允许「即时拉取」、成本/延迟预算、失败时降级口径 |

**事实（厂商口径）**：Anthropic 将 Context Engineering 表述为 Prompt Engineering 的进展；Prompt 工程侧重写与组织指令，Context 工程覆盖推理时整份上下文状态（系统指令、工具、MCP、外部数据、消息历史等）。

**建议（本库归纳，非厂商原文）**：面试或立项时，不要把「我们优化过 Prompt」和「我们有上下文系统」混为一谈——前者是文案资产，后者是运行时管线。

## 一次调用里，上下文通常装什么

结合 [上下文窗口](../llm/context-window.md) 的容器观，生产系统里常见的「可策展部件」包括：

```
系统指令 / 开发者指令（相对稳定）
+ 工具与 schema 定义（Agent 场景常很大）
+ 检索/知识片段（RAG 等）
+ 短期对话与中间推理痕迹
+ 工具调用记录与 tool_result
+ 长期记忆中「本次被选中」的条目
+ 用户本轮输入
+ 预留给模型输出的额度
────────────────
≤ 上下文窗口
```

其中：

- **指令类**：仍靠 Prompt 工程写清楚；但要意识到它只占整窗的一小部分。
- **知识类**：多数来自 [RAG](../llm/what-is-rag.md) 或「按需用工具再读」——不是一次性写死在 Prompt 里。
- **工具类**：工具定义本身也占窗口；长跑 Agent 里，历史 `tool_result` 往往是窗口膨胀的主因（见 [Agent 入门](../llm/agent-basics.md)）。

Anthropic 在同文中强调：上下文是**有限且边际收益递减**的资源（他们用 needle-in-a-haystack 类评测讨论随长度增加的检索/专注退化，并使用 *context rot* 表述）。因此「能塞」不等于「该塞」——这与本库在上下文窗口一文里讲的「中间遗忘 / 够用优于越大越好」一致，此处不重复展开机制细节。

## 生产里常见的 Context 策略（事实为主）

下列手段来自公开工程文档与厂商博客；**具体 API 字段、价目、最低可缓存长度会随模型与平台变化，落地前以官方页为准。**

### 1. 静态前缀可复用：Prompt Caching

**事实**：

- **Anthropic**：通过 `cache_control` 等机制对稳定前缀做缓存；命中后可显著降低重复前缀的计费与延迟。官方文档给出的倍率口径包括：约 5 分钟 TTL 的 cache write 为基价输入的 **1.25×**，1 小时 TTL 的 write 为 **2×**，cache read 约为基价输入的 **0.1×**（截至 2026-08-06，见 Anthropic Prompt Caching 文档）。另有按模型区分的最低可缓存 token 门槛；未达门槛则不会真正缓存。
- **OpenAI**：对符合条件的请求可对**精确前缀匹配**自动做 Prompt Caching；官方指南建议把稳定内容（指令、示例、工具定义等）放在靠前位置，可变内容靠后，以提高命中率。较新模型家族还提供显式 breakpoint / `prompt_cache_key` 等控制项（见 OpenAI Prompt Caching 指南；访问日期 2026-08-06）。

**建议**：PM 侧把「哪些字段会每请求变化」（时间戳、用户 ID、随机会话噪声）从「应稳定缓存的系统层」里剔出去，当作产品约束写进 PRD；**不要**自己拍板具体 breakpoint 落在第几个 content block——那是工程实现。

### 2. 工具面与工具结果：别让 Agent「自己把自己撑爆」

**事实**：

- OpenAI 将 Function / Tool Calling 定义为模型通过结构化调用对接外部系统与数据的标准路径；工具定义可计入可缓存前缀，大规模工具集可用 tool search 等能力延迟加载（见 OpenAI Function calling / Tools 文档）。
- Anthropic 在工具上下文管理文档中，把 **prompt caching（降重复工具定义成本）**、**tool search（大工具集按需加载定义）**、**programmatic tool calling**、**context editing / tool-result clearing（清掉过时笨重的工具返回）** 等列为应对「工具定义 + 累积 tool_result 吃光窗口」的不同切入点。

**建议**：PM 定义「工具目录的最小必要集」和「高风险工具必须人工确认」；要求工程对长任务给出窗口占用可观测性（每轮 input tokens、工具结果占比）。工具返回要不要全文进窗、何时摘要/清理，由工程按官方能力选型，PM 只给业务优先级（例如：支付回执必须保留原文 vs 日志可摘要）。

### 3. 长任务：压缩、外置记忆、子 Agent

**事实（Anthropic 工程博客归纳）**：对超出单窗的长程任务，常见做法包括 **compaction（摘要后重启窗口）**、**structured note-taking / memory（笔记落在窗外、需要时再拉回）**、**sub-agent（子代理在干净窗口里深挖，只回传蒸馏摘要）**。Claude 平台亦提供与 compaction、tool clearing、memory tool 相关的产品化能力（见 Anthropic cookbook / docs；能力名与 beta 头以当日文档为准）。

**建议**：产品叙事上把「记忆」说成工程模拟的跨轮状态，而不是「模型真的记住了」；临界体验（触顶是截断、摘要还是转人工）应写进需求，而不是留给模型自由发挥。

### 4. 「预先塞满」vs「即时拉取」

**事实**：Anthropic 描述一类 **just-in-time** 策略——上下文里先放轻量标识（路径、查询、链接），运行时再用工具按需载入，而不是推理前把可能相关的材料全部预取进窗。这与「固定 Top-K 预检索」的 Naive RAG 形成对照，也和 Agentic 检索演进一致。

**建议**：知识变化快、全量预取贵或噪声大时，优先讨论「标识 + 按需读」是否成立；知识稳定且延迟敏感时，可保留部分预取。选型框架链到 [长上下文 vs RAG](../llm/long-context-vs-rag.md)，此处不展开检索算法。

## PM 职责边界

| 该管（产品 / 约束） | 不该代替工程做 |
|--------------------|----------------|
| 任务目标、成功标准、不可接受失败（胡说、越权、泄密） | 具体 Prompt 措辞的逐 token 调参、温度/采样细节 |
| 「上下文清单」：必须有 / 可以有 / 禁止进入窗口的信息类别 | 向量库参数、切块大小、重排模型选型的最终拍板（可参与评审） |
| 成本与延迟预算：单次任务 token 上限、是否允许多轮工具循环 | Cache breakpoint、compaction 触发阈值、清理策略的实现代码 |
| 工具权限与确认策略：哪些动作要人点头 | MCP/工具 schema 的字段级实现 |
| 评测与回归：上下文策略变更是否纳入 eval / 灰度 | 把「感觉更好」当上线依据 |

一句话：**PM 管「要什么上下文、在什么约束下拼、怎样算拼对了」；工程管「怎么高效、可靠、可观测地拼」。** 你深度参与 Prompt 与上下文设计是加分项，但替代工程调参会让系统不可维护，也让责任边界糊掉。

## 给 AI PM 的可操作检查清单（建议）

立项或复盘一次「答得差 / 又贵又慢」的 AI 功能时，按序问：

1. 失败更像**指令问题**还是**材料/历史/工具结果问题**？（前者回 Prompt 工程化；后者开 Context 工程复盘。）
2. 当前窗口里，占比最大的三块是什么？有没有「从不被引用却每轮都带上」的块？
3. 稳定前缀是否被动态字段打散，导致 caching 几乎打不中？（对照厂商「静态在前」建议。）
4. Agent 场景：工具数量是否超过人也能说清「该用哪个」的程度？历史 tool_result 是否只进摘要？
5. 策略变更有没有进评测集与灰度？（与 [系统提示词与 Prompt 工程化](system-prompt-and-prompt-ops.md) 同一纪律。）

## 相关阅读

- [Prompt 基本功:角色、任务、约束、示例、输出格式](prompt-basics.md)
- [系统提示词设计与 Prompt 工程化:把玄学变工程](system-prompt-and-prompt-ops.md)
- [上下文窗口:限制、成本与产品设计影响](../llm/context-window.md)
- [什么是 RAG:检索增强生成入门](../llm/what-is-rag.md)
- [Agent 入门:规划、记忆、工具调用](../llm/agent-basics.md)
- [长上下文 vs RAG:什么时候塞全库,什么时候检索](../llm/long-context-vs-rag.md)
- [MCP:给 Agent 接上外部世界的「USB-C」](../llm/mcp.md)
- [Agent Skills:给 Agent 装上可复用的「操作手册」](../llm/agent-skills.md)
- 面试题:[什么是 Context Engineering？和 Prompt Engineering 有什么区别？](../../04-interview/basics/what-is-context-engineering.md)
- 面试题:[大模型的上下文窗口和记忆机制是什么?](../../04-interview/basics/context-window-and-memory.md)

## 参考资料

- [Effective context engineering for AI agents](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents) — Anthropic Engineering，发布约 2025-09-29；**2026-08-06 访问**
- [Prompt caching](https://platform.claude.com/docs/en/build-with-claude/prompt-caching) — Anthropic 官方文档；**2026-08-06 访问**
- [Manage tool context](https://platform.claude.com/docs/en/agents-and-tools/tool-use/manage-tool-context) — Anthropic 官方文档；**2026-08-06 访问**
- [Prompt caching](https://developers.openai.com/api/docs/guides/prompt-caching) — OpenAI 官方文档；**2026-08-06 访问**
- [Function calling](https://developers.openai.com/api/docs/guides/function-calling) — OpenAI 官方文档；**2026-08-06 访问**
- [Using tools](https://developers.openai.com/api/docs/guides/tools) — OpenAI 官方文档；**2026-08-06 访问**
- [The rise of "context engineering"](https://www.langchain.com/blog/the-rise-of-context-engineering) — LangChain 对术语与「动态提供正确信息与工具」的业界表述；**2026-08-06 访问**
- [Context Engineering](https://www.langchain.com/blog/context-engineering-for-agents) — LangChain 对 write / select / compress / isolate 等策略综述；**2026-08-06 访问**