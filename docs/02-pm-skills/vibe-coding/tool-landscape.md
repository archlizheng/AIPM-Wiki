---
title: tool-landscape
type: note
permalink: aipmwiki/docs/02-pm-skills/vibe-coding/tool-landscape
---

# Vibe Coding 工具盘点与选型(2026 年 9 月)

> **信息状态**：动态快照｜**最后核验**：2026-09-14

## 先立一个规矩:这是截至 2026-09-14 的一张快照

Vibe Coding 工具迭代很快，价格、额度和权限都可能随月变化。本文已将可核验的工具价格更新到 2026 年 9 月 14 日；无法稳定复现的数字改为官方动态入口和计费维度。**分类和选型逻辑可以复用，采购前必须打开对应官方入口复核当日价格、地区和套餐权益**。本文快照不能作为报价或预算承诺。

如果你还不清楚"Vibe Coding 是什么、AI PM 该不该自己写代码",建议先读 [Vibe Coding 是什么](what-is-vibe-coding.md)。这篇文章只聚焦"选哪个工具"。

## 四类工具,对应四种不同的"做 Demo"需求

AI PM 说"我想做个 Demo"时,其实可能是几件不同的事。**2026 年的默认选型顺序是:优先编程 Agent、全栈生成平台、设计落地工具;低代码 / 纯工作流平台只作为特定场景的备选**,原因见第四节开头。

1. 我已经有一定代码基础,或者需要深度参与已有代码库的调试和协作——**AI 编程 Agent**(第一节;形态含终端、独立桌面端、IDE,并不一定绑编辑器)。
2. 我想让别人**看到并点一点**一个交互界面,判断这个流程顺不顺——**全栈网页应用生成平台**(第二节;产出物本质是代码)。
3. 我更在意**视觉与设计系统**,想先在画布上定稿再落到代码——**设计落地 / Vibe Design 工具**(第三节;常挂在编程 Agent 上跑,产出可进 Git)。
4. 我只想快速串一条自动化流程、且明确不打算把成果迁出平台——这时候才轮到**低代码 / 工作流搭建平台**(第四节,非主推荐)。

下面按这四类分别盘点。

## 一、AI 编程 Agent(终端 / 独立桌面端 / IDE)

这一类的共同点是:它们操作的是**真实代码库**,上限高——理论上能做的事和专业工程师用的工具没有本质区别。需要纠正一个过时印象:**编程 Agent 并不等于"装在 IDE 里的插件"**。

到 2026 年中,主流产品的形态已经明显分化、且经常**多形态并存**:

| 形态 | 典型代表 | 你怎么用 |
|------|----------|----------|
| **独立桌面端** | Codex App、Google Antigravity 2.0、Claude Code 桌面端 | 打开专用 App,以任务/对话为中心编排 Agent,不必先开 VS Code |
| **终端 / CLI** | Claude Code、Codex CLI、Antigravity CLI(`agy`) | 在项目目录里用命令行把活交给 Agent |
| **IDE / 编辑器内** | Cursor、GitHub Copilot、各家 IDE 插件 | 边看代码边改,或在熟悉的编辑器里唤起 Agent |
| **网页 / 云端异步** | Codex Web、部分云端任务 | 丢任务后离开,回来看结果;也可手机端远程跟进 |

选型时先问"我想要哪种交互方式",再问"哪家模型/生态"——不要默认以为必须先学会某个 IDE。

