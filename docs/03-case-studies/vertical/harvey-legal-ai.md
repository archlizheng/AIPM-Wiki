---
title: harvey-legal-ai
type: note
permalink: aipmwiki/docs/03-case-studies/vertical/harvey-legal-ai
---

# Harvey 案例拆解:法律垂直的高合规企业 AI

**分类**:垂直行业
**体验时间**:2026 年 9 月（基于公开资料，未进行付费账户实测）

## 一、产品背景

法律是典型的高合规、高客单价、强专业壁垒行业:一份合同审查出错、一条引证幻觉、一次客户数据外泄,代价都可能远超一次客服会话失败。通用大模型可以起草、摘要、问答,但律所和企业法务真正买的不是"能聊天",而是能嵌进尽职调查、合同审查、诉讼准备等既有工作流、且经得起合规审计的基础设施。

Harvey 定位自己是律所与企业法务的"法律操作系统 / 法律基础设施"(legal infrastructure / operating system for legal and professional services)。2022 年由前律师 Winston Weinberg 与前 DeepMind/Meta 研究员 Gabe Pereyra 创立。**最新公开公司披露**是 2026 年 9 月 9 日完成 **5.5 亿美元融资、估值 155 亿美元**，由 Diffusion 与 Lightspeed 共同领投；此前 2026 年 3 月的 2 亿美元、110 亿美元估值是历史融资口径，不能继续写成当前最新一轮。

最新融资博文给出的规模口径是：**80% 的 Am Law 100 律所使用 Harvey**，并有五家 Fortune 10 企业使用；这是 Harvey 公司自述，未说明独立审计分母。2026 年 3 月公司还称客户运行超过 **2.5 万个自定义 Agent**；该数字应标注为历史公司披露，不等于当前活跃 Agent 数。公开新闻室在 2026 年 9 月继续列出全所部署、企业部署和产品更新，说明组织采用与平台能力仍在扩展，但不能由新闻标题推出实际使用深度。

与仓库已有的[智能客服案例](ai-customer-service.md)形成对照:**客服卖的是可计费的"会话结果"(解决/转接/线索判定),Harvey 卖的是嵌入法律工作流的高合规生产力平台**——律师仍对最终判断负责,AI 的"结果"定义不是一次性关闭工单,而是完成一段可审计的专业工作流(尽调清单、条款比对、多步 Agent 任务)。

## 二、核心功能拆解

按 Harvey 官网产品矩阵,可以把能力拆成"人机协作层 → 知识与文档层 → 自主执行层":

| 模块 | 做什么 | AI 介入程度 | 合规相关设计点 |
|------|--------|-------------|----------------|
| Assistant | 法律问答、文档分析、起草 | 高 | 领域专用模型/提示,输出需律师复核 |
| Vault | 安全存储、组织与批量分析法律文档 | 高(批量理解/抽取) | 客户文档隔离与访问控制是卖点前提 |
| Knowledge | 跨域法律、监管与税务问题检索研究 | 高 | 答案需可追溯至可信法律内容源 |
| Agents | 端到端执行复杂法律工作流(并购、尽调、基金设立等长周期任务) | 很高 | 多步执行需留痕、可中断、可与律所流程对齐 |
| Shared Spaces | 律所与外部客户/合作方在安全空间协作 | 中–高 | 跨组织权限与数据边界 |
| Contract Intelligence | 合同洞察、谈判与审查加速 | 高 | 条款风险提示仍属辅助,非自动签约 |
| Command Center | 用量、基准与 Agent 洞察,支撑所内 AI 转型管理 | 低–中 | 管理侧可见性,便于治理与采购复盘；公开资料曾标 Early Access |
| Harvey II / Memory | 将 matter、project、权限、历史和个人偏好带入后续 Agent 任务 | 高 | 公司称可查看、更改或关闭记忆，且不用于训练模型；需按租户与案件验证 |
| Agentic Vault Search / Organization | 让 Agent 根据文件夹、命名、上传者等线索查找并整理 Vault 文件 | 高 | 公司称沿用用户权限、ethical walls，并记录触碰文件的审计轨迹 |

一次典型企业侧流程可以概括为:

1. 把案件/交易相关文档装入 Vault →
2. 用 Assistant / Knowledge 做研究与初稿 →
3. 对重复性、多步骤任务启动自定义 Agent（公司 2026-03 披露客户运行超过 2.5 万个）→
4. 律师在关键节点审阅、改写、签字确认 →
5. 必要时在 Shared Spaces 与客户或外部顾问协同。

