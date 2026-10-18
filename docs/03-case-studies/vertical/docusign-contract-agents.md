---
title: docusign-contract-agents
type: note
permalink: aipmwiki/docs/03-case-studies/vertical/docusign-contract-agents
---

# Docusign 合同 Agent：把协议上下文接到可治理的业务动作

**分类**: 垂直行业（合同 / 法务）
**资料核验时间**: 2026 年 9 月（基于公开资料，未进行付费账户实测）

> **证据边界**：本文依据 Docusign 官方产品资料、产品路线图和 2026 年第二财季 SEC 披露整理。功能可用范围、套餐、地区、语言和发布阶段以官方页面为准。文中“已推出”表示官方发布或财报披露，不表示作者实测；Docusign 宣称的效率或 ROI 不作为独立验证的业务结果。

## 一、产品背景

合同工作经常被拆散在起草、审阅、谈判、签署、履约和续约多个系统里。法务关心条款风险与审批权限，销售关心成交速度，采购关心供应商续约和价格，财务关心义务、付款与收入确认。传统电子签名解决了“签字”这个节点，却没有自动理解完整协议上下文，也没有把结论稳定地传到后续业务流程。

Docusign 把 Intelligent Agreement Management（IAM）定位为围绕协议全生命周期的管理平台，并以 Iris 作为合同领域 AI 引擎。2026 年 9 月 3 日披露的第二财季材料称，平台已推出由 Iris 驱动的 agentic tools，用于理解协议内容、自动化工作和采取行动；同一披露显示 IAM 占总 ARR 的 15.1%，上一季度为 12.6%。这里的 IAM ARR 是平台口径，**不能据此推出 Iris 或合同 Agent 的独立收入**，也不能把增长直接归因于某一个 Agent。

## 二、核心功能拆解

公开材料呈现的主流程可以抽象为“协议进入 → 结构化理解 → 按规则判断 → 生成或触发动作 → 留下治理记录”：

| 环节 | 公开功能 | AI / Agent 起作用的位置 | PM 要核验的边界 |
|---|---|---|---|
| 进入与检索 | Agreement Manager、协议历史与关系信息 | Iris 基于协议上下文回答问题、提取条款和义务 | 哪些协议被索引、用户能查到哪些租户和文件 |
| 审阅与谈判 | AI assistant、按公司标准检查、建议修改和 redline | 将合同条款与 playbook、已接受条款进行比较，生成建议语言 | 建议是否必须人工复核；法规、模板和 playbook 的版本 |
| 重复业务任务 | agreement intake、vendor renewal 等预置 Agent | 按预先定义的步骤整理信息、生成续约简报、触发下一步 | 预置流程的适用地区、语言、套餐与失败转人工路径 |
| 个性化编排 | Agent Studio | 用自然语言描述业务规则，构建、治理和部署自定义 Agent | 工具权限、可执行动作、审批节点、测试集与回滚 |
| 连接业务系统 | Workflow Builder、Salesforce 等集成 | 将 Agent 决策接入传统工作流的路由和动作 | 外部系统授权、重复触发、幂等键和审计关联 |
| 外部 Agent 调用 | Docusign MCP Server 与连接器 | Claude、ChatGPT、Gemini、Copilot、Slack 等可通过自然语言查询和触发协议动作 | 公开资料称 MCP 在 2026 年 9 月仍处于开放 beta；可用性与 GA 日期需重新核验 |

一个可教学的合同续约例子是：Agent 找出未来 90 天到期的供应商协议，读取价格、自动续期、通知期和责任上限，依照采购 playbook 生成续约简报；若价格变更超过审批阈值，则创建审批任务并把证据链附上。它可以减少检索和整理，但是否续约、是否接受新条款、是否发送正式合同仍应由有权限的人或既有业务系统确认。不要把“生成建议”“创建任务”和“完成签署”混成同一成功事件。

## 三、技术方案推测

以下是基于公开功能的推测，不能当作 Docusign 未公开的内部架构：

1. **领域上下文层**：Iris 需要读取协议文本、结构化元数据、版本关系、历史动作和权限上下文。产品页强调“full agreement context”，但公开资料没有给出索引、分块、嵌入模型或召回率，因此不能进一步断言具体 RAG 实现。
2. **规则与 Agent 编排层**：Agent Studio、Workflow Builder 和 playbook 共同构成“模型判断 + 确定性业务规则”的混合链路。高风险动作不应只由自然语言提示控制，应有工具白名单、字段级权限、审批和可追溯轨迹。
3. **工具接入层**：MCP Server 把协议查询和动作暴露给外部 Agent。Docusign 公开描述其提供企业级安全、权限与治理；PM 仍应要求验证 OAuth 范围、租户隔离、用户级授权、写操作确认、超时重试和撤销能力。
4. **评测与运营层**：合同 Agent 的评测单位至少要拆成条款抽取、风险判断、建议采纳、审批创建和动作完成。单看“回答是否像法务”无法证明任务完成，也无法证明降低了合同周期。

