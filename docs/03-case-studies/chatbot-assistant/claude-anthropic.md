---
title: claude-anthropic
type: note
permalink: aipmwiki/docs/03-case-studies/chatbot-assistant/claude-anthropic
---

# Claude 案例拆解:知识工作协作的深度优先路线

**分类**:对话助手
**体验时间**:2026 年 7 月

## 一、产品背景

Claude 由 Anthropic 推出,是海外头部通用助手里最常被拿来与 ChatGPT「对照着讲」的产品之一。若 ChatGPT 的产品叙事是「广度优先」(一个入口装下搜索、购物、浏览器、Agent、广告化变现),Claude 走的是另一条路:**深度优先**——把主力用户锁在重度知识工作者、开发者与企业团队,用长文本一致性、指令遵循、可审慎协作,以及「把活干完而不是只给答案」来占位。

**竞争格局**(与本仓库 [ChatGPT 案例](chatgpt.md) 的差异化边界一致):

| 维度 | ChatGPT | Claude | Gemini |
|------|---------|--------|--------|
| 主叙事 | 规模与功能面最广的通用工作台 | 知识工作深度协作 | Google 生态分发 + 超长上下文 |
| 典型用户心智 | 「什么都能问」 | 「把复杂材料交给它一起改到能交付」 | 「嵌在搜索/Docs/Android 里」 |
| 商业化倾向 | 订阅分层 + 低价档广告/购物 | 订阅额度分层 + API/企业合同,未见广告 | 订阅 + Workspace 捆绑 |

这篇拆解不复述 ChatGPT 已写过的「全民入口」故事,而聚焦 Claude 如何把「对话助手」做成**可交付物导向的知识工作协作面**。

**目标用户分层**(官方定价页口径,2026-07):

- **Free**:试用对话、联网搜索、基础文件与记忆
- **Pro / Max**:日常重度个人用户与 power user(含 Claude Code、Claude Cowork)
- **Team / Enterprise**:需要 SSO、集中计费、连接器管控、合规与支出上限的团队

中国大陆用户通常需经由官方支持地区或企业采购渠道访问;但对国内 AI PM 而言,其「深度协作 + Agent 交付物」产品逻辑仍是高频对标对象。

Anthropic 的公开叙事长期绑在「可治理的前沿模型」上:从 Constitutional AI,到持续发布的 System Card,再到企业侧对连接器、支出与桌面 Agent 权限的产品化。这对 Claude 的取舍有直接影响——更愿意把算力与风险卖给「付得起、也担得起责任」的专业用户,而不是用广告补贴换最大 DAU。理解这一点,比背一串媒体传闻中的 ARR 数字更有助于拆产品。

## 二、核心功能拆解

Claude 的表面交互仍是对话框,但产品重心已从「多轮问答」前移到「会话内产出可编辑交付物 + 跨工具代办任务」。下表按 2026 年 7 月产品现状梳理——刻意少写功能清单式堆叠,多写**知识工作定位**下各模块解决什么摩擦。

| 模块 | 在知识工作中的角色 | AI 起作用的环节 |
|------|-------------------|-----------------|
| 基础对话(Sonnet 5 / Opus 4.8) | Free/Pro 默认走 Sonnet 5;复杂判断、长链路协作可切 Opus 4.8 | 理解意图、长文改写、结构化分析;Effort 控件调节「想多久」 |
| Artifacts | 把代码、文档、可视化从聊天气泡里拆成**可迭代画布**,边聊边改交付物 | 生成 → 用户批注 → 就地重写,减少「复制到别处再改」 |
| Projects | 为课题/客户/产品线隔离上下文与上传材料,类似「给 AI 建项目文件夹」 | 项目级记忆与材料复用,降低跨会话重复贴文档成本 |
| Research | 围绕课题做多步检索与综合,产出带来源线索的研究报告 | 规划检索、综合多源、结构化成稿 |
| Memory | 跨会话记住偏好与事实,可管理/关闭 | 从历史提炼可复用信息并注入新对话 |
| **Claude Cowork** | 知识工作 Agent:描述「做完长什么样」,跨本地文件与已连接工具(如 Slack、Google Drive、浏览器)把活干完 | 任务规划、子 Agent 并行、读写文件、产出 deck/表格/文档并留待人审 |
| Claude Code | 终端侧编程 Agent;与订阅打通,面向仓库级改动与长跑任务 | 读写代码、跑命令/测试、自我校验;Enterprise/Team/Max 可试用 Dynamic Workflows(大规模并行子 Agent) |
| Skills / Connectors / Plugins | 把团队规范、MCP 连接器、子 Agent 打成可安装包,让 Claude「按角色上岗」 | 按需加载领域流程,而不是每次手写超长系统提示 |
| Claude for Chrome / Microsoft 365 | 把协作面嵌进浏览器与办公套件 | 页面理解、办公文档读写、企业搜索 |