官方叙事强调:AI 不只是"协助律师",而是成为**法律工作得以执行的系统**;律师应把时间留给判断、策略与结果把关。对 PM 而言,这意味着产品成功指标要从"回答是否流畅"切到"**工作流是否跑完、是否可审计、律师是否敢在客户交付物上署名**"。

## 三、技术方案推测

> 以下内容部分来自官方博客披露,部分为基于产品行为的合理推测,已标注区分。

- **官方已披露**:客户在 Harvey 上部署大量**自定义 Agent**,覆盖并购、尽调、合同起草与文档审查;并出现**长周期(long-horizon)Agent**,可在较长时间跨度内执行多步工作流(官方举例包括基金设立 fund formation)。2026 年 9 月融资博文仍把自建智能和客户工作流作为投入方向，但未披露 Agent 的独立收入或生产成功率。
- **官方已披露(产品矩阵)**:Assistant / Vault / Knowledge / Agents / Ecosystem 等模块并存,暗示不是单一对话框,而是"文档库 + 研究 + 自主执行 + 协作空间"的组合架构。
- **推测**:领域问答与合同分析大概率采用"通用大模型 + 法律语料/检索增强 + 领域后处理"的组合;Knowledge 与引证场景对幻觉极度敏感,推测会强调来源 grounding(具体是否深度绑定某家法律数据库,以厂商当时公开合作为准,本文不展开未核实细节)。
- **推测**:Vault 的批量分析接近"对企业文档库做结构化抽取与问答"的 RAG/索引流水线;Agents 则是在此之上叠加任务规划、工具调用与人工审批闸门——法律场景几乎必然需要**人在回路(human-in-the-loop)**,否则无法满足职业责任与客户合同约束。
- **推测**:SOC/权限、数据驻留、审计日志等企业安全能力是采购门槛而非锦上添花;没有这些,AmLaw 与 Fortune 级客户很难规模化上线。
- **官方已披露（2026-08-18）**：Harvey II 的 Agent 在 matter/project Space 中继承文档、当事方、任务、权限、历史和 playbook；Memory 可跨 Harvey、Word、Outlook 延续偏好，公司称用户可查看、修改或关闭，且不用于训练模型。**这只是产品声明，仍需在具体租户合同和权限配置中验证。**
- **官方已披露（2026-09-08）**：Agentic Vault Search / Organization 可根据文件夹、命名习惯和上传者等线索查找、整理文件，并沿用用户已有权限与 ethical walls；触碰文件写入工作区审计轨迹。

## 四、商业模式(效果指标与计费逻辑)

**与客服"按结果付费"的差异(本文重点)**

| 维度 | 智能客服(Fin/Sierra) | Harvey(法律垂直) |
|------|----------------------|------------------|
| "结果"是什么 | 一次会话解决、流程化转接、线索合格等可计费事件 | 工作流完成度、律师可采纳的分析/草稿、Agent 跑完的尽调/审查包 |
| 谁对结果负责 | 厂商用规则定义"算不算解决",客户按次付费 | 律师/合伙人最终对客户交付负责,AI 是辅助执行层 |
| 计费主轴 | 按 outcome 计价(如 Fin 的 $0.99/结果) | **企业合同 + 席位/模块/用量协商**,官方不公布价目表 |
| 验证难度 | 相对短周期、可会话级统计 | 周期长、专业判断介入多,难以把"一次正确法律结论"直接做成公开计价单位 |

换句话说:客服可以把"结果"定义成双方都认的、短闭环商业事件;法律很难把"这份备忘录是否正确到可出庭"定义成自动计费触发器——**结果定义不同,商业模式就会分叉**。

**可核实的规模与收入口径**

- 2026-09-09 最新融资博文称 80% Am Law 100 律所使用 Harvey、五家 Fortune 10 企业使用；该口径为公司自述，未给出公开审计方法。
- 2026-03 融资博文称客户运行超过 2.5 万个自定义 Agent；这是历史公司披露，不把它解释成当前活跃或成功 Agent 数。
- 年经常性收入(ARR):CNBC 报道曾称 Harvey 在 **2026 年 1 月约 1.9 亿美元 ARR**；该数字来自媒体转述，未在当前可核验的 Harvey 2026-09 官方融资博文中重新披露，继续使用时必须标“媒体转述、非独立审计”，不得与 155 亿美元估值建立因果关系。

**席位价(第三方估计,标存疑)**

Harvey **不公开标准价目表**,销售为询价制企业合同。二级媒体与行业评测中的席位区间、起订数和年付说法口径互不一致；本次没有找到可作为官方价目的原始页面，**删除具体席位单价，不把第三方估计写成事实**。对 PM 有用的确定信息是：高客单价、席位/模块协商、长销售周期、常配实施与 legal engineering。

