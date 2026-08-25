---
title: when-not-to-use-multi-agent
type: note
permalink: aipmwiki/docs/02-pm-skills/ai-product-operations/when-not-to-use-multi-agent
---

# 何时不该上多 Agent：延迟、成本、工具膨胀与劝退

多 Agent 很适合写进路线图，却不适合默认当成「更先进的架构」。生产里常见的真相是：多 Agent 用更多 token、更多跳数、更多协调状态，换取并行覆盖与上下文隔离；**若任务并不卡在单 Agent 的硬约束上，协调税往往大于收益。**

> **事实**：Anthropic 在工程实践中建议先找最简单方案，必要时再增加复杂度；其公开测试称，多 Agent 实现相对单 Agent 完成同类任务通常多消耗约 **3–10×** token。其研究型多 Agent 系统公开数据则指出：相对普通对话，Agent 约 **4×** token、多 Agent 约 **15×** token。见文末资料（截至 2026-08-06）。
>
> **建议**：立项时先问「单 Agent / Workflow / 单次 LLM+检索」是否已经够用；把多 Agent 当成**有条件的升级**，而不是默认终点。选题结构启发自磊叔《关于 AI 产品经理的 100 个问题》Q16 / Q17 / Q41 / Q43 / Q99，正文为原创重写。