### 主流程:从「问答」到「交卷」

以典型知识工作为例——「根据本季投放导出与 CRM 纪要,周一交 4 页指标 deck + 行动建议」:

1. 用户在 Cowork(或桌面端)指定文件夹/连接器,说明完成标准与节奏(可设为定时任务)。
2. Claude 拆分子任务:读导出表 → 核对口径 → 检索相关线程/纪要 → 起草幻灯与备忘。
3. 需要人拍板时打断询问(官方强调:关键决策仍回到用户;删除等敏感操作需批准)。
4. 用户审阅后定稿;会话可在桌面启动、手机跟进——2026 年 7 月起 Cowork 向 Web/移动端滚动放出(官方称先从 Max 用户开始 beta,再扩及其他付费档)。

官方产品指南把工具矩阵写得很清楚:**Chat 适合对话式起草,Claude Code 适合写代码,Cowork 适合跨应用的知识工作代办**——三条路径共享模型能力,但「完成定义」不同。Anthropic 在 Cowork Web/移动端发布文中还给出公司口径:Cowork 使用中超过 90% 并非软件开发,最大品类是业务运营与内容创作(合计约占一半)。这组数字来自厂商自报,适合理解定位,不宜外推为全市场渗透率。

对 PM 更有启发的是:当 Agent 真能代办时,产品边界会自然从「程序员工具」扩到「运营周报、合同台账、客户会前 brief」——这些活很少写进 JD,却占掉很多人一周里很大一块时间。Claude 用 Cowork 抢的是这块「工作周围的工作」,而不是再做一个更会闲聊的对话框。

### 模型层:Sonnet 5 与 Opus 4.8 的分工

- **Claude Sonnet 5**(2026-06-30 发布):官方定位为「迄今最偏 Agent 的 Sonnet」,在编码、工具使用、知识工作上拉近与 Opus 4.8 的差距,并作为 Free/Pro 默认模型;API 有至 2026-08-31 的入门价,之后回到标准价。官方还说明新 tokenizer 下同文可能映射为约 1.0–1.35× 更多 token,入门价意在让迁移大致成本中性。
- **Claude Opus 4.8**(2026-05-28 发布):旗舰协作模型,强调长跑 Agent、更审慎的判断与更低的「未核实就宣称进度」倾向(官方称相较前代更不易让有缺陷的代码「默默通过」);API 标准价与上一代 Opus 持平,另提供更快但更贵的 Fast Mode。
- **Effort 控件**(随 Opus 4.8 起在 claude.ai / Cowork 等面开放):用户可显式选择思考投入——高 effort 更深、慢、更吃额度;低 effort 更快、更省额度。这是把「算力预算」产品化成用户可理解的旋钮,而不是藏在黑盒路由里。Opus 4.8 默认偏 high;困难长跑任务官方建议用更高档(产品内称 extra / `xhigh` 等)。

选型直觉可以记成:日常起草、高频自动化与成本敏感的生产 Agent 先看 Sonnet 5;高风险专业判断、超长链路与「错一次很贵」的任务再上 Opus 4.8——最终仍应用真实样本做质量/总成本对照,而不是只比单次 token 单价。

## 三、技术方案推测

> 以下基于公开产品表现与官方博客/文档进行**推测**,Anthropic 未完整公开内部编排细节,请勿当作确定性架构结论。

