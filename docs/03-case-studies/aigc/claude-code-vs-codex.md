---
title: claude-code-vs-codex
type: note
permalink: aipmwiki/docs/03-case-studies/aigc/claude-code-vs-codex
---

# Claude Code 与 OpenAI Codex：AI 编程 Agent 的运行环境与执行闭环对比

> **核验时间：2026 年 7 月 13 日。** 本文主要依据 Anthropic Claude Code 文档、OpenAI Codex 开发者文档与官方帮助页整理。两款产品更新很快，价格、模型、额度、计划权益和地区可用性只代表核验日页面；官方没有确认的内容标为“未披露”或“推测”，不把单次体验推断成官方承诺。

**分类**:AIGC
**体验时间**:2026 年 7 月（资料核验）

## 一、产品背景

AI 编程产品正在从“补全一行代码”转向“交给 Agent 一个可验收的任务”：读取代码库、修改多个文件、执行命令、运行检查，再返回差异和结果。Claude Code 与 OpenAI Codex 都属于这一类，但产品差异不应只看模型名称或单次生成质量，更应该看四件事：

1. Agent 在哪里运行：本机、隔离工作树，还是云端容器；
2. 它能执行到哪一步：只给建议，还是可以读写文件、运行命令、创建提交或 PR；
3. 人什么时候需要确认：权限由工具规则控制，还是由沙箱与审批策略共同控制；
4. 任务能否离开用户继续跑：异步云任务、计划任务，与“远程查看仍在本机运行的会话”并不是一回事。

| 维度 | Claude Code | OpenAI Codex |
|------|-------------|--------------|
| 产品定位 | Anthropic 的 agentic coding tool，覆盖终端、IDE、桌面端和浏览器 | OpenAI 的编程 Agent，覆盖桌面端、CLI、IDE extension 与 Codex cloud |
| 典型交付物 | 跨文件改动、测试/检查结果、diff，以及可选的 commit / branch / PR | 本地或云端任务结果、diff、检查结果，以及云端任务的后续 PR 流程 |
| 本地工作 | CLI、VS Code/JetBrains 等 IDE 集成、桌面端 | CLI、IDE extension、桌面端的 Local / Worktree 模式 |
| 云端工作 | Claude Code on the web；另有 Remote Control 让用户远程接续本地会话 | Codex cloud 在隔离容器中运行任务 |
| 主要控制面 | 细粒度的 allow / ask / deny 规则与 permission modes | sandbox mode 决定技术边界，approval policy 决定何时停下来询问 |

本文不做“谁的代码能力更强”的排行榜，而是把两套产品当成 **AI 编程任务基础设施** 来拆解。对于 PM 来说，运行边界、审批节奏和可验证交付比一张模型榜单更能决定产品能否进入真实研发流程。

## 二、核心功能拆解

### 1. 运行环境：本地上下文与云端隔离是两条产品路线

#### Claude Code：同一套 Agent 跨本地、桌面和 Web

官方概览把 Claude Code 描述为可以读取代码库、编辑文件、运行命令并连接开发工具的 Agent，入口包括：

- **Terminal**：功能完整的 CLI，直接在项目目录中编辑文件、运行命令和管理项目；
- **IDE**：VS Code 等集成提供 inline diff、文件引用、计划审阅与会话历史；
- **Desktop**：独立于 IDE 或终端运行，可并行开多个会话、查看可视化 diff、安排重复任务，并启动云端会话；
- **Web**：无需本地安装即可提交长任务，也可以处理本地没有的仓库或并行提交多个任务；
- **Remote Control**：浏览器或手机连接到正在本机运行的 Claude Code 会话。官方明确说明，这条路径不会把会话搬到云端，本机文件系统、MCP 和项目配置仍留在本机。

这里有一个容易被混淆的边界：**Claude Code on the web 是云端执行；Remote Control 是远程操控本地执行**。两者都能让用户离开电脑后继续关注任务，但数据边界、网络依赖和故障模式不同。

#### Codex：Local / Worktree / Cloud 三种任务环境

OpenAI 的 Codex 文档在桌面端把任务环境分成三种：

