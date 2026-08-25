---
title: cursor-vs-copilot
type: note
permalink: aipmwiki/docs/03-case-studies/aigc/cursor-vs-copilot
---

# Cursor 与 GitHub Copilot:AI 代码助手的付费转化

**分类**:AIGC
**体验时间**:2026 年 7 月

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

### Cursor 定价(核实截至 2026 年 7 月,来源:cursor.com/pricing)

官网个人档展示为 **Individual**,其下再分 Pro / Pro+ / Ultra;页面默认可见年付折合价(例如 Individual 展示约 **$16/月** 起,对应年付)。结合官网说明与公开价目习惯,个人侧可理解为:

| 档位 | 大致价位(2026-07) | 转化角色 |
|------|-------------------|----------|
| Hobby | 免费 | 试用:有限 Agent、有限 Tab,培养「AI 写代码」习惯 |
| Pro | 约 $20/月;年付折合约 $16/月 | 个人主力转化档:扩大 Agent 限额、前沿模型、MCP/Skills/Hooks、Cloud agents 等 |
| Pro+ | 约 $60/月档(官网推荐给日常 Agent 用户) | 用量升级:同一功能集,更大模型用量池 |
| Ultra | 约 $200/月档(官网推荐给 Agent 重度用户) | 顶格个人用量 + 优先体验新能力 |
| Teams | 约 $40/用户/月;年付折合约 $32/用户/月 | 团队计费、共享规则/插件市场、团队隐私模式、SSO 等 |
| Enterprise | 定制 | 池化用量、SCIM、审计、访问控制、优先支持 |

官网 FAQ 写明:每个方案含一定模型用量,**用尽后可按需(on-demand)继续用,周期末计费**。因此 Cursor 的个人转化不只是「买功能」,更是「买包含用量 + 可选超转」——Pro 解决「能不能当主力编辑器」;Pro+/Ultra 解决「Agent 是否天天触顶」。

### GitHub Copilot 定价(核实截至 2026 年 7 月,来源:官网 Plans 与 GitHub Docs)

**个人:**

| 档位 | 价格 | 转化角色 |
|------|------|----------|
| Free | $0 | 每月约 2,000 次补全;有限模型与 Chat/Agent;零成本获客 |
| Pro | $10/用户/月 | 无限补全;Cloud agent、代码评审等;约 **$15/月** 合计 AI Credits(含 base + flex 等构成,以官网为准) |
| Pro+ | $39/用户/月 | 更高用量(官方称约 4x+ Pro)与高级模型;约 **$70/月** Credits |
| Max | $100/用户/月 | 更高用量(官方称约 2.9x+ Pro+)与优先新模型;约 **$200/月** Credits |

**组织:**

| 档位 | 价格 | 转化角色 |
|------|------|----------|
| Business | $19/用户/月 | 组织策略与集中管理;每席约 1,900 AI Credits 并在计费实体层池化 |
| Enterprise | $39/用户/月 | 更大 Credits 池(约 3,900/席)、GitHub.com 深度整合与企业向定制能力 |

超额用量方面,文档说明额外消耗按 AI Credits 计量(公开材料中有 **1 credit = $0.01 USD** 的换算口径,具体以当时账单文档为准);组织管理员可设预算与是否允许超转。

### 两条转化漏斗对比

| 设计选择 | Cursor | Copilot |
|----------|--------|---------|
| 免费层厚度 | 功能可见但紧 | 补全次数明确封顶(2,000),习惯易养成、天花板也清晰 |
| 个人首付价格锚点 | Pro 约 $20(年付展示更低) | Pro **$10**,锚点更低、更易完成「第一次付款」 |
| 个人上探方式 | 同功能、加用量(Pro+ / Ultra) | 同逻辑(Pro+ / Max)+ 更贵模型门槛 |
| 团队溢价理由 | 管理、共享资产、隐私与 SSO | 策略、池化 Credits、与 GitHub 权限/PR 一体 |
| 成本传递方式 | 包含用量 + on-demand | AI Credits 包 + 超额 |

Copilot 用 **$10** 完成大量个人付费转化,再用 Credits 包把 Agent 时代的成本涨价「藏进额度」;Cursor 用更高一点的 Pro 锚点换「整个 IDE 换栈」的承诺,再靠 Pro+/Ultra 吸收重度 Agent 用户。两者都没有把「无限免费 Agent」当作长期策略——那会与真实推理成本冲突。

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

- [Cursor · Pricing](https://cursor.com/pricing) — Cursor 官方定价页,2026-07 访问
- [GitHub Copilot · Plans & pricing](https://github.com/features/copilot/plans) — GitHub 官方定价页(个人 Free/Pro/Pro+/Max 与 Credits 展示),2026-07 访问
- [Plans for GitHub Copilot](https://docs.github.com/en/copilot/about-github-copilot/subscription-plans-for-github-copilot) — GitHub Docs(个人与 Business/Enterprise 价格表),2026-07 访问
- [About billing for GitHub Copilot in organizations and enterprises](https://docs.github.com/en/copilot/concepts/billing/organizations-and-enterprises) — GitHub Docs(Business $19、Enterprise $39 与 Credits 池),2026-07 访问
- [GitHub Copilot licenses](https://docs.github.com/en/billing/concepts/product-billing/github-copilot-licenses) — GitHub Docs(席位与个人档价目),2026-07 访问