| 工具 | 定位 | 适合场景 | 门槛 / 官方状态与价格（访问 2026-09-14） |
|------|------|----------|----------------------------|
| **Claude Code**(Anthropic) | 以终端起步的编程 Agent,强调审批优先——每一步改动、每一条命令都可设为需确认;可读写整个代码库、直接执行命令;现已有网页版与桌面端 | 需要深度定制、想对每一步改动保持可控感;习惯终端或想用独立桌面端而不绑特定 IDE | 随 Claude 订阅:Pro 约 $17-20/月,Max 版 $100 起(更高用量),Team Premium 席位约 $100/月起;有免费额度可先体验 |
| **Codex**(OpenAI) | OpenAI 的自主编程 Agent:读写文件、执行命令、跑测试、修 Bug,以"完整任务"为粒度交付;覆盖**独立桌面端**、CLI、IDE 插件、网页版,并支持云端异步执行 | 已在用 ChatGPT、想让 Agent 接手整段编程任务(而不只是行级补全);适合本地改代码或丢到云端跑完再看结果 | 随 ChatGPT 订阅附带,无单独买 Codex:Free 有限;Plus 约 $20/月;Pro 约 $100/月(5x)或 $200/月(20x);超额可加购 credits,也可用 API Key 按量计费 |
| **Antigravity**(Google) | Google 的 agent-first 开发平台:2.0 起主推**独立桌面端**(多 Agent 并行编排,本身不是编辑器),另有 CLI(`agy`)与可选的 Antigravity IDE | 想并行丢多个子任务给 Agent、或已在 Google / Gemini 生态里;可与自选 IDE"双开",不必绑死一家编辑器 | 个人版 **GA、$0/月**、基础 weekly rate limits;Google AI Pro / Ultra 提供更高配额与 AI credits pool;Organization plan 通过 Google Cloud 提供,页面标注向部分客户逐步开放,并可按 Gemini Enterprise Agent Platform 消耗计费;以 [官方 plans](https://antigravity.google/docs/plans) 和 [pricing](https://antigravity.google/pricing?app=antigravity-ide-) 动态页面为准 |
| **Cursor** | AI 原生代码编辑器(基于 VS Code 分支),图形化界面,擅长在大代码库里做智能补全和 Agent 模式改代码 | 想要"看得见代码、边看边改"的图形化体验——这是仍以 IDE 为中心的一路 | Hobby 免费版功能有限;Pro 约 $20/月起,Pro+ 约 $60/月,Ultra 约 $200/月;Teams 约 $40/人/月 |
| **GitHub Copilot** | 深度嵌入 GitHub 和 VS Code 生态的编程助手,插件形态,和代码托管、Pull Request 流程一体化 | 团队本身已经用 GitHub 协作,PM 想在同一套工具链里改代码、发起 PR | Free 版有限额度;Pro 约 $10/月,Pro+ 约 $39/月,Max 约 $100/月(2026 年 6 月起改为额度制计费,超额需另购) |

这一类对完全零代码基础的 PM 来说,启动成本仍比第二节平台高一些——你至少要能大致判断"报错"和"跑通"长什么样。但门槛不等于"必须先学会 IDE":用 Codex / Antigravity / Claude Code 的桌面端或 CLI,同样能在真实代码库上迭代。粗分一下:Claude Code、Codex、Antigravity 更偏"交给 Agent 整段干活"(且都有独立于 IDE 的入口);Cursor 更偏"在编辑器里边看边改";GitHub Copilot 则绑在 GitHub 协作链路上。

## 二、全栈网页应用生成平台

这一类工具的共同特点是:打开网页,用对话的方式描述你要什么,平台直接生成一个能运行、甚至能拿到公开链接分享的网页应用,不需要你接触终端。这是目前对"零代码基础 PM"最友好的一类。

| 工具 | 定位 | 适合场景 | 门槛 / 官方状态与价格（访问 2026-09-14） |
|------|------|----------|----------------------------|
| **v0**(Vercel) | 对话生成前端页面和组件,底层用 React + Tailwind CSS + shadcn/ui,并提供沙箱、数据库连接与部署能力 | 想快速出一版视觉和交互都过得去的界面原型,给设计或用户做评审 | Free $0/月,含每月 $5 credits、每日 7 次消息;Plus $30/用户/月;Business $100/用户/月;Enterprise 定制,具体以 [官方 pricing](https://v0.app/pricing) 为准 |
| **Bolt**(StackBlitz) | 浏览器内一句话生成可运行的全栈应用,内置数据库、部署,能对接 Supabase、Stripe、Netlify、GitHub 等第三方服务 | 想要一个"从零到能跑起来"的完整小应用,不只是前端界面 | Free $0/月,每日 30 万、每月 100 万 tokens;Pro $25/月起、每月 1000 万 tokens 起且无每日上限;Teams $30/成员/月;额度与滚存规则以 [官方 pricing](https://bolt.new/pricing) 为准 |
| **Lovable** | 定位为对话式"AI 软件工程师",强调完全不懂技术的人也能独立把想法做成一个可用的 Web 应用 | 完全零代码背景的 PM,想独立从零搭出一个可演示、甚至可小范围试用的产品雏形 | Free / Pro / Business / Enterprise 按 workspace credits 与功能分层;Enterprise 为定制与按量 credits;不抄录易变额度,以 [官方 pricing](https://lovable.dev/pricing) 当前页面为准 |
| **Replit Agent** | 云端 IDE + Agent 一体化,能自主调试报错、写测试、管理数据库、直接部署上线,按任务复杂度计费("effort-based pricing") | 想要"做完当场就有个线上地址可以发给别人"的完整体验 | Core $20/月或年付折合 $18/月;Pro $100/月或年付折合 $90/月;Enterprise 定制;Agent 另按任务 effort 计费,以 [官方 pricing](https://replit.com/pricing) 与 [AI billing](https://docs.replit.com/billing/ai-billing) 为准 |

这四个工具的差异主要在于"前端精致度 vs 全栈完整度"的取舍:v0 更偏前端生成、沙箱与 Vercel 部署;Bolt、Replit Agent、Lovable 都主打"从想法到能跑起来的完整应用",适合你需要一个带数据库、能交互的真实产品雏形,而不只是一张能点的界面图。若你的不确定性主要在**视觉与品牌规范**,而不是"有没有后端/能不能部署",下一节的设计落地工具往往更对口。

## 三、设计落地 / Vibe Design 工具

这一类解决的不是"写业务逻辑",而是**把界面意图变成可版本管理的设计产物,并尽量直接落到代码**。和第二节的差别:全栈生成平台偏"一句话出可运行应用";设计落地工具偏"画布 / 设计系统 + Agent,先把 UI 定准"。和 Figma 传统工作流的差别:默认目标是**可进 Git、可交给编程 Agent 继续改**,而不是只交一张设计稿。

| 工具 | 定位 | 适合场景 | 门槛 / 官方状态与价格（访问 2026-09-14） |
|------|------|----------|----------------------------|
| **[pen.dev](https://www.pen.dev/)**（原 Pencil） | AI 原生设计画布:可在 Cursor / VS Code 扩展或独立桌面端里画界面,设计存为可进 Git 的 `.pen` 文件,经 MCP 交给编程 Agent"落地成代码" | 已在用 Cursor / Claude Code,想在同一工作区完成"设计 → 代码",减少 Figma 导出再翻译的损耗 | 官方页面标注当前免费,未来付费功能会在收费前通过条款说明;AI 能力依赖你已有的编程 Agent 订阅及 token 用量 |
| **[OpenDesign](https://open-design.ai/official/)** | 开源、local-first 的 vibe design 工作区(Claude Design 的开源替代方向):用你本机已有的编程 Agent CLI 当引擎,靠 `DESIGN.md` 设计系统与 skills/插件产出原型、页面、幻灯片等,产物归本地文件 | 在意本地化与可带走性、不想锁在单一厂商托管设计产品;已有 Claude Code / Codex / Cursor 等 CLI 可 BYOK | 开源 Apache-2.0,工具本身免费;成本主要是你自带的 Agent / API 用量,最新版本与本地安装方式以官方页为准 |
| **Claude Design**(Anthropic,对照) | Anthropic 托管的设计向能力:对话里直接出设计产物,闭源、云端、模型绑定 | 已在 Claude 订阅体系内、接受托管与厂商锁定时快速出视觉稿 | 随 Claude 订阅;与 OpenDesign 形成"托管闭源 vs 本地开源"对照 |

选型直觉:**要视觉定稿 + 进工程** → pen.dev / Pencil(尤其已在 Cursor 生态);**要本地、开源、可换 Agent** → OpenDesign;只想最快出一版能点的应用、不太抠视觉规范 → 仍优先第二节的 v0 / Bolt / Lovable。这类工具通常**挂在第一节的编程 Agent 上**,不是替代 Agent。

## 四、低代码 / 工作流搭建平台(非主推荐)

> **选型态度先说清楚:这一类不再是 2026 年 AI PM 做 Demo 的主推荐。**  
> 和前几类最大的差别不在"好不好用",而在**上限和可带走性**:
>
> 1. **能力上限被平台框死**——你只能在平台预设的节点、组件、发布渠道里拼,超出边界就只能等厂商开放能力,很难像真实代码那样任意扩展。
> 2. **成果很难本地化带走**——流程、Prompt、知识库配置往往锁在平台云端;不能干净导出成可交给工程师继续维护的代码仓库,验证通过后仍要在正式工程里重做一遍,沉没成本高。
> 3. **和工程师协作链路断层**——前几类产出的是(或接近)真实代码 / 可进 Git 的设计文件,能直接进仓库、开 PR;低代码产物多半只能截图或录屏演示,很难成为团队共用的工程资产。
>
> 因此:默认优先用第一至三节的工具;只有当你**明确只验证流程串联、且接受"做完就留在平台里"**时,再考虑下面这些工具。它们仍然有用,只是定位从"首选"降为"特定场景备选"。这也是 [05-resources/tools.md](../../05-resources/tools.md) 中"工作流 / Agent 搭建平台"一节对应的品类。

| 工具 | 定位 | 适合场景 | 门槛 / 官方状态与价格（访问 2026-09-14） |
|------|------|----------|----------------------------|
| **n8n** | 节点式可视化工作流自动化平台,开源、可自托管,擅长把不同工具和 API(包括 AI 模型)串联成自动化流程 | 已有工具链要串自动化、且愿意自托管时相对更可带走;仍非做产品 Demo 的首选 | Cloud Starter / Pro / Enterprise 按 executions、存储和保留等层级计费;Community Edition 可自托管,基础设施自理;当前金额以 [官方 pricing](https://n8n.io/pricing/) 动态页面为准 |
| **Dify** | 面向 LLM 应用的开发平台,内置 RAG 检索管道、Agent 编排、Prompt 管理和可视化工作流编辑器,偏"搭建一个 AI 应用的后端逻辑" | 快速验证问答/RAG/Agent 后端逻辑;开源自托管版可减轻部分锁定,但前端与工程化仍需另做 | Sandbox 免费,每月 200 message credits;Professional $590/工作区/年、5,000 credits/月;Team $1,590/工作区/年、10,000 credits/月;Enterprise 定制;另提供 Community 自托管版本,以 [官方 pricing](https://www.dify.ai/pricing) 为准 |
| **Coze(扣子)**(字节跳动) | 国内主流的智能体 / 工作流搭建平台,可视化搭建 Agent 并一键发布到飞书、微信、网页等多渠道;2026 年新增"扣子编程",定位为面向 Vibe Coding 的基础设施 | **仅当**目标就是飞书/微信等渠道快速发布、且不打算迁出平台时考虑;不适合当长期产品雏形的主战场 | 个人、团队、企业版分层;企业版采用包年包月与按量混合计费,官方文档标注 2026-07-14 起生效的权益/计费调整;具体套餐与资源点以 [官方版本与计费文档](https://docs.coze.cn/guides_edition) 为准 |

三者里,n8n / Dify 因开源自托管,锁定程度相对轻一些;Coze 启动最快、渠道最熟,但也最典型地体现"做完带不走"。若你最终要把 Demo 交给工程师落地,优先用前几类工具重做,而不是指望从低代码平台"导出升级"。

## 五、2026 年 9 月的 Builder 增量

工具选型已经不只是“能不能生成代码”。PM 还要看 Agent 能否排队执行、能否被管理员约束，以及交付物处在 GA、Preview 还是 Experimental 阶段。以下状态均来自厂商公开入口，研究论文不列入可采购能力。

| 能力入口 | 截至 2026-09-14 的状态 | 选型时要核对 |
|---|---|---|
| [GitHub Copilot Agent Plugins](https://github.blog/changelog/2026-08-13-github-copilot-weekly-releases-august-10/) | Agent Plugins 1.0 GA，覆盖 VS Code、CLI、SDK 和 Copilot 应用 | 组织是否已启用对应产品，插件运行范围和模型额度 |
| [Copilot managed permissions](https://github.blog/changelog/2026-09-09-enterprise-managed-permissions-for-github-copilot-agent-operations/) | 企业托管权限 GA，可分别管控 Shell、文件读写和网络域名 | 管理员策略能否覆盖个人设置，审批谁负责，允许列表如何维护 |
| [Copilot weekly releases](https://github.blog/changelog/2026-09-10-github-copilot-weekly-releases-september-7/) | Voice mode 为 Experimental，Recurring agent tasks 为 Public Preview | 不把实验入口当稳定交付能力，确认地区和账户开关 |
| [Claude computer use tool](https://platform.claude.com/docs/en/agents-and-tools/tool-use/computer-use-tool) | `computer_toolset_20260801` 是 API 工具集，文档要求应用侧执行；不代表 Claude Managed Agents 已托管可用 | VM 或容器隔离、最小权限、域名 allowlist、人工确认和审计 |
| [GPT-Live-1 in the API](https://openai.com/index/introducing-gpt-live-1-in-the-api/) | OpenAI 官方公告称 API 已可用，支持全双工语音、打断和后台委派 | 当前模型、地区、用量和语音层价格须以 API 定价页复核，不能把公告示例价当长期报价 |

这组增量会改变 Demo 的交付标准。可运行的链接之外，还应交付权限表、来源范围、任务状态和失败恢复说明。针对文档、实时语音和 Computer Use 的 PRD 骨架，分别见 [文档智能产品怎么设计](../prd-and-design/document-intelligence-design.md)、[实时语音 Agent 怎么设计](../prd-and-design/realtime-voice-agent-design.md) 和 [Computer Use 产品怎么设计](../prd-and-design/computer-use-product-design.md)。

## PM 该选哪一类:按"我要验证什么"分流

与其纠结"哪个工具最好",不如先问自己一个问题:**这次我要验证的核心不确定性是什么?**

| 我要验证的是… | 推荐工具类别 | 理由 |
|----------------|--------------|------|
| 这个交互流程顺不顺、界面看着对不对 | **优先**全栈应用生成平台(v0 / Bolt / Lovable);视觉规范要求高时加 pen.dev / OpenDesign | 几分钟到几十分钟出可点界面;抠品牌与设计系统时用第三节 |
| 这是不是一个能真正跑起来、能分享链接的产品雏形(有数据、有基本逻辑) | **优先**全栈应用生成平台(Bolt / Replit Agent / Lovable) | 内置数据库和部署,产出物比低代码更接近"真应用" |
| 界面视觉 / 设计系统要先定准,再落到工程代码 | **优先**设计落地工具(pen.dev / OpenDesign)+ 编程 Agent | 画布或 DESIGN.md 定稿后进 Git,减少"设计稿 → 代码"翻译损耗 |
| 我已有一定代码基础,要深度参与已有代码库的调试和长期迭代 | **优先**编程 Agent(Claude Code / Codex / Antigravity / Cursor / Copilot) | 操作真实代码,上限最高;可用独立桌面端或 CLI,不必绑 IDE |
| 一条自动化流程是否成立,且我接受成果留在平台、不迁本地 | 工作流平台作**备选**(n8n / Dify;渠道发布可看 Coze) | 串步骤快,但上限与可带走性都弱于前几类——不要当默认首选 |
| 目标就是飞书/微信渠道快速发布、明确不迁出 | Coze(备选) | 渠道发布方便;若还要做成可维护产品,仍应用前几类重做 |

实际工作中更常见的组合是:**pen.dev / OpenDesign 定视觉 → Bolt / Lovable 出可点原型 → Claude Code / Codex / Antigravity(桌面端或 CLI)或 Cursor 在正式代码库里重做**。低代码平台可以偶尔用来摸清"流程能不能串起来",但不要默认把它当成从想法到 Demo 的主路径——验证通过后,仍建议用能产出真实代码的工具重新实现一遍。

## 相关阅读

- [Vibe Coding 是什么:AI PM 该不该自己写代码](what-is-vibe-coding.md)
- [传统 PM 转型 AI PM 指南](../../00-roadmap/transition-guide.md)
- [Agent 入门:规划、记忆、工具调用](../../01-ai-basics/llm/agent-basics.md)
- [值得上手的 AI 工具与平台](../../05-resources/tools.md)
- [从一句话需求到能跑的原型:AI PM 的 Vibe Coding 实战方法论](from-idea-to-demo.md)
- [AI 产品 PRD 怎么写](../prd-and-design/ai-prd-guide.md)

## 参考资料

- [Plans & Pricing | Claude by Anthropic](https://claude.com/pricing) — Anthropic 官方定价页,2026-07 访问
- [Pricing | OpenAI Codex](https://developers.openai.com/codex/pricing) — OpenAI Codex 官方定价页,2026-07 访问
- [Introducing Google Antigravity 2.0](https://antigravity.google/blog/introducing-google-antigravity-2) — Google Antigravity 官方博客(独立桌面端说明),2026-09-14 访问
- [Antigravity plans](https://antigravity.google/docs/plans) — Google Antigravity 官方计划与 quota 说明,个人 $0 起、Pro/Ultra 配额与 credits 规则,2026-09-14 访问
- [Antigravity pricing](https://antigravity.google/pricing?app=antigravity-ide-) — Google Antigravity 官方定价入口,2026-09-14 访问
- [Cursor - Pricing](https://cursor.com/pricing) — Cursor 官方定价页,2026-07 访问
- [GitHub Copilot · Plans & pricing](https://github.com/features/copilot/plans) — GitHub 官方定价页,2026-07 访问
- [v0 by Vercel - Pricing](https://v0.app/pricing) — Vercel v0 官方定价页,2026-09-14 访问
- [Updated v0 pricing](https://vercel.com/blog/updated-v0-pricing) — Vercel 官方博客,2026-09-14 访问
- [Bolt - Plans & pricing](https://bolt.new/pricing) — Bolt(StackBlitz)官方定价页,2026-09-14 访问
- [Lovable - Pricing](https://lovable.dev/pricing) — Lovable 官方定价页,2026-09-14 访问
- [Replit - Pricing](https://replit.com/pricing) — Replit 官方定价页,2026-09-14 访问
- [Replit AI billing](https://docs.replit.com/billing/ai-billing) — Replit Agent effort-based 计费说明,2026-09-14 访问
- [pen.dev Pricing](https://www.pen.dev/pricing) — pen.dev 官方定价页(当前免费,未来付费功能需另行说明),2026-09-14 访问
- [pen.dev Terms](https://www.pen.dev/terms-of-use) — pen.dev 使用条款(当前免费与未来付费说明),2026-09-14 访问
- [OpenDesign 官方页](https://open-design.ai/official/) — OpenDesign 官网 / 开源 vibe design 工作区,2026-09-14 访问
- [nexu-io/open-design](https://github.com/nexu-io/open-design/) — OpenDesign GitHub 仓库,2026-09-14 访问
- [n8n Plans and Pricing](https://n8n.io/pricing/) — n8n 官方定价页,2026-09-14 访问
- [n8n choose how to use](https://github.com/n8n-io/n8n-docs/blob/main/docs/get-started/choose-how-to-use-n8n.md) — n8n Cloud / Community Edition 说明,2026-09-14 访问
- [Dify Pricing](https://www.dify.ai/pricing) — Dify 官方定价页,2026-09-14 访问
- [扣子版本与计费](https://docs.coze.cn/guides_edition) — 扣子官方版本与计费说明,2026-09-14 访问
- [扣子企业版计费规则](https://docs.coze.cn/coze_pro_enterprise_plan) — 扣子官方文档,2026-09-14 访问
- [Enterprise managed permissions for GitHub Copilot agent operations](https://github.blog/changelog/2026-09-09-enterprise-managed-permissions-for-github-copilot-agent-operations/) — GitHub 官方 Changelog，2026-09-09 发布，2026-09-14 访问
- [GitHub Copilot weekly releases, September 7](https://github.blog/changelog/2026-09-10-github-copilot-weekly-releases-september-7/) — GitHub 官方 Changelog，Voice mode 为 Experimental、Recurring agent tasks 为 Public Preview，2026-09-10 发布，2026-09-14 访问
- [Introducing GPT-Live-1 in the API](https://openai.com/index/introducing-gpt-live-1-in-the-api/) — OpenAI 官方公告，2026-09-10 发布，2026-09-14 访问
- [Computer use tool](https://platform.claude.com/docs/en/agents-and-tools/tool-use/computer-use-tool) — Anthropic 官方文档，工具集与安全边界，2026-09-14 访问