- **Local**：直接在当前项目目录运行；
- **Worktree**：在 Git worktree 中隔离改动，适合并行任务或不想污染当前工作区的场景；
- **Cloud**：在远程配置的云环境中运行。

Codex cloud 的官方流程更明确地写成了一条环境流水线：创建隔离容器，按选定分支或 commit SHA 检出仓库，执行 setup script，再由 Agent 循环运行终端命令、编辑代码、执行检查并尝试验证结果。任务结束后，用户可以看到最终回答和 diff，并继续追问或打开 PR。

云端环境的网络也不是默认全开：setup 阶段可以联网安装依赖，Agent 阶段默认关闭网络，需要时再为环境配置受限或不受限的访问。API key 登录支持本地 Codex 工作流，但 Codex cloud 要求使用 ChatGPT 登录；使用哪种身份，决定了可用的管理控制和计费口径。

### 2. 任务执行：都能“动手”，但交付边界不同

一个典型编程 Agent 任务可以抽象为：

**理解目标 → 读取仓库与规则 → 形成计划 → 调用文件/终端工具 → 修改代码 → 执行检查 → 汇总 diff、日志和剩余风险 → 人工审阅与合并**

| 环节 | Claude Code | Codex |
|------|-------------|-------|
| 读取上下文 | 读取代码库、项目说明、CLAUDE.md、技能和已连接工具 | 读取代码库、AGENTS.md、配置、IDE 上下文和已连接工具 |
| 修改与执行 | 官方文档明确覆盖文件编辑、命令执行、测试、Git 操作；可创建 commit、branch 和 PR | 本地或云端运行终端命令并编辑文件；云端任务按分支/commit 在隔离环境中执行 |
| 代码验证 | 官方工作流示例包含写测试、运行测试并修复失败；概览也把“验证能否工作”列为任务能力 | 云环境文档明确写明 Agent 会运行 checks 并尝试验证工作；代码评审可在不修改工作区的情况下给出优先级排序的发现 |
| 评审与交接 | 可以在 diff、PR、代码评审和会话之间接续 | /review 或评审面板默认只报告 findings，不改变工作树；如果让 Codex 应用修复，仍遵循正常 sandbox 与 approval 设置 |

“可以运行测试”不等于“测试必然通过”，也不等于“代码已经适合合并”。两家官方文档都没有把所有项目的测试、构建、类型检查或安全扫描承诺为统一的强制门禁。产品设计上，Agent 的最终回复应该同时包含：

- 改了哪些文件、为什么改；
- 执行了哪些命令、命令是否成功；
- 哪些检查没有运行，以及原因；
- 尚未验证的风险和建议的人审点。

### 3. 权限与确认：规则系统 vs 沙箱系统

#### Claude Code：按工具和规则细分确认

Claude Code 的官方权限表把工具分成三类：

- 只读工具（读取文件、Grep）在工作目录和附加目录内通常不需要确认；
- Bash 命令默认需要确认，但内置的只读命令例外；
- 文件编辑/写入默认需要确认。

用户可以通过 /permissions 管理规则。规则有 **allow、ask、deny** 三种意图，并且可以写入版本控制、分发给团队。官方还提供多种 permission mode：默认模式、自动接受编辑的 acceptEdits、只读探索的 plan、带背景安全检查的 auto、仅允许预先批准工具的 dontAsk，以及跳过大部分提示的 bypassPermissions。后两种高权限模式仍受显式 ask 规则或安全断路器约束；官方特别提醒，不要在没有隔离的环境中随意使用 bypassPermissions。

这套设计的产品特点是：**确认粒度可以落到某个工具、某类命令或某个路径**。它把“Agent 能不能做”与“这一次是否需要人确认”放进同一个可配置策略系统。

#### Codex：技术隔离与审批时机分层

Codex 官方把安全控制拆为两层：

1. **Sandbox mode**：技术上允许 Agent 触碰什么，例如能否写入工作区、能否访问网络；
2. **Approval policy**：什么时候必须先停下来请求批准，例如访问网络、修改工作区外的文件或执行不在可信范围内的命令。

本地 CLI/IDE 的默认方向是 OS 级沙箱、写权限限制在当前工作区、网络关闭。Auto 预设允许在工作区内读文件、改文件和运行命令；访问工作区外或网络通常需要批准。也可以切换为 read-only，只读浏览而不修改。Codex cloud 则使用 OpenAI 管理的隔离容器，setup 和 Agent 阶段有不同网络与 secret 边界。