- **模型路由与 Effort(推测)**:产品侧已公开「用户可选 effort」;同时仍可能存在按任务类型在 Haiku / Sonnet / Opus 间的默认路由。Effort 更像是在同一模型上调节推理 token 预算,与「换模型」正交。
- **Cowork 与 Claude Code 同源 Agent 骨架(官方表述 + 推测)**:文档写明 Cowork「使用与 Claude Code 相同的 agentic 架构」,只是把交互面从终端换成桌面/Web 知识工作场景。合理推测共用「规划 → 工具调用(文件/浏览器/MCP)→ 子 Agent 并行 → 结果自检」循环;Dynamic Workflows 则是把并行子 Agent 规模推到「数百」量级的企业/重度档能力。
- **Artifacts(推测)**:交付物与对话线程分离存储,对 Artifact 的编辑作为结构化反馈回灌下一轮生成,本质是「带状态的人机共编」,而非纯文本续写。
- **Projects / Memory / Research(推测)**:Projects 近似项目级 RAG(材料检索注入);Memory 为用户级事实/偏好抽取与按需注入;Research 为多步联网检索 + 综合生成。原理与仓库内《什么是 RAG》《Agent 入门》一致,检索源换成用户材料与实时网页。
- **Skills / MCP(推测 + 开放标准)**:Skills 按任务渐进加载指令与脚本;Connectors 走 MCP 把外部系统能力接到 Agent 工具表——与「把一切塞进系统提示」相比,更利于企业治理与复用。详见仓库内 [Agent Skills](../../01-ai-basics/llm/agent-skills.md)、[MCP](../../01-ai-basics/llm/mcp.md)。
- **安全与对齐(公开叙事)**:Anthropic 长期强调 Constitutional AI / 对齐评估与 System Card;产品上则体现为权限确认、企业连接器管控、对高风险网络能力的默认防护等。这对「敢不敢把本地文件夹和邮箱交给 Agent」是信任前提,而不只是品牌口号。

## 四、商业模式

Claude 的变现主轴是**订阅额度分层 + API/云市场按量**,公开材料里几乎不见消费级广告叙事——这与 ChatGPT 在 Free/Go 探索广告、购物货币化形成对照。以下价格均来自官方定价页与帮助中心,核实截至 **2026-07**;含税与地区差异以官网为准。

### 个人订阅

| 档位 | 价格(官网) | 与知识工作相关的核心权益 |
|------|------------|--------------------------|
| Free | $0 | Web/App/桌面聊天、联网搜索、记忆、基础文件与代码执行、远程 MCP 连接器等;额度有限 |
| Pro | $20/月,或年付折合约 $17/月($200 年付) | 更高额度;含 **Claude Code、Claude Cowork**、Research、更多模型、无限 Projects 等 |
| Max 5x | $100/月 | Pro 全部 + 约 **5×** Pro 的每会话用量、更高输出上限、高峰优先、新功能优先 |
| Max 20x | $200/月 | 约 **20×** Pro 用量;面向全日重度协作(含高强度 Cowork / Claude Code) |

帮助中心补充:Max 目前仅月付;除 5 小时窗口类用量外,还有「全模型」与「仅 Sonnet」两套周限额。Cowork 比普通 Chat 更快消耗额度——官方在产品页提示重度用户考虑升 Max。

### 团队与企业

| 档位 | 价格(官网) | 要点 |
|------|------------|------|
| Team Standard | $20/席/月(年付) / $25(月付);约 5–150 人 | 高于 Pro 的用量、集中管理与 SSO、连接器管控等;可混合席位类型 |
| Team Premium | $100/席/月(年付) / $125(月付) | 约 5× Standard 用量;含 Claude Code 与 Cowork 等重度能力 |
| Enterprise | 席位约 $20/席 + **按 API 费率计用量**(可自助或销售协助) | RBAC、SCIM、审计日志、Compliance API、支出上限、HIPAA-ready 选项等;Cowork 企业管控已上线,但官方注明 Cowork 活动**尚未**进入审计日志/Compliance API |

教育机构另有 Education plan(官网单独入口),本文不展开。

### API 定价(每百万 Token,官方平台文档,2026-07)

| 模型 | 输入 | 输出 | 备注 |
|------|------|------|------|
| Claude Sonnet 5 | $2(至 2026-08-31 入门价)→ 之后 $3 | $10 → 之后 $15 | Free/Pro 默认对话模型;新 tokenizer 下同文可能映射为更多 token |
| Claude Opus 4.8 | $5 | $25 | 与 Opus 4.7 标准价持平 |
| Claude Opus 4.8 Fast Mode | $10 | $50 | 研究预览:更高输出速度,溢价计费 |
| Claude Haiku 4.5 | $1 | $5 | 低延迟/高通量场景 |

另有 Prompt Caching、Batch 等折扣机制;AWS/Google/Azure 等云上部署可能带区域溢价(如部分场景约 1.1×),以各云报价为准。

### 收入结构说明(刻意不写天文数字主轴)

二级媒体常报道 Anthropic 的 ARR、估值与融资轮次,口径不一、修订频繁。本案例**不以未经交叉核验的 ARR/估值作为事实主轴**;能确定的是商业设计本身:

