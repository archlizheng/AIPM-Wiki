---
title: cursor-vs-copilot
type: note
permalink: aipmwiki/docs/03-case-studies/aigc/cursor-vs-copilot
---

# Cursor 与 GitHub Copilot:AI 代码助手的付费转化

**分类**:AIGC
**资料核验时间**:2026 年 9 月 14 日

## 一、产品背景

AI 代码助手是生成式 AI 里付费转化相对「看得见」的品类之一:用户几乎每天写代码,助手直接嵌在编辑器或 Git 工作流里,价值感知强、替换成本也高。这篇拆解对比两条清晰可核实的产品路径——**Cursor**(AI 原生 IDE)与 **GitHub Copilot**(深度嵌入 GitHub / 多 IDE 的编程助手)——焦点放在「免费如何养习惯、付费档如何按用量与协作深度拆开」,而不是争论「谁写代码更强」。

| 维度 | Cursor | GitHub Copilot |
|------|--------|----------------|
| 产品形态 | 独立编辑器(VS Code 分支),AI 是一等公民 | 插件 + GitHub.com / Mobile / CLI 等多入口 |
| 典型用户 | 个人开发者、重度 Agent 用户、工程团队 | 已在 GitHub 协作的个人与组织 |
| 免费层 | Hobby:有限 Agent / Tab | Free:每月约 2,000 次补全等有限能力 |
| 个人付费主阶梯 | Pro → Pro+ → Ultra(用量递进) | Pro → Pro+ → Max(额度/Credits 递进) |
| 组织付费 | Teams / Enterprise | Business / Enterprise |

竞争格局里还有 Claude Code、OpenAI Codex、Google Antigravity、各类 IDE 插件等(详见仓库内 Vibe Coding 工具盘点)。Cursor 与 Copilot 适合做对标,是因为两者都已经把「个人订阅 + 团队席位 + 用量超转」跑成公开价目表,便于从官网直接核对转化漏斗设计。

关于营收与大客户传闻:二级媒体不时报道 Cursor 的 ARR、融资或特定标杆客户。**此类数字与名单未经官方 IR 口径持续证实的,本文不作为确定事实引用**;若需提及市场热度,仅保留「据媒体报道增长很快」一类弱表述,或干脆不写——付费转化机制本身已足够支撑案例结论。

## 二、核心功能拆解

两者都覆盖「补全 → 对话改代码 → Agent 多步任务」的能力谱,但默认交互重心不同。

### Cursor:以编辑器为容器的 Agent 工作台

| 模块 | 作用 | AI 介入 |
|------|------|---------|
| Tab 补全 | 行级/多行续写,低摩擦 | 高,几乎每次击键都可能触发 |
| Chat / Inline Edit | 选中代码提问或改写 | 高 |
| Agent | 跨文件规划、改代码、跑命令(视权限) | 高,多步工具调用 |
| Cloud Agents / 自动化 | 云端异步任务、团队共享上下文(Teams+) | 高 |
| MCP / Skills / Hooks | 接外部工具与可复用技能 | 中高,依赖配置 |
| Bugbot 等评审能力 | 对变更做检查(个人档可按用量,团队档打包更完整) | 中高 |

主流程(付费用户典型日):打开仓库 → Tab 完成样板代码 → 用 Agent 改一个跨文件需求 → 本地跑测 → 不够用的前沿模型用量走「包含额度 + 按需加购」。**转化相关的产品点**是:Hobby 能让人感到「真的快了一点」,但 Agent 与前沿模型很快触顶,日常全职开发几乎必然撞到 Pro 门槛。

### GitHub Copilot:以 GitHub 工作流为容器的助手

| 模块 | 作用 | AI 介入 |
|------|------|---------|
| 代码补全 / Next Edit | 多 IDE 内联建议 | 高 |
| Chat / Agent mode | 编辑器内对话与代理改码 | 高 |
| Cloud agent | 可委托任务并开 PR 等 | 高 |
| Code review | PR / diff 评审 | 高 |
| Copilot CLI | 终端侧助手 | 中高 |
| 第三方 Agents(如 Claude Code、Codex,预览能力随档位变化) | 在 Copilot 体系内调用其他代理 | 中高 |
| 组织策略 / 审计 / 代码索引(Enterprise 更强) | 管理与定制 | 偏产品与平台能力 |

主流程(组织用户典型日):在 VS Code 里补全与 Agent 改代码 → 推送到 GitHub → Copilot 参与 PR 总结或评审 → 管理员在组织层看用量与策略。**转化相关的产品点**是:Free 用补全建立习惯;Pro($10)用「无限补全 + 一包 AI Credits」完成个人转化;要上组织管控、IP 与策略,则走 Business/Enterprise 席位。

## 三、技术方案推测

