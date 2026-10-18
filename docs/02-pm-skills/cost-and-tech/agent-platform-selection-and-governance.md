---
title: agent-platform-selection-and-governance
type: note
permalink: aipmwiki/docs/02-pm-skills/cost-and-tech/agent-platform-selection-and-governance
---

# Agent 平台怎么选：运行时、成本与治理一起决策

> **层级：进阶。** 先修：[Agent 入门](../../01-ai-basics/llm/agent-basics.md)、[上下文工程](../../01-ai-basics/prompt-engineering/context-engineering.md)、[生产化](../ai-product-operations/agent-product-design.md)。

平台选型不是“哪家模型分数最高”。Agent 真正依赖的是一整套运行时：模型循环、工具连接、会话状态、上下文压缩、执行环境、权限、审计、评测与回滚。本篇的目标是让 AI PM 在读完后，能把一个 Agent 需求填进决策表，给出平台选择、保留的自建部分，以及不满足条件时的退出方案。

## 一、先把“平台”拆成六层

| 层 | 解决的问题 | 选型问题 |
|---|---|---|
| 模型与路由 | 推理、工具选择、成本 | 是否支持所需语言、工具、结构化输出与版本钉死？ |
| Agent Harness | 循环、规划、重试、子 Agent | 谁负责上下文压缩、并行、失败恢复？ |
| 执行环境 | 文件、代码、网络、浏览器 | 沙箱是否隔离？能否配置 CPU/GPU、网络和密钥？ |
| 状态与记忆 | 会话、任务进度、长期偏好 | 能否跨重启恢复？租户与删除边界在哪里？ |
| 工具与协议 | Function Calling、MCP、内部 API | 工具发现、鉴权、幂等、异步结果是否统一？ |
| 治理与运营 | 评测、审计、预算、回滚 | 是否能关联 task/trace、版本和责任人？ |

只比较模型价格，会漏掉自建队列、重试、日志、沙箱和人工接管成本。只比较托管能力，也会漏掉数据驻留、供应商锁定和业务服务端鉴权。

### 架构责任图：SDK 不等于运行时

SDK 是开发工具包，帮助团队调用模型、组织工具和事件；Harness 是模型执行配套机制，Runtime 是实际运行与维护会话的环境。厂商可能把它们打包出售，名称本身不能证明包括哪些责任。

```text
业务负责人：需求、成功定义、试点范围
    ↓
应用/UX：预览、确认、取消、结果展示
    ↓
SDK + Harness：模型循环、上下文组织、工具选择
    ↓ 运行于
Runtime：计算环境、会话持久化、资源与网络边界
    ↓ 调用（仍需逐次鉴权）
业务工具服务端：租户/资源权限、幂等、审批规则、业务回执
    ↘ 日志与费用导出 → PM/工程/IT共同验收
```

自建方案中工程团队承担中间三层；托管方案可以转移运行责任，但应用方仍要验证服务端业务控制。迁移测试至少导出一条含暂停/重试的任务、对应策略版本和费用记录，在替代环境中重建可读状态；做不到就把它记为退出成本。

## 二、四种常见方案

| 方案 | 适合 | 优点 | 代价与风险 |
|---|---|---|---|
| 单次模型 + 自建工具 | 固定问答、抽取、短流程 | 成本和行为最容易控制 | 需自建状态、重试和观测 |
| 自建 Workflow/Agent | 复杂业务、强数据边界 | 状态机和权限可按业务定制 | Harness、sandbox、恢复都由团队维护 |
| 托管 Agent Runtime | 长任务、多工具、需要沙箱 | 快速获得会话、压缩、恢复、执行环境和观测 | Beta 变更、区域/配额、锁定风险 |
| 混合平台 | 模型或数据不能集中托管 | 保留关键业务服务和权限，借用托管 Harness | 跨平台 trace、版本和故障归因更难 |

2026 年的厂商产品已经把“长时间运行的 Agent”作为基础设施问题处理。OpenAI 的 Agents API（2026-09-10）目前是 Public Beta，托管 session orchestration、context compaction、recovery、tool search 和 subagents，可使用 OpenAI sandbox 或合作方/自有基础设施。Google Cloud 在 2026-07-29 的平台更新中将 Agent Runtime、Memory Bank、Identity、Gateway、Registry 作为面向用户的能力，并称 Agent Runtime 可连续运行最长 7 天。AWS 在 2026-08-06 发布 Runtime Instances，文章描述 AWS 管理的 EC2、共享 session 最长 14 天、GPU 和暂停恢复；该页面没有明确写 GA，区域与资格需另查。

这些是厂商事实，不是“托管就一定更可靠”的证明。平台只减少基础设施工作，业务权限、完成标准、工具幂等和评测仍由产品团队负责。

## 三、可填写的决策表

先用 0—3 分评分，0 表示不满足，3 表示证据充分。没有证据的项填 0，不要用“应该可以”。