概念铺垫见 [Agent 入门](../../01-ai-basics/llm/agent-basics.md)；任务契约、预算与人工介入见 [Agent 产品生产化](agent-product-design.md)；路由错了怎么救见面试题 [Agent 路由与超大文件](../../04-interview/basics/agent-routing-and-large-files.md)；劝退口径面试答法见 [什么场景不该用 Agent](../../04-interview/basics/when-not-to-use-agent.md)；分层路由的负优化清单见 [成本优化 · 路由失败模式](../cost-and-tech/cost-optimization.md#十一路由失败模式分层很美落地常翻车)。

## 一、先分清三层：别把「上 Agent」和「上多 Agent」混谈

| 层级 | 典型形态 | 主要代价 | 常见误判 |
|------|----------|----------|----------|
| 单次 LLM（可加 RAG/示例） | 问答、摘要、抽取 | 相对最低 | 明明一步够用，却做成 Agent |
| 单 Agent / Workflow | 多步工具循环，或固定编排 + 局部模型判断 | 步数与重试带来延迟/成本不确定性 | 把可写死的流程交给模型「自由规划」 |
| 多 Agent | 多个独立上下文 + 编排/交接 | 上下文复制、交接摘要、协调失败点翻倍 | 用「角色拆分」制造伪专业，却没有真并行或真隔离需求 |

Anthropic 把**预定义路径编排**称为 Workflow，把**由模型动态决定步骤与工具**称为 Agent。OpenAI 的 Agent 指南也强调：先验证用例是否真需要 Agent，否则确定性方案往往更合适。对 PM 而言，决策顺序应是：

```text
任务是否多步骤、需外部动作、结果可验证？
  ├─ 否 → 单次 LLM / 规则 / 表单即可
  ├─ 是，且路径大体可写死 → Workflow（关键节点可调模型）
  ├─ 是，且路径开放、需动态规划 → 受约束的单 Agent
  └─ 单 Agent 撞上硬约束（上下文污染 / 真并行 / 专精工具面）→ 再考虑多 Agent
```

## 二、延迟与成本：跳数上升时发生了什么

多 Agent 的账单和体感延迟，很少只来自「多调了几次模型」。更常见的放大器是：

1. **上下文复制**：每个子 Agent 都要带着自己的系统提示、工具说明与任务说明开跑。
2. **交接摘要**：主编排要把中间结果压缩后再注入下一跳；摘要丢信息 → 返工 → 再烧一轮。
3. **串行协调**：子任务其实有依赖时，fan-out 并不能缩短墙钟时间，却仍付多份 baseline。
4. **失败重试**：任一子 Agent 空转或选错工具，整条链路的步数上限更容易被顶穿。

可用一张**定性对照表**跟老板对齐预期（数字用自有评测与灰度账单填，不要背外部「行业翻倍」口号）：

| 观察项 | 单 Agent / Workflow 基线 | 多 Agent 候选方案 | 判定问题 |
|--------|--------------------------|-------------------|----------|
| 端到端 P50 / P95 延迟 | 测出基线 | 灰度对比 | 用户可感变慢是否换来更高任务成功？ |
| 单任务 token / 费用 | 测出基线 | 对照倍数 | 业务价值是否覆盖协调税？ |
| 任务成功率 / 人工接管率 | 基线 | 对照 | 成功率持平或下降 → 架构负优化 |
| 可观测性 | 一条轨迹 | 多条轨迹 + 交接 | 出问题能否在 SLA 内定位？ |

**什么时候延迟翻倍「可能值得」**（仍需用评测证明，不是口号）：

- 信息面大、需要**并行覆盖**（多源检索、多角度调研），且任务价值足够高；
- 单上下文会被无关中间结果**污染**，隔离后主路径质量明显更好；
- 工具面过宽，拆成专精子 Agent 后**选型准确率**上升（见下一节）。

**什么时候更像自嗨**：

- 步骤少、路径稳定（改状态、填表、固定审批链）；
- 用户在对话里等结果，P95 延迟直接打穿体验预算；
- 子任务强依赖、几乎无法并行，却仍拆成「规划 Agent / 执行 Agent / 审核 Agent」流水线——协调成本高、交接丢上下文风险高；
- 还没有任务级评测与成本看板，就先上「更炫的编排」。

Anthropic 对其研究系统的公开结论可作产品口径：**多 Agent 适合高价值、可并行、信息超出单窗、工具面复杂的任务；共享同一大上下文或强依赖协调的领域（例如许多编码任务）往往不划算。** 这是原厂经验边界，不是你司财务红线——最终仍用自有单位任务价值对比。

## 三、工具膨胀：工具从「够用」到「拖累」

工具不是能力清单越长越好。工具定义会占用上下文；候选一多，模型就要在更大决策面里选对工具。Anthropic 公开文档指出：典型多 MCP 服务器组合，仅工具定义就可能消耗约 **55k** token；工具选择能力在可用工具超过约 **30–50** 个后会明显变差。其 Advanced Tool Use 工程文给出的内部评测：为大工具库启用按需检索后，Opus 4 选型准确率约从 **49% → 74%**，Opus 4.5 约从 **79.5% → 88.1%**（截至 2026-08-06 访问）。

对 PM，这意味着：

| 信号 | 产品含义 | 优先动作 |
|------|----------|----------|
| 工具数快速涨、成功率不涨或下降 | 决策面过宽 / 描述冲突 | 砍重叠工具、合并同义工具、按场景分组暴露 |
| 每轮固定烧掉大量「工具说明」token | 上下文税 | 动态加载 / Tool Search / 按任务挂载工具包 |
| 相似工具名（send_user vs send_channel） | 误调用与副作用风险 | 改名、收紧 schema、高风险动作强制确认 |
| 「再加一个 MCP 就完整了」成为默认需求 | 能力通胀 | 用评测集证明增量工具带来净收益 |

**不要把 PDF 或口头传说里的「成功率掉了百分之几十」当成普适红线。** 正确做法是：固定任务评测集 → 对比「精简工具包 vs 膨胀工具包」的成功率、错工具率、步数与费用 → 用净收益决定是否回滚工具面。生产化里「工具当产品接口」的契约写法见 [Agent 产品生产化](agent-product-design.md#工具调用要当作产品接口设计)。

## 四、上线后延迟暴增：先砍 Agent，还是先砍功能？

不要一上来二选一。先做**归因分层**，再动刀：

```text
延迟暴增
  ├─ 1. 量：简单意图是否错误触发了多 Agent / 深规划？
  ├─ 2. 宽：工具面是否过大，导致空转与重试？
  ├─ 3. 深：最大步数 / 子 Agent 数是否无上限？
  ├─ 4. 串：所谓并行是否其实在串行等待？
  └─ 5. 外：工具 API / 检索本身变慢（与 Agent 拓扑无关）
```

| 归因 | 优先砍什么 | 说明 |
|------|------------|------|
| 简单请求误入重路径 | **路由门槛**与默认路径 | 先走 Workflow / 单次调用；复杂才升级。对应[路由失败模式](../cost-and-tech/cost-optimization.md#十一路由失败模式分层很美落地常翻车) |
| 子 Agent 过多或交接过碎 | **砍 Agent 拓扑** | 合并角色；能单 Agent 完成的不要硬拆 |
| 工具空转、错工具 | **砍 / 分组工具** | 比继续加子 Agent 更便宜 |
| 功能承诺了「全自动多跳」但价值低 | **砍功能范围** | 改成半自动、异步批处理、或人工确认后的短链路 |
| 外部依赖变慢 | **动基础设施与超时策略** | 换拓扑解决不了 DB/API 抖动 |

经验顺序：**先收默认路径与预算上限 → 再瘦工具面 → 再合并 Agent → 最后才砍对用户可见的功能。** 「砍功能」应发生在：即使用最简可靠拓扑，单位任务成本或 P95 仍不可接受，或任务价值本身撑不起 Agent 税。

## 五、怎么写一份「这个场景不该用（多）Agent」的劝退材料

老板要的通常不是技术否定，而是**可签字的取舍**。建议一页纸结构：

1. **用户任务与成功证据**：完成标准是业务证据，不是模型自称完成（对齐[任务卡](agent-product-design.md#一先定义任务不先定义-agent)）。
2. **候选方案对照**：规则/单次 LLM / Workflow / 单 Agent / 多 Agent，各一列：效果假设、延迟、成本、可控性、上线工期。
3. **自有基线数据**：哪怕是小流量灰度——P50/P95、单位任务费用、成功率、接管率。缺数据就写清「两周内补齐评测，当前不扩大多 Agent 投入」。
4. **硬约束检查**：是否存在上下文污染、真并行、专精工具面？三项皆无 → 默认不批多 Agent。
5. **劝退结论 + 替代路径**：例如「先上 Workflow + 人工确认；评测证明单 Agent 不够再开子 Agent」。
6. **复盘触发条件**：何种指标组合出现时，才允许重新立项多 Agent（避免「被竞品叙事再次绑架」）。

口头对齐可用三句：

- 「多 Agent 是用更高的协调税，换并行与隔离；我们还没证明税值得付。」
- 「现在成功率瓶颈在工具面/评测/权限，不在 Agent 个数。」
- 「先把单路径做到可观测、可熔断；再谈编排复杂度。」

## 六、PM 决策清单（可直接贴进评审）

- [ ] 已证明单次 LLM / Workflow / 单 Agent **不够**，且不够的原因属于「上下文 / 并行 / 专精」之一。
- [ ] 有任务级评测集，能对比架构变更前后的成功率与错工具率。
- [ ] 有单位任务成本与 P95 延迟预算；多 Agent 的期望收益能覆盖协调税。
- [ ] 工具包按场景裁剪；不把「所有 MCP」一次性暴露给同一 Agent。
- [ ] 简单意图有便宜默认路径；升级多 Agent 有明确门槛。
- [ ] 步数、子 Agent 数、费用、时间均有上限，触顶可解释地停止或转人工。
- [ ] 轨迹可观测到交接边界，坏案例能回流评测（见 [Agent 评测与可观测性](agent-evaluation-and-observability.md)）。

## 相关阅读

- [Agent 入门：规划、记忆、工具调用](../../01-ai-basics/llm/agent-basics.md)
- [Agent 产品生产化：从会执行到可负责地完成任务](agent-product-design.md)
- [Agent 路由选错了怎么办？超大文件又该怎么接？](../../04-interview/basics/agent-routing-and-large-files.md)
- [什么是 Agentic Workflows?](../../04-interview/basics/agentic-workflows.md)
- [降本手段盘点：缓存、批处理、路由与蒸馏](../cost-and-tech/cost-optimization.md)（含路由失败模式）
- [Agent 评测与可观测性](agent-evaluation-and-observability.md)
- [AI 产品指标体系](ai-product-metrics.md)
- [什么是 Agent？它和 Chatbot 的区别是什么?](../../04-interview/basics/agent-vs-chatbot.md)
- [所有人都在卷 Agent，什么场景不该用？怎么说服老板？](../../04-interview/basics/when-not-to-use-agent.md)

## 参考资料

- [Building effective agents](https://www.anthropic.com/engineering/building-effective-agents) —— Anthropic 工程博客：先简单、再按需增加复杂度；Agent 用延迟与成本换效果，2026-08-06 访问
- [When to use multi-agent systems (and when not to)](https://claude.com/blog/building-multi-agent-systems-when-and-how-to-use-them) —— Anthropic/Claude：多 Agent 约 3–10× token；适用「上下文隔离 / 并行 / 专精」三类约束，2026-08-06 访问
- [How we built our multi-agent research system](https://www.anthropic.com/engineering/multi-agent-research-system) —— Anthropic：Agent ≈4×、多 Agent ≈15× 相对 chat 的 token；适用边界说明，2026-08-06 访问
- [Introducing advanced tool use on the Claude Developer Platform](https://www.anthropic.com/engineering/advanced-tool-use) —— Anthropic：大工具库下 Tool Search 与选型准确率评测数字，2026-08-06 访问
- [Tool search tool](https://platform.claude.com/docs/en/agents-and-tools/tool-use/tool-search-tool) —— Claude 文档：约 30–50 工具后选型劣化；多服务器工具定义约 55k token 量级，2026-08-06 访问
- [A practical guide to building AI agents](https://openai.com/business/guides-and-resources/a-practical-guide-to-building-ai-agents/) —— OpenAI：何时值得上 Agent；编排从单 Agent 演进，2026-08-06 访问

选题启发：磊叔《关于 AI 产品经理的 100 个问题》Q16、Q17、Q41、Q43、Q99（仅借鉴问题结构，不采用其答案表述）。