> 以下为基于公开产品行为与文档的推测,两家均未完整公开模型路由与上下文工程细节。

- **推测(两者共性)**:补全侧多为低延迟小上下文补全模型或专用头;Chat/Agent 侧路由到更大的通用/代码模型,并注入当前文件、选区、仓库检索片段、终端输出等工具结果。
- **推测(Cursor)**:作为独立 IDE,更容易做「全仓库索引 + 多文件编辑事务」的统一体验;Agent 与 Cloud Agents 更像把 IDE 操作、终端、浏览器等工具封装成可编排动作。官方强调 Privacy Mode 下代码不用于训练(团队可强制开启)——这是 To-B 转化里的信任组件,而非模型本身。
- **推测(Copilot)**:优势在 GitHub 图数据(Issue、PR、仓库权限模型)与 IDE 插件分发;Enterprise 的「组织代码库索引 / 定制」说明其路线是「助手 + 平台知识」,而不只是编辑器插件。2026 年起个人与组织计费都更明确地走向 **GitHub AI Credits** 用量池,Chat、Agent、Cloud agent、CLI、评审等会消耗 Credits——把「功能开关」和「token 成本」绑在同一计量单位上。
- **推测(转化与技术的关系)**:当产品从「补全」升级到「Agent 长时间跑任务」,边际成本从「每次建议几分钱」变成「一次任务可能烧掉可观 Credits」。因此两家都在个人高档位(Cursor Ultra、Copilot Max)和企业池化用量上做文章——这是成本结构倒逼套餐结构,而不是单纯营销升级。

## 四、商业模式

### Cursor 定价(核实截至 2026 年 9 月 14 日,来源:cursor.com/pricing)

官网个人档展示为 **Individual**,其下再分 Pro / Pro+ / Ultra;页面默认可见年付折合价(例如 Individual 展示约 **$16/月** 起,对应年付)。结合官网说明与公开价目习惯,个人侧可理解为:

| 档位 | 官网当前价位 | 转化角色 |
|------|-------------------|----------|
| Hobby | 免费 | 试用:有限 Agent、有限 Tab,培养「AI 写代码」习惯 |
| Pro | 约 $20/月;年付折合约 $16/月 | 个人主力转化档:扩大 Agent 限额、前沿模型、MCP/Skills/Hooks、Cloud agents 等 |
| Pro+ | 约 $60/月档(官网推荐给日常 Agent 用户) | 用量升级:同一功能集,更大模型用量池 |
| Ultra | 约 $200/月档(官网推荐给 Agent 重度用户) | 顶格个人用量 + 优先体验新能力 |
| Teams | 约 $40/用户/月;年付折合约 $32/用户/月 | 团队计费、共享规则/插件市场、团队隐私模式、SSO 等 |
| Enterprise | 定制 | 池化用量、SCIM、审计、访问控制、优先支持 |

截至核验日，官网仍显示 Hobby 免费、Pro $20/月、Pro+ $60/月、Ultra $200/月，并列出 MCP、Skills、Hooks 与 Cloud agents；页面建议日常 Agent 用户用 Pro+、重度用户用 Ultra。官网同时说明每个方案含一定模型用量，超出后可按需使用并在周期末计费。Cursor Changelog 另显示 Projects 于 2026-09-02 以 beta 开始向全体用户滚动开放，self-hosted machines 同日发布，支持让工具执行留在企业网络。

### GitHub Copilot 定价(核实截至 2026 年 9 月 14 日,来源:官网 Plans 与 GitHub Docs)

**个人:**

| 档位 | 价格 | 转化角色 |
|------|------|----------|
| Free | $0 | 每月约 2,000 次补全;有限模型与 Chat/Agent;零成本获客 |
| Pro | $10/用户/月 | 个人付费入口;官网当前表列 1,000 base、1,500 total AI Credits |
| Pro+ | $39/用户/月 | 高级模型与更高用量;官网当前表列 3,900 base、7,000 total AI Credits |
| Max | $100/用户/月 | 持续高用量;官网当前表列 10,000 base、20,000 total AI Credits |

**组织:**

| 档位 | 价格 | 转化角色 |
|------|------|----------|
| Business | $19/用户/月 | 组织策略与集中管理;每席 1,900 AI Credits 并在计费实体层池化 |
| Enterprise | $39/用户/月 | 每席 3,900 AI Credits、优先新模型与 GitHub.com 深度整合 |

超额用量方面，GitHub 文档说明额外消耗按 AI Credits 计量，1 credit = $0.01 USD；组织管理员可设置预算与是否允许超转。2026-09-09 的官方 Changelog 还确认 managed permissions 已在 Copilot app、CLI 和使用 Agent Host 的 VS Code 会话 GA，可管理 Shell、文件读写与网络域名策略。2026-09-10 周更把 Voice mode 标为 Experimental、Recurring agent tasks 标为 Public Preview，不能当成稳定默认能力。