| 维度 | 权重 | 自建 | 托管 A | 托管 B | 证据/待验证 |
|---|---:|---:|---:|---:|---|
| 任务持续时间与恢复 | 3 |  |  |  | session 上限、重启恢复演练 |
| 数据驻留与租户隔离 | 3 |  |  |  | DPA、区域、服务端鉴权 |
| 工具/MCP 兼容 | 2 |  |  |  | 工具 schema、异步、MRTR |
| 沙箱与网络控制 | 3 |  |  |  | 出网、文件、密钥、浏览器 |
| 评测与追踪 | 3 |  |  |  | trace、online monitor、导出 |
| 成本可预测性 | 2 |  |  |  | 每任务 token、工具、人工成本 |
| 回滚与迁移 | 3 |  |  |  | 版本钉死、数据导出、替代路径 |
| 组织治理 | 2 |  |  |  | 身份、目录、审批、审计 |

决策规则：高风险写操作、跨租户数据或不可逆动作任一项没有服务端证据，不能仅凭模型能力分数上线；总分接近时，选择迁移成本更低、失败出口更清楚的方案。

## 四、内部 IT 服务助手演算

教学场景完全虚构。员工 E01 属于 tenant-A；助手可以回答带生效制度和引用的问题，可以生成设备申请草稿，也可以只读查询申请状态。员工确认后才提交，业务系统返回申请 ID；主管审批和 IT 交付由人完成。

| 需求 | 平台能力 | 不能交给平台默认决定的事 |
|---|---|---|
| D01 现行设备标准问答 | 只读检索、引用、版本过滤 | 不能把过期 D02 当现行制度 |
| 设备申请草稿 | 结构化输出、短期状态、预览 | 不能绕过 E01 确认直接提交 |
| 提交申请 | MCP/内部 API、幂等 request_key、业务回执 | 不能代主管审批、不能把 submitted 说成 approved |
| 查询状态 | 每次实时读取业务系统 | 不能从长期记忆推断最新状态 |

选择平台时，把“提交申请”作为硬门槛：平台可以托管上下文和 Agent 循环，但最终写入必须回到业务服务端鉴权；超时先按 request_key 查询，不能盲重试。若托管平台不能导出 task/trace 或不能限制跨租户工具，宁可保留 Workflow/自建执行层。

### 权限—记忆—费用矩阵（虚构教学）

| 对象 | 权限边界 | 可持久保存的内容 | 费用与责任 |
|---|---|---|---|
| E01 员工 | 只读适用制度与本人申请；确认后本人提交 | 明确授权的界面语言；任务状态另存 | 任务关联员工/租户，不能因重试重复计业务结果 |
| M01 主管 | 在业务系统审批授权部门申请 | 审批记录在业务系统，不能作个人偏好 | 人工审批时间与模型成本分别统计 |
| IT01 经办 | 按工单授权处理异常和交付 | 必要交接记录，敏感内容最小化 | 接管次数、时长及制度维护成本 |
| ADM01 管理员 | 配置连接器、配额和审计，无默认代审批权 | 配置及审计版本，不读取所有私人记忆 | 设租户预算、告警和封顶；核对共享额度 |

基于这个低风险起步场景，本教学选择“确定性业务工具＋可替换的模型调用”，先保留既有审批系统。只有跨应用长任务确实需要持续环境、恢复与导出能力时，再评估托管运行时。选择不是固定结论：若自建维护超出团队能力、托管边界通过验证，可以改变，但必须保留业务权限和退出方案。完整输入见[实践资料包](../../../assets/internal-service-assistant/teaching-materials.md)。

## 五、治理清单

- 每个 Agent 有负责人、用途、版本、运行环境、数据域和停用日期。
- 每个工具标记只读、可逆写、不可逆写和是否需要确认；服务端重新鉴权。
- 记录 task_id、trace_id、模型/Prompt/工具版本、权限决策、确认人、业务回执和费用。
- 上线前跑核心、边界、安全、历史 Badcase 四类评测；模型、工具或路由变更自动回归。
- 设单任务步数、时间、token、工具和人工预算；触顶进入 blocked 或转人工。
- 托管平台变更前确认数据导出、旧版本保留、MCP/工具兼容和一键切回。

## 相关阅读

- [Agent 产品生产化：从会执行到可负责地完成任务](../ai-product-operations/agent-product-design.md)
- [Agent 记忆怎么设计：写入、读取、更正与删除](../ai-product-operations/agent-memory-design.md)
- [Agent 评测与可观测性：从最终答案追到完整轨迹](../ai-product-operations/agent-evaluation-and-observability.md)
- [MCP：给 Agent 接上外部世界的「USB-C」](../../01-ai-basics/llm/mcp.md)

## 参考资料

- [Introducing the Agents API](https://openai.com/index/introducing-the-agents-api/) — OpenAI，2026-09-10，Public Beta，访问 2026-09-14。
- [What’s new in Gemini Enterprise Agent Platform](https://cloud.google.com/blog/products/ai-machine-learning/whats-new-in-gemini-enterprise-agent-platform) — Google Cloud，2026-07-29，厂商称相关能力 GA，访问 2026-09-14。
- [Runtime instances](https://aws.amazon.com/blogs/aws/runtime-instances-persistent-compute-for-production-ai-agents-on-amazon-bedrock-agentcore/) — AWS，2026-08-06，Launch，访问 2026-09-14。
- [Agent and Model Evaluations are now GA](https://developers.googleblog.com/agent-and-model-evaluations-in-gemini-enterprise-agent-platform-are-now-ga/) — Google，2026-07-31，GA，访问 2026-09-14。