Codex 还保留 --yolo 等“无沙箱、无审批”的高风险组合，官方明确不建议作为常规工作方式。与 Claude Code 的工具规则相比，Codex 更强调 **运行时边界先挡住风险，再用审批策略控制例外**。

### 4. 异步工作流：云端任务、计划任务与远程接续

| 场景 | Claude Code | Codex |
|------|-------------|-------|
| 离开电脑后继续跑 | Web 可提交长任务；Desktop 可启动云会话和重复任务 | Codex cloud 把任务委托给隔离环境；桌面端也支持后台/计划任务 |
| 多任务并行 | Web、Desktop、Agent view、worktree 等入口可并行，具体能力依计划和版本 | Worktree 隔离本地任务；Cloud 任务在独立容器中运行 |
| 手机/浏览器接续 | Remote Control 连接本机正在运行的会话；Web 则是云端会话 | Codex cloud 可在任务结束后回看结果并追问；API key 路径不提供云端功能 |
| 结果交接 | diff、测试结果、PR、会话历史 | diff、检查结果、评审 findings、后续 PR |

异步产品的关键不是简单地加一个“后台运行”按钮，而是让用户知道：任务在哪台机器上、使用哪个分支或 commit、网络是否打开、何时需要批准、失败后能否继续，以及结果是否已经通过独立检查。Claude Code 的 Remote Control 与 Claude Code on the web、Codex 的 Local/Worktree 与 Cloud，都应该在界面上用不同标签明确区分。

## 三、技术方案推测

> 以下内容把官方已披露的信息与推测分开。两家公司都没有完整公开 Agent 的模型路由、上下文组装、仓库索引实现、工具编排策略和测试选择算法。

### 官方已披露的组成

- **Claude Code**：官方文档公开了内置工具、CLAUDE.md 项目指令、Skills、subagents、hooks、MCP、Git 集成、权限规则、沙箱与多种运行入口。Web 任务还涉及云环境、setup script、网络设置和 Docker 配置。
- **Codex**：官方文档公开了 CLI、IDE extension、桌面端、Codex cloud、AGENTS.md、MCP、worktree、sandbox、approval policy、云环境 setup script 和 code review。云端 Agent 的“终端命令循环—编辑—运行检查—尝试验证”也有明确描述。

### 基于公开行为的推测

1. **两者都可能采用“观察—工具调用—读取结果—继续规划”的循环**。这是对 Agent 产品形态的合理抽象，能够解释它们为什么可以跨文件工作、根据命令输出继续修复；但具体状态机、模型调用次数和上下文压缩策略均未披露。
2. **项目说明文件是上下文治理层，而不是模型能力本身**。Claude Code 的 CLAUDE.md 与 Codex 的 AGENTS.md 都让团队把命令、目录约束和验收标准写进仓库；但两者的优先级、注入时机和完整解析规则不应未经文档确认就视为相同。
3. **本地工作树、云端容器和权限沙箱解决的是不同问题**。Worktree 主要解决文件改动互相覆盖，sandbox 主要解决进程能触碰什么，approval 主要解决关键动作是否需要人确认。把三者都叫“隔离”会掩盖真实风险边界。
4. **是否使用 RAG、代码图或专用检索索引：未披露**。从 Agent 能读取代码库不能反推出具体采用了哪种检索架构；本文不把这一点写成确定事实。
5. **模型路由与成本控制：未披露**。官方页面会列出可用模型、额度或 credits，但没有因此公开完整的内部路由、缓存命中率和单任务成本模型。

## 四、商业模式

### Claude Code：订阅、Console 与第三方提供商并存

Anthropic 官方概览说明，多数 Claude Code 入口需要 Claude 订阅或 Anthropic Console 账号；Terminal CLI 和 VS Code 还支持第三方模型提供商。桌面端文档明确要求付费订阅。官方价格页同时列出 Claude 的 Free、Pro、Max、Team、Enterprise 方案及 API 定价，但 Claude Code 在不同入口、计划、地区和提供商下的可用功能与额度可能不同。

