---
title: cli-vs-mcp
type: note
permalink: aipmwiki/docs/04-interview/basics/cli-vs-mcp
---

# 大厂为什么都在推 CLI?CLI 会取代 MCP 吗?

**难度**:⭐⭐⭐ 进阶
**出现频率**:中高,Agent 平台、开发者工具、基础架构协作面常见

## 考察点

- 能否从分发、权限、可脚本化、成本解释「CLI 热」
- 是否理解 CLI 与 MCP 解决的是不同层问题
- 能否给出「取代论」的产品判断,而不是站队口号

## 参考答案

**一句话**:大厂推 CLI,是因为**终端是程序员最高杠杆的统一入口**——可脚本、可权限收敛、可进 CI;MCP 解决的是**工具/数据如何标准接入 Agent**,二者不是同一赛道上的零和。

### 为什么推 CLI(产品视角)

| 动机 | 解释 |
|------|------|
| 分发简单 | 一条安装命令,嵌进已有 shell 习惯,比再做一个 GUI 快 |
| 可编排 | 易进 Makefile/CI/定时任务,Agent「干活」需要可重复执行 |
| 权限模型清晰 | 本地进程权限、目录沙箱、人工确认点更好做 |
| 可观测 | stdout/stderr、退出码天然适合日志与评测 |
| 与 Coding Agent 同构 | Claude Code、Codex CLI 等本身就是「对话 + 工具」在终端里跑 |

对 PM:CLI 热 ≠「GUI 死了」,而是 **Agent 的执行面**正在向「可编程界面」集中;面向非开发者仍要 GUI/宿主 App。

### CLI 会不会取代 MCP?

**短答:不会取代,更可能分层共存。**

| | CLI | MCP |
|--|-----|-----|
| 本质 | 一种**交互/分发形态**(人或其他程序怎么调用能力) | 一种**连接协议**(Agent 怎么发现/调用外部工具与资源) |
| 擅长 | 本地工作流、脚本化、开发者入口 | 跨 Host 复用连接器、标准化 Tools/Resources |
| 弱项 | 每个工具一套旗标与鉴权,N×M 集成仍痛 | 不等于自动拥有好 UX;还要 Host 实现与治理 |

更准确的说法:

- **CLI 可以成为 MCP Server 的一种运输/封装方式**(本地 STDIO 常见就是「背后起一个进程」)。
- **MCP 也可以被 GUI、IDE、网页 Host 使用**,不绑定终端。
- 真正会竞争的是「专有插件协议 vs 开放 MCP」,不是「CLI vs MCP」。

选型:做给开发者的 Agent 运行时 → CLI 很香;做企业系统连接生态 → 优先 MCP(或两者都做)。详见 [MCP](../../01-ai-basics/llm/mcp.md)。

## 追问延伸

- **追问 1**:"那 IDE 插件还有意义吗?"
  → 有。IDE 提供编辑器上下文、可视化 diff、内联体验;CLI 提供无头自动化。很多产品是「IDE + CLI」双入口。
- **追问 2**:"安全上 CLI 更危险还是更安全?"
  → 都能很危险。CLI 默认能力强,必须有确认、沙箱、审计;MCP 远程服务器则要管鉴权与供给链。比的是治理,不是形态。
- **追问 3**:"如果只能先做一件事?"
  → 看用户:开发者工作流先 CLI;要接很多 SaaS/内部系统先 MCP 连接层。

## 相关阅读

- [MCP:给 Agent 接上外部世界的「USB-C」](../../01-ai-basics/llm/mcp.md)
- [Agent Skills:给 Agent 装上可复用的「操作手册」](../../01-ai-basics/llm/agent-skills.md)
- [Agent 入门:规划、记忆、工具调用](../../01-ai-basics/llm/agent-basics.md)
- [Vibe Coding 工具盘点](../../02-pm-skills/vibe-coding/tool-landscape.md)