**效果指标体系(客户侧更可能看什么)**

| 指标类型 | 含义 | 说明 |
|----------|------|------|
| 工作流吞吐 | 尽调/审查/起草周期缩短 | 比"聊天满意度"更贴近律所 P&L |
| 律师采纳与返工率 | AI 草稿被采用的比例、合伙人改写量 | 间接反映质量与信任 |
| Agent 任务完成率 | 多步 Agent 是否跑完、卡在哪一闸门 | 接近客服解决率,但粒度是任务而非会话 |
| 合规事件 | 数据越权、幻觉引证、越权建议 | 一票否决项,发生即采购危机 |

## 五、PM 视角的启示

1. **"按结果付费"不是万能模板,先问结果能不能被双方审计且短闭环计价。** 客服可以;法律往往不能把"正确法律意见"自动计价。Harvey 选择席位/平台费 + 深度实施,是因为**结果定义不同**——PM 做垂直定价时,应先画清"结果是事件、是工件,还是最终职业判断"。
2. **高合规垂直的壁垒在工作流嵌入与责任分配,不在模型参数量。** 2.5 万+ 自定义 Agent、legal engineering、Vault/Shared Spaces,说明客户买的是"能在所内规范地跑起来",通用 Chat 很难替代。
3. **人在回路是产品功能,不是失败兜底。** 法律 AI 的成功体验是"加速到律师敢签字",而不是"全自动替代合伙人"。设计审批节点、引证展示、权限边界,本身就是核心需求。

## 相关阅读

- [智能客服:公开商业化闭环案例拆解](ai-customer-service.md) —— 对照"按结果付费"与本文不同的结果定义
- [AI 产品经理岗位类型盘点](../../00-roadmap/ai-pm-job-types.md) —— 垂直 AI PM 与 Harvey 示例
- [模型选型决策框架:效果、成本、延迟、合规四要素](../../02-pm-skills/cost-and-tech/model-selection-framework.md)
- [如何缓解幻觉?](../../04-interview/basics/hallucination-mitigation.md)
- [成熟市场里 AI 产品如何差异化?](../../04-interview/product-design/competitive-differentiation.md)
- [Agent 入门:规划、记忆、工具调用](../../01-ai-basics/llm/agent-basics.md)

## 参考资料

- [Harvey Raises $550M at a $15.5B Valuation](https://www.harvey.ai/blog/harvey-raises-dollar550m-at-a-dollar155b-valuation-to-help-legal-teams-own-their-intelligence) — Harvey 官方博客,2026-09-09；最新融资和公司自述采用口径,访问 2026-09-14
- [Introducing Harvey II](https://www.harvey.ai/blog/introducing-harvey-ii) — Harvey 官方博客,2026-08-18；context、Memory、Tenet 与 Space 权限声明,访问 2026-09-14
- [Agentic Vault Search and Organization in Harvey](https://www.harvey.ai/blog/agentic-vault-search-and-organization) — Harvey 官方博客,2026-09-08；文件搜索/整理与审计声明,访问 2026-09-14
- [Introducing Command Center](https://www.harvey.ai/blog/introducing-command-center) — Harvey 官方博客,2026-05-20；早期访问、匿名基准和治理面板声明,访问 2026-09-14
- [Harvey Raises at $11 Billion Valuation to Scale Agents Across Law Firms and Enterprises](https://www.harvey.ai/blog/harvey-raises-at-dollar11-billion-valuation-to-scale-agents-across-law-firms-and-enterprises) — Harvey 官方博客,2026-03-25,2026-07 访问
- [Harvey Raises Growth Round at $11 Billion Valuation Co-led by GIC and Sequoia](https://www.harvey.ai/blog/harvey-raises-growth-round-at-dollar11-billion-valuation-co-led-by-gic-and-sequoia) — Harvey 官方博客(同轮融资说明),2026-07 访问
- [Legal AI startup Harvey raises $200 million at $11 billion valuation](https://www.cnbc.com/2026/03/25/legal-ai-startup-harvey-raises-200-million-at-11-billion-valuation.html) — CNBC,含约 $190M ARR 等业务数据转述,2026-07 访问
- [Legal software firm Harvey valued at $11 billion in latest funding round](https://www.reuters.com/technology/legal-software-firm-harvey-valued-11-billion-latest-funding-round-2026-03-25/) — Reuters,2026-07 访问
- [Harvey 产品与解决方案概览](https://www.harvey.ai/) — 官方站点产品矩阵(Assistant / Vault / Agents 等),2026-07 访问