## 四、商业模式

Docusign 没有在上述公开材料中给出“每次 Agent 执行”的统一公开价目。当前更稳妥的产品判断是：Agent 能力被嵌入 IAM 的套餐与企业销售体系，具体资格和价格需以客户合同、套餐页及销售报价确认。2026 年 9 月 3 日财报披露了总收入、总 ARR 和 IAM 占比，但没有把 Agent 单独拆成收入项；因此不能用 IAM ARR 代替 Agent ARR，也不能把公司对“AI 加速增长”的叙述当作因果证明。

成本侧除了模型调用，还包括协议解析、存储与索引、权限治理、审阅人员、法务 playbook 维护、外部系统集成和错误返工。若采用按结果或按工作流收费，合同应明确“step、action、审批任务、完成的协议”各自的定义，规定失败重试、人工升级和取消如何计费。结果型定价必须能从审计日志重建计费事件，避免把一次失败重试算成多次业务结果。

## 五、PM 视角的启示

**先把专业上下文做成可用资产。** 合同 Agent 的差异点不是泛化聊天，而是协议、条款、模板、历史版本和 playbook 被组织成能驱动下一步的上下文。立项时要先盘点数据质量、版本关系和权限，而不是先选模型。

**把“建议”和“动作”拆开验收。** 风险标记、redline 建议、生成审批任务、发送协议分别有不同责任人和失败代价。每一步都应有状态、证据、权限和人工接管。对外宣称“端到端执行”时，必须列出哪些动作仍需批准。

**开放连接器扩大价值，也扩大治理面。** MCP 让用户在熟悉的 AI 工具里调用协议能力，降低切换成本；同时第三方上下文、权限和写操作边界更复杂。上线前至少准备租户隔离、最小权限、动作确认、幂等重试、审计检索和撤销演练。

**商业指标不能替代业务证据。** IAM ARR 的变化可以说明平台商业化采用的一个公开信号，不能说明某个 Agent 的 ROI。真实评估需要前后对照或受控试点，记录合同周期、人工复核时间、错误返工、审批等待和风险事件，并区分厂商自述、客户试点和独立验证。

## 参考资料

- [Docusign Announces Second Quarter Fiscal 2027 Financial Results（SEC Exhibit 99.1）](https://www.sec.gov/Archives/edgar/data/1261333/000126133326000088/q227ex-991er.htm) — Docusign，2026-09-03 发布；IAM ARR、Iris Agent、Agent Studio、MCP 与连接器的公司披露；访问 2026-09-14。
- [Docusign Iris: Agents](https://www.docusign.com/products/agents) — Docusign 官方产品页，功能与治理能力为厂商自述；访问 2026-09-14。
- [Docusign Product and Features Roadmap](https://www.docusign.com/releases/roadmap) — Docusign 官方路线图，标注功能的 Live / Coming Soon、地区、套餐和英语限制；访问 2026-09-14。
- [Orchestrating Agentic Agreement Workflows with Docusign Iris](https://www.docusign.com/blog/developers/developers-guide-docusign-iris-agentic-capabilities) — Docusign 开发者博客，解释 Agent、Workflow Builder 与 MCP 的公开用法和可用范围；访问 2026-09-14。
- [Docusign Agreement Layer for the Agentic Enterprise Coming to Every Agent](https://www.docusign.com/en-ca/company/news-center/docusign-agreement-layer-for-the-agentic-enterprise-coming-to-every-agent) — Docusign 新闻稿，2026-09-04；MCP 计划 2026-09-30 GA 的厂商计划，访问 2026-09-14，不能提前写成已 GA。

## 相关阅读

- [AI 产品定价与商业化：订阅、Token、按结果怎么选](../../02-pm-skills/cost-and-tech/ai-pricing-and-monetization.md)
- [AI 产品指标体系：从业务结果到成本与风险](../../02-pm-skills/ai-product-operations/ai-product-metrics.md)
- [AI 安全与 Agent 权限：把「能做什么」变成可治理的边界](../../02-pm-skills/ai-product-operations/guardrails-and-agent-security.md)
- [MCP：让 AI 模型安全调用外部工具](../../01-ai-basics/llm/mcp.md)