因此，Claude Code 的商业化可以读成三条路径：

1. **个人订阅**：把日常对话、终端、IDE、桌面和 Web 能力放进 Claude 方案，用额度和功能可用性承接轻度到重度用户；
2. **团队/企业订阅**：用管理设置、权限策略、用量分析和组织级治理承接团队；
3. **Console/API/第三方提供商**：让开发者或组织按各自平台的模型调用和治理方式使用。

本文不把某个页面上的具体 Claude 价格或额度固化进表格：官方页面更新频繁，且本次核验材料不足以确认每个入口与每个计划的完整权益。**具体金额、地区可用性和 Claude Code 是否包含在某个计划中，发布前应重新核验 Claude 官方价格页与 Feature availability 页面。**

### Codex：ChatGPT 计划用量 + credits + API key

OpenAI Codex 官方定价页在本次核验时列出的个人档位如下，金额均为页面当前显示的美元月价；它们是动态信息，不应当视为长期承诺：

| 档位 | 官方页面核验到的价格 | 页面给出的 Codex 定位 |
|------|----------------------|----------------------|
| Free | $0/月 | 体验 Codex，适合快速编码任务 |
| Go | $8/月 | 轻量级编码任务 |
| Plus | $20/月 | 每周若干次聚焦编码会话；页面列出 Web、CLI、IDE extension、iOS 与云端集成 |
| Pro | $100/月起 | 5× 或 20× Plus 的更高用量档位 |
| Business | 年付 $20/用户/月；月付 $25/用户/月 | 团队工作区与管理能力 |
| Enterprise & Edu | 联系销售 | 企业级控制、审计、数据留存/驻留等能力 |

Codex 的计费与可用性有几个值得记录的产品点：

- ChatGPT Work 与 Codex 共享 usage、credits 和 usage limits；
- 本地消息与云任务共享五小时窗口，具体消耗取决于模型、上下文、推理、工具、检索和缓存，官方没有承诺“一个任务固定消耗多少”；
- Plus/Pro 达到使用上限后可以购买额外 credits；Business、Edu、Enterprise 是否可灵活购买 credits 取决于工作区计费配置；
- API key 走 OpenAI API 标准费率，适合 CLI、SDK 或 CI 等本地/程序化场景，但 API key 路径没有 Codex cloud 等依赖 ChatGPT 工作区的能力；
- 计划价格、模型名称、使用上限和地区/工作区资格均可能变化，本文不展开固定消息数或具体 credits 单价。

两者的商业化差异因此不只是“谁更便宜”：

| 设计轴 | Claude Code | Codex |
|---------|-------------|-------|
| 主要购买关系 | Claude 订阅、Anthropic Console 或第三方提供商 | ChatGPT 计划、工作区 credits 或 OpenAI API key |
| 重度用户升级 | 计划额度、模型/功能可用性与团队治理 | 计划档位、共享用量窗口、额外 credits 或 API 按量 |
| 云端能力的资格 | 受 Claude 入口、计划和组织设置影响 | Codex cloud 要求 ChatGPT 登录，计划/区域/工作区资格影响可用性 |
| 成本可解释性 | 需要把 Claude 计划与入口/提供商组合起来看 | 官方直接把本地、云任务、评审和 credits 放进同一套用量叙事 |

## 五、PM 视角的启示

1. **先定义“在哪里完成”，再定义“能做什么”。** 同一个 Agent 放在本机、worktree、云容器或远程控制通道里，数据边界、延迟、网络权限和失败恢复都不同。产品设计应先展示运行位置、分支/commit、网络状态和可写范围，再展示模型与能力清单。
2. **权限是主流程，不是设置页里的合规补丁。** Claude Code 的工具级 ask/allow/deny 与 Codex 的 sandbox/approval 分层，说明“什么时候问用户”本身就是 Agent 的交互设计。好的默认值要让低风险读操作顺畅，让写文件、联网、外部系统和破坏性命令有可理解的确认理由。
3. **把“验证尝试”做成可审计交付物。** Agent 能运行测试只是执行能力；PM 还需要设计检查清单、命令日志、失败重试、diff、风险摘要和人工签核，让用户能区分“代码改完了”“检查跑完了”和“可以合并了”。
4. **异步任务需要清晰的接力协议。** 任务离开当前窗口后，用户最关心的是它是否仍在运行、在哪里运行、何时需要输入、结果写到了哪个分支，以及失败后能否恢复。云端任务、计划任务、移动端接续和本地 Remote Control 不应共用模糊的“后台执行”标签。
5. **Agent 定价要围绕任务复杂度解释，而不是只展示功能数量。** 长上下文、多轮工具调用、云端执行和独立评审的成本方差都更大。无论采用订阅额度、credits 还是 API 按量，都应告诉用户用量为何变化，并给出“缩小任务、切换模型、继续购买额度、改用 API”等明确的下一步。