### 两条转化漏斗对比

| 设计选择 | Cursor | Copilot |
|----------|--------|---------|
| 免费层厚度 | 功能可见但紧 | 补全次数明确封顶(2,000),习惯易养成、天花板也清晰 |
| 个人首付价格锚点 | Pro 约 $20(年付展示更低) | Pro **$10**,锚点更低、更易完成「第一次付款」 |
| 个人上探方式 | 同功能、加用量(Pro+ / Ultra) | 同逻辑(Pro+ / Max)+ 更贵模型门槛 |
| 团队溢价理由 | 管理、共享资产、隐私与 SSO | 策略、池化 Credits、与 GitHub 权限/PR 一体 |
| 成本传递方式 | 包含用量 + on-demand | AI Credits 包 + 超额 |

Copilot 以 **$10** 作为 Pro 个人入口，并用 AI Credits 显式计量额外用量；公开资料不足以证明付费转化规模，也不能把 Credits 描述成“隐藏涨价”。Cursor 以 Pro、Pro+ 和 Ultra 分层承接不同用量，具体转化效果仍需产品数据验证。两者的公开套餐都没有承诺无限免费 Agent，长期成本策略仍应按官方套餐与实际用量核算。

## 五、PM 视角的启示

1. **代码助手的付费转化,核心不是多一个按钮,而是「免费层证明价值、付费层按用量与协作深度加价」。** Free/Hobby 证明「我会变快」;个人 Pro 证明「我能天天用」;Team/Business 证明「公司能管控、能报销、能共享」。三层目标不同,套餐文案也应分开写清。
2. **Agent 化之后,套餐必须从「功能开关」转向「用量池」。** Cursor 的 on-demand 与 Copilot 的 AI Credits,本质都是承认:重度代理任务的成本方差极大,固定功能订阅扛不住。PM 做 AI 功能定价时,先估算 P50/P95 用户的 token 或 GPU 消耗差,再决定「包含多少 + 如何超转」。
3. **分发容器决定转化叙事。** Copilot 可以靠 GitHub 已有开发者关系做 $10 低锚点渗透;Cursor 必须说服用户换编辑器,因而更强调 Agent、多模型与编辑器内一站式体验,价格锚点也更高。选型或做竞品分析时,先问「用户是否已在我的容器里」,再谈功能对比。

## 相关阅读

- [Vibe Coding 工具盘点与选型](../../02-pm-skills/vibe-coding/tool-landscape.md)
- [Vibe Coding 是什么](../../02-pm-skills/vibe-coding/what-is-vibe-coding.md)
- [Agent 基础:从 Chatbot 到智能体](../../01-ai-basics/llm/agent-basics.md)
- [Agent Skills:可复用的 Agent 能力包](../../01-ai-basics/llm/agent-skills.md)
- [MCP:模型上下文协议](../../01-ai-basics/llm/mcp.md)
- [DeepSeek 开始峰谷定价,你怎么看 AI 产品的商业模式?](../../04-interview/case-analysis/ai-business-model-and-pricing.md)

## 参考资料

- [Cursor · Pricing](https://cursor.com/pricing) — Cursor 官方定价页，Hobby/Pro/Pro+/Ultra 当前价格与 Cloud agents，2026-09-14 访问
- [Cursor Changelog](https://cursor.com/changelog) — Cursor 官方 Changelog，Projects beta 与 self-hosted machines，2026-09-14 访问
- [GitHub Copilot · Plans & pricing](https://github.com/features/copilot/plans) — GitHub 官方定价页，个人计划与 Agent/Cloud agent 能力，2026-09-14 访问
- [Plans for GitHub Copilot](https://docs.github.com/en/copilot/get-started/plans) — GitHub Docs，Free/Pro/Pro+/Max/Business/Enterprise 价格与 AI Credits，2026-09-14 访问
- [About billing for GitHub Copilot in organizations and enterprises](https://docs.github.com/en/copilot/concepts/billing/organizations-and-enterprises/fundamentals) — GitHub Docs，Business $19、Enterprise $39 与 Credits 池，2026-09-14 访问
- [Enterprise managed permissions for GitHub Copilot agent operations](https://github.blog/changelog/2026-09-09-enterprise-managed-permissions-for-github-copilot-agent-operations/) — GitHub 官方 Changelog，权限治理 GA，2026-09-09 发布，2026-09-14 访问
- [GitHub Copilot weekly releases — September 7](https://github.blog/changelog/2026-09-10-github-copilot-weekly-releases-september-7/) — GitHub 官方 Changelog，Voice Experimental、Recurring agent tasks Public Preview，2026-09-10 发布，2026-09-14 访问