1. **个人侧用 Max 把「额度不够」变成清晰升级阶梯**,而不是先做广告填免费用户成本。
2. **企业侧用「席位 + 用量」**,让重度 Agent(Cowork/Code)的成本与实际消耗对齐。
3. **API 侧用 Sonnet 5 拉近旗舰能力、用价格与 Effort 做成本—质量曲面**,服务产品内嵌 Agent 的开发者。

## 与 ChatGPT 的定位差异(写给 PM)

- ChatGPT 优化「入口心智与任务覆盖面」,并已验证低价档广告与购物等消费级货币化。
- Claude 优化「交付物质量、长任务可靠性、企业可治理的 Agent 权限」,货币化更贴近**专业订阅与 API**。
- 同一用户完全可能两者并用:广度探索用 ChatGPT,长文共创、仓库级改动、跨文件交卷用 Claude——并非零和。

## 五、PM 视角的启示

1. **「深度优先」可以是完整战略,而不只是模型评测排名。** Claude 没有把产品故事写成功能最长清单,而是反复强化同一完成定义:交卷(Artifacts/Cowork)、可隔离的工作上下文(Projects)、可调节的思考预算(Effort)、可安装的岗位知识(Skills/Plugins)。做垂直或专业助手时,先写清「用户认为什么叫做完」,再决定功能取舍。
2. **把 Agent 的权限与人审节点做成默认体验,而不是事后合规补丁。** Cowork 强调文件夹白名单、重要动作确认、企业连接器与支出管控——因为知识工作 Agent 一旦能改本地文件和发消息,信任失败是产品级事故。PM 设计「代办类」能力时,应同步设计拒绝、确认、审计与回滚,而不是只演示 happy path。
3. **用价格阶梯表达算力与风险,而不是只表达功能开关。** Pro→Max 5x→Max 20x 主要卖的是用量与优先权;API 上 Sonnet 5 vs Opus 4.8、标准 vs Fast、Effort 高低,卖的是同一能力曲面的不同采样点。这比「高级功能锁在某档」更贴合 Agent 时代的成本结构:贵的是长跑与高 effort,不是某个按钮。Enterprise「低席位费 + 用量按 API」也是同一逻辑——让组织为真实消耗买单,避免全员按最高档 lock-in。
4. **产品矩阵可以用「完成定义」切分,而不是用「模型名字」切分。** Chat / Cowork / Claude Code 背后模型高度重叠,但用户心智不同:要一段话、要一份可交的材料、要一次可合并的改动。国内团队做助手矩阵时,与其先争论「要不要再做一个 App」,不如先问三种完成定义是否都有清晰入口与计费方式。

## 相关阅读

- [ChatGPT 案例拆解](chatgpt.md) —— 对照「广度优先」产品矩阵与广告化路径
- [什么是 RAG:检索增强生成入门](../../01-ai-basics/llm/what-is-rag.md)
- [Agent 入门](../../01-ai-basics/llm/agent-basics.md)
- [Agent Skills](../../01-ai-basics/llm/agent-skills.md)
- [MCP](../../01-ai-basics/llm/mcp.md)
- [幻觉(Hallucination)](../../01-ai-basics/llm/hallucination.md)
- [如何分析一个优秀的 AI 产品](../../04-interview/case-analysis/analyze-a-good-ai-product.md)
- [AI 产品的商业模式与定价](../../04-interview/case-analysis/ai-business-model-and-pricing.md)

## 参考资料

- [Plans & Pricing | Claude](https://claude.com/pricing) — Anthropic 官方订阅与团队定价,2026-07 访问
- [What is the Max plan? | Claude Help Center](https://support.claude.com/en/articles/11049741-what-is-the-max-plan) — Max 5x/20x 说明,2026-07 访问
- [Pricing - Claude Platform Docs](https://platform.claude.com/docs/en/about-claude/pricing) — 官方 API 定价,2026-07 访问
- [Introducing Claude Sonnet 5](https://www.anthropic.com/news/claude-sonnet-5) — 官方发布,2026-07 访问
- [Introducing Claude Opus 4.8](https://www.anthropic.com/news/claude-opus-4-8) — 官方发布,2026-07 访问
- [Claude Cowork](https://www.anthropic.com/product/claude-cowork) — 官方产品页,2026-07 访问
- [The Claude Cowork product guide](https://claude.com/blog/the-claude-cowork-product-guide) — 官方产品指南,2026-07 访问
- [Claude Cowork on web and mobile](https://claude.com/blog/cowork-web-mobile) — Web/移动端滚动发布与用量口径,2026-07 访问
- [Cowork overview - Claude Docs](https://claude.com/docs/cowork/overview) — Cowork 与 Claude Code 同构 Agent 架构说明,2026-07 访问