## 相关阅读

- [Cursor 与 GitHub Copilot:AI 代码助手的付费转化](cursor-vs-copilot.md) —— 对照 IDE/代码托管生态下的订阅与用量分层
- [Vibe Coding 工具盘点与选型](../../02-pm-skills/vibe-coding/tool-landscape.md) —— 从入口形态和任务类型选择编程 Agent
- [Agent 基础:从 Chatbot 到智能体](../../01-ai-basics/llm/agent-basics.md) —— 理解规划、工具调用与执行闭环
- [Agent Skills:可复用的 Agent 能力包](../../01-ai-basics/llm/agent-skills.md) —— 了解项目规则与可复用能力如何进入 Agent
- [MCP:模型上下文协议](../../01-ai-basics/llm/mcp.md) —— 对照外部工具与上下文连接方式
- [Claude 案例拆解](../chatbot-assistant/claude-anthropic.md) —— 了解 Anthropic 的通用助手与订阅矩阵
- [ChatGPT 案例拆解](../chatbot-assistant/chatgpt.md) —— 了解 Codex 所在的 ChatGPT 产品体系

## 参考资料

- [Claude Code Overview](https://code.claude.com/docs/en/overview) — Anthropic 官方产品/开发者文档，终端、IDE、桌面端与 Web 入口、任务能力，2026-07-13 访问
- [Configure permissions](https://code.claude.com/docs/en/permissions) — Anthropic 官方帮助文档，权限规则与 permission modes，2026-07-13 访问
- [Claude Code on the web](https://code.claude.com/docs/en/claude-code-on-the-web) — Anthropic 官方开发者文档，云环境、网络与 Web 任务，2026-07-13 访问
- [Remote Control](https://code.claude.com/docs/en/remote-control) — Anthropic 官方开发者文档，本地会话的浏览器/移动端接续，2026-07-13 访问
- [Desktop application](https://code.claude.com/docs/en/desktop) — Anthropic 官方开发者文档，桌面端并行会话、diff、计划任务与云会话，2026-07-13 访问
- [Plans & Pricing | Claude](https://claude.com/pricing) — Anthropic 官方定价页，计划与 API 定价入口；具体 Claude Code 权益待按发布日核验，2026-07-13 访问
- [Agent approvals & security](https://developers.openai.com/codex/agent-approvals-security) — OpenAI 官方开发者文档，sandbox、approval policy 与网络边界，2026-07-13 访问
- [Codex environments](https://developers.openai.com/codex/environments/modes) — OpenAI 官方开发者文档，Local、Worktree、Cloud 任务环境，2026-07-13 访问
- [Cloud environments](https://developers.openai.com/codex/environments/cloud-environment) — OpenAI 官方开发者文档，云端容器、setup script、网络与验证流程，2026-07-13 访问
- [Code review](https://developers.openai.com/codex/code-review) — OpenAI 官方开发者文档，评审范围、findings 与应用修复的权限关系，2026-07-13 访问
- [Authentication](https://developers.openai.com/codex/auth) — OpenAI 官方开发者文档，ChatGPT 登录、API key 与 Codex cloud 可用性，2026-07-13 访问
- [Scheduled tasks](https://developers.openai.com/codex/automations) — OpenAI 官方开发者文档，后台计划任务与本地/Worktree 选择，2026-07-13 访问
- [Pricing | Codex](https://developers.openai.com/codex/pricing) — OpenAI 官方定价与用量文档，计划价格、共享 usage、credits 与动态使用上限，2026-07-13 访问