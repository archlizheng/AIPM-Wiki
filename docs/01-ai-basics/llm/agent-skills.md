---
title: agent-skills
type: note
permalink: aipmwiki/docs/01-ai-basics/llm/agent-skills
---

# Agent Skills:给 Agent 装上可复用的「操作手册」

> 时效说明:本文机制与选型框架相对稳定;客户端名单、预置 Skills 目录等细节截至 **2026-07**,请以 [agentskills.io](https://agentskills.io/) 与各厂商文档为准。

## 一句话说清

**Agent Skills**(常简称 Skills)是一套开放格式:用一个文件夹打包「何时用、怎么做」的程序性知识(指令、可选脚本与参考资料),让通用 Agent 在相关任务上**按需加载**成领域专家——而不是把整本手册塞进每一次对话的系统提示词。

Anthropic 的类比很贴切:给 Agent 写 Skill,就像给新同事写**入职手册**,而不是为每个岗位单独造一个全新的人。

## 它解决什么问题

读完 [Agent 入门](agent-basics.md) 会发现:Agent 已经能规划、调工具、看反馈,但落地时仍常卡在两件事上——

1. **领域流程装不进权重里**:品牌规范、财务关账步骤、你们团队的 Code Review 清单,不可能也不该靠微调塞进模型。
2. **上下文装不下、也装不精**:把所有规范一次性塞进 System Prompt / Rules,既贵又互相干扰;任务无关时还在占窗口。

Skills 的答案是:**可发现、可组合、按需加载的能力包**。Agent 启动时只知道「有哪些手册、各自管什么」;真正相关时才打开对应章节。

它和「系统提示词 / 项目 Rules」的差别可以记成:

| | 系统提示词 / Rules | Agent Skills |
|--|-------------------|--------------|
| 作用范围 | 往往全局或会话级,长期生效 | 按任务匹配触发 |
| 加载方式 | 通常整段进上下文 | 渐进披露,用多少读多少 |
| 形态 | 一段或几段文本 | 文件夹 + `SKILL.md`(+ 脚本/资料) |
| 复用 | 难在多产品间原样搬运 | 开放标准,一份多端可用 |

## 起源与发展(2025 → 2026)

| 时间 | 事件 | 对产品的含义 |
|------|------|--------------|
| **2025-10-16** | Anthropic 发布 Agent Skills,覆盖 Claude apps、Claude Code、API | Skills 从「内部技巧」变成一等产品能力 |
| **2025-12-18** | 发布为开放标准 [agentskills.io](https://agentskills.io/),并推进组织级管理与合作伙伴目录 | 「写一次、多 Agent 复用」成为明确目标 |
| **2026 上半年** | Cursor、OpenAI Codex、Gemini CLI、GitHub Copilot、VS Code 等官方支持同一格式;agentskills.io Client Showcase 已列出数十个客户端 | 选型时更应问「Skill 资产能否跨工具带走」,而不是绑死一家 IDE |

公开示例与目录在扩张(如 Anthropic / OpenAI 的官方 skills 仓库、社区目录),具体数量变化快,不必背数字,记住趋势即可:**程序性知识正在从聊天记录外化成可版本管理的仓库资产**。

## 机制:一个文件夹 + 渐进披露

### 长什么样

最小形态就是一个目录,里面有必填的 `SKILL.md`:

```text
my-skill/
├── SKILL.md          # 必填:元数据 + 指令
├── scripts/          # 可选:可执行代码
├── references/       # 可选:详细文档
└── assets/           # 可选:模板、样例资源
```

`SKILL.md` 开头是 YAML frontmatter,至少包含:

- **name**:技能标识
- **description**:写清**何时该用、何时不该用**——Agent 主要靠这段做触发判断

正文则是工作流、约束、示例与指向附属文件的链接。需要确定性步骤时,可以附带脚本让 Agent 去执行,而不是用生成 token 去「模拟」排序、填表这类事。

### 渐进披露(Progressive disclosure)

这是 Skills 能规模化的核心设计,分三层理解即可:

1. **发现**:启动时只把各 Skill 的 name / description 放进系统侧上下文(很轻)。
2. **激活**:用户任务与某条 description 匹配时,再读入完整 `SKILL.md`。
3. **执行**:按需打开 `references/`、跑 `scripts/`,用多少加载多少。

因此你可以安装很多 Skills,而不必让每一次对话都背负全部手册。OpenAI Codex 等实现还会限制「初始 Skills 列表」占用的上下文比例,Skill 很多时会先压缩 description——这也是为什么 **description 要写得短、触发词靠前、边界清晰**。

## 概念族对照:面试常考一张表

海信等校招面经已出现「讲一下 Agent / Workflow / Function Calling / Skill」(见[海信 AI PM 面经](../../04-interview/experiences/hisense-ai-pm-202607.md))。用同一条链路串起来最稳:

```
用户目标
  → Workflow:步骤是否被人写死?
  → Agent:是否由模型动态决定下一步?
  → Function Calling / Tools:如何调用单个能力?
  → MCP:如何用标准协议接到外部系统?
  → Skill:如何按需加载「这类任务该怎么做」的手册?
  → Subagent:是否拆给专精子代理并行/分治?
```

| 概念 | 一句话 | 典型问题 |
|------|--------|----------|
| **Workflow** | 预定义路径编排 LLM 与工具 | 流程固定、要可控可审计 |
| **Agent** | 模型动态决定下一步与工具使用 | 任务开放、需边做边调整 |
| **Function Calling / Tools** | 模型输出结构化调用、执行单个动作 | 「能不能查库 / 发请求」 |
| **MCP** | Agent 与外部工具/数据的连接协议 | 「怎么稳定接到公司系统」 |
| **Skill** | 可复用的程序性知识包(按需加载) | 「怎么按我们的规范反复做好」 |
| **Subagent** | 把子任务交给另一个 Agent 上下文 | 「要不要隔离上下文、并行专精」 |

### Skills 和 MCP:互补,不是二选一

社区里出现过「Skills 是否取代 MCP」的争论,产品决策上更有用的拆法是:

| | **MCP** | **Skills** |
|--|---------|------------|
| 本质 | 连接层:触达外部世界 | 知识/剧本层:知道怎么做 |
| 执行 | 偏确定性 API 调用(固定 schema) | 偏 LLM 解释自然语言指令(+ 可选脚本) |
| 适合 | 实时数据、权限管控、精确读写外部系统 | 领域流程、品牌与合规规范、团队最佳实践 |
| 主要风险 | 选错工具、上下文被大结果污染、网络延迟 | 选错 Skill、**误解「怎么做」**、手册过时 |
| 维护 | 服务端更新可集中生效 | 本地/仓库文件,要当文档一样迭代 |

可以记:**MCP ≈ 神经系统,Skills ≈ 操作手册**。复杂工作流常常是「Skill 规定步骤与规范,MCP/Tools 负责每一步碰外部系统」。Anthropic 也明确把 Skills 定位为与 MCP **互补**的方向。MCP 的架构、三大原语与产品边界见 [MCP 专文](mcp.md)。

## 发展趋势(PM 该盯什么)

1. **跨平台标准资产化**:同一套 `SKILL.md` 可在 Claude、Cursor、Codex、Gemini CLI 等环境复用(各端目录名可能不同,如 `.cursor/skills/`、`.claude/skills/`、`.agents/skills/`,但格式同源)。组织开始把 Skills 当内部平台能力,而不是个人提示词收藏夹。
2. **效果可评测**:SkillsBench(2026-02)在 87 个任务上做「有/无 Skills」对照,汇总显示平均通过率约从 33.9% 提到 50.5%;并观察到**模块更少、更聚焦的 Skills**往往优于大而全捆绑,小模型配好 Skills 有时能追上更大模型裸跑。对 PM 的启示是:Skill 也要有评测集,不是写完就算交付。
3. **组织目录与治理**:企业级启用、版本管理、合作伙伴 Skills 目录出现——接下来的产品问题是权限、审计、谁有权发布。
4. **Agent 自生成 Skills**:官方展望包括让 Agent 把成功路径沉淀成 Skill、并自我评估迭代——「能力外化」会从人工编写走向半自动生产。
5. **安全成为一等约束**:Skills 可含指令与代码;恶意 Skill 足以诱导外连或危险操作。只安装可信来源、上线前审计脚本与外网指令,应写进产品与安全基线。

## 给 AI PM 的启示

- **何时做 Skill**:同一类任务反复出现、正确做法依赖组织上下文、且希望跨会话/跨人对齐结果时——优先 Skill,而不是让每个人私藏一段 Prompt。
- **何时不该硬上 Skill**:只要一次确定性 API 就能完成、或知识必须实时以服务端为准(频繁变更的 SDK 文档等)——MCP/工具或文档服务可能更合适;Skill 手册若维护跟不上,反而制造过时流程。
- **怎么写才触发得准**:把预算打在 `description` 上——场景、触发词、排除边界写清楚;正文保持可执行的步骤与检查清单;细节外置到 `references/`,避免主文件膨胀。
- **怎么验收**:准备「该触发 / 不该触发」用例 + 任务成功标准(可参考 SkillsBench 的配对评测思路);上线后看误触发率、补救次数、手册是否过期。
- **架构默认组合**:对外连接走 MCP/Tools,对内规范走 Skills,高风险步骤仍按 [Agent 入门](agent-basics.md) 设人工确认——三者叠在一起,而不是互相替换。

## 相关阅读

- B站:[从 LLM 到 Agent Skill](https://www.bilibili.com/video/BV1E7wtzaEdq)（马克的技术工作坊，~135 万播放）
- B站:[Agent、Skill、Harness 一次讲明白](https://www.bilibili.com/video/BV1YRG46eE1n)（通义实验室，~21 万播放）
- [Agent 入门:规划、记忆、工具调用](agent-basics.md)
- [MCP:给 Agent 接上外部世界的「USB-C」](mcp.md)
- [什么是 Agent?它和 Chatbot 的区别是什么?](../../04-interview/basics/agent-vs-chatbot.md)
- [什么是 Agentic Workflows?](../../04-interview/basics/agentic-workflows.md)
- [海信集团 AI 产品校招面经(含 Skill 考点)](../../04-interview/experiences/hisense-ai-pm-202607.md)
- [AI 术语速查表](../glossary.md)

## 参考资料

- [Introducing Agent Skills](https://www.anthropic.com/news/skills) — Anthropic,2025-10-16(2025-12-18 更新开放标准说明)
- [Equipping agents for the real world with Agent Skills](https://www.anthropic.com/engineering/equipping-agents-for-the-real-world-with-agent-skills) — Anthropic Engineering
- [Agent Skills Overview](https://agentskills.io/home) — 开放标准官网
- [Agent Skills - Claude Platform Docs](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/overview) — Anthropic 平台文档
- [Agent Skills | Cursor Docs](https://cursor.com/docs/skills) — Cursor 官方文档,2026-07 访问
- [Agent Skills – Codex](https://developers.openai.com/codex/skills) — OpenAI 开发者文档,2026-07 访问
### 对比解读（不替代规范）

以下是工具开发者或第三方的概念解读；协议事实以官方规范为准，平台支持以对应产品文档为准。

- [Skills vs MCP tools for agents: when to use what](https://www.llamaindex.ai/blog/skills-vs-mcp-tools-for-agents-when-to-use-what) — LlamaIndex
- [EP213: MCP vs Skills, Clearly Explained](https://blog.bytebytego.com/p/ep213-mcp-vs-skills-clearly-explained) — ByteByteGo,2026-05-02
- [SkillsBench](https://arxiv.org/abs/2602.12670) — arXiv:2602.12670,2026-02

## 2026-09 更新：从 Skill 目录到 Plugin 生命周期

单个 Skill 解决“这类任务怎么做”；当一组 Skill 需要共同依赖文档、MCP Server、权限和脚本时，产品形态开始变成 Plugin bundle。Google Cloud 2026-09-10 发布的 Developer Plugin 将 Skills、官方文档和远程 MCP servers 打包为可安装单元，并称其遵循 Agent Plugins 规范。该页面是公告，未明确所有客户端或生态已 GA，不能把“遵循规范”写成普遍兼容。

企业治理至少要把下面五件事一起做：

| 生命周期 | PM 要求 | 验收证据 |
|---|---|---|
| 来源 | 记录发布者、仓库、版本、依赖和许可证 | manifest、commit、来源链接 |
| 触发 | 说明何时启用、何时明确不启用 | 正/负触发用例 |
| 权限 | MCP 工具按只读、可逆写、不可逆写分级 | 服务端鉴权与确认日志 |
| 安全 | 扫描指令、脚本、外连和依赖 | 扫描结果、人工复核 |
| 运营 | 发布、灰度、回滚、过期和责任人 | 版本记录与停用演练 |

Anthropic 2026-08-06 的 Release Notes 显示，Enterprise 可开启第三方 Skill/Plugin security scanning（Beta），在上传或编辑时检查恶意内容。它是安全扫描能力，不代表扫描能证明 Skill 永远安全。含脚本和外部指令的 Skill 仍需限制网络、密钥和副作用工具。

内部 IT 服务助手的“设备申请 Skill”可以规定：先读 D01 生效制度、再收集缺失字段、生成草稿、等待 E01 确认、调用带 `request_key` 的提交工具、返回业务申请 ID；但 Skill 不能替代服务端权限，也不能把主管审批和 IT 交付写成 Agent 自动完成。

练习：给一个 Skill 写 manifest 草案，列出触发词、排除边界、MCP 依赖、敏感权限、评测用例、回滚版本和过期日期。至少准备一条“应触发”和一条“相似但不应触发”的输入。

参考更新：

- [Introducing the Google Cloud Developer Plugin for AI Coding Agents](https://cloud.google.com/blog/topics/developers-practitioners/introducing-the-google-cloud-developer-plugin-for-ai-coding-agents) — Google Cloud，2026-09-10，Plugin 公告，访问 2026-09-14。
- [Claude Release Notes](https://support.claude.com/en/articles/12138966-release-notes) — Anthropic，2026-08-06，Skill/Plugin scanning Beta，访问 2026-09-14。
- [agents-cli release notes](https://github.com/google/agents-cli/blob/main/RELEASE_NOTES.md) — Google，1.5.0，2026-09-01，扩展与评测相关变更，访问 2026-09-14。
