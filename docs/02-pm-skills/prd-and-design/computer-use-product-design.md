---
title: computer-use-product-design
type: note
permalink: aipmwiki/docs/02-pm-skills/prd-and-design/computer-use-product-design
---

# Computer Use 产品怎么设计

**层级**：进阶正课　**先修**：[Agent 产品生产化](../ai-product-operations/agent-product-design.md)、[Agent 安全与权限](../ai-product-operations/guardrails-and-agent-security.md)

Computer Use 让模型通过截图、鼠标和键盘操作桌面。它适合没有稳定接口的遗留软件、跨应用视觉检查和必须复现人类界面的任务。它不应成为绕过 API 权限、租户隔离或业务审批的捷径。

## 一、先选工具，再谈模型

| 任务 | 首选 | 原因 | 需要的人审 |
|---|---|---|---|
| 查询申请状态 | 业务 API | 权限和状态明确，可审计 | 处理异常状态 |
| 创建申请草稿 | 业务 API 的 draft 接口 | 可校验字段，容易幂等 | 提交前确认 |
| 遗留桌面软件无接口 | Computer Use | 能复现用户界面 | 每个高后果动作 |
| 网页内结构化操作 | 浏览器工具或 DOM 接口 | 比截图点击更稳定 | 登录、发送、删除 |
| 视觉回归检查 | 浏览器截图或 Computer Use | 能发现布局和渲染问题 | 发布前抽检 |

Google 在 2026-06-24 宣布 Gemini 3.5 Flash 内置 Computer Use，支持浏览器、移动和桌面环境，并提供敏感动作确认与提示注入停止能力。Google 当前 API 文档把 Computer Use 标为 Preview，并要求客户端实现动作执行循环。Anthropic 当前文档提供 `computer_toolset_20260801`，由应用侧执行截图、鼠标和键盘调用，同时明确要求隔离环境、最小权限和人工监督。它们是供应商能力说明，不能写成所有产品默认具备的安全保证。

### API、MCP 与 Computer Use 的关系

API 是业务系统或模型服务的调用界面，MCP 是连接 Agent 与工具/资源的协议层，Computer Use 是在没有稳定接口时对界面进行观察和操作的执行方式。三者可以组合，MCP 不是 API 的互斥替代品：例如用 MCP 发现只读资料工具，用业务 API 查询或提交申请，最后只在遗留软件没有接口时使用 Computer Use。选型表至少记录接口稳定性、权限重鉴权、可审计回执和用户接管点。

## 二、把观察、动作和回执分开

```text
目标 → 观察屏幕/读取页面 → 规划一步 → 申请权限
                         ↓
                  执行动作 → 读取回执 → 校验
                                      ↓
                             完成 / 重试 / 接管
```

每一步都记录截图或结构化回执、动作、工具身份、时间、页面来源和结果。页面文字可能是间接提示注入，模型不应把页面里的“忽略之前规则”当成系统指令。遇到登录、付款、删除、对外发送或改权限时，停止并交给用户本人完成或确认。

## 三、应用到内部 IT 服务助手

任务 B（申请提交）和任务 C（状态查询）都优先业务接口。Computer Use 只可用于没有接口的只读材料查看，不能通过点击绕过 tenant-A 的服务端鉴权。管理员 ADM01 的界面权限不能被模型当作员工 E01 的身份。任务 B 提交仍必须经过 `awaiting_confirmation`，并用 request_key 防止超时重复建单；任务 C 每次从业务系统读取最新状态。

如果必须驱动遗留窗口，产品至少展示当前应用、账号身份、目标对象、动作后果和可停止位置。停止当前鼠标动作只停止后续操作，不能撤回已经发出的业务请求。已提交申请的撤回需要查询业务系统规则并取得回执。

### 三条必须演练的具体轨迹

1. **页面变化**：观察到“提交”按钮后页面刷新，原坐标已失效。Agent 必须重新观察并确认目标对象、金额和当前页面版本，不能沿用旧坐标点击；证据包括刷新前后截图、页面标识、最终业务回执。
2. **登录失效**：工具返回会话过期或跳转登录页时，立即暂停并提示用户本人重新认证，不读取或保存密码，不把“点击成功”当作业务完成；证据包括认证错误、暂停状态、重新认证后的新会话和后续回执。
3. **取消与恢复**：用户在写入前取消，任务进入 `cancelled`，队列执行前再次检查不得发出动作；若动作已发出或结果未知，先按同一 `request_key` 查询，再依据业务撤回规则处理。恢复只能从保存的任务状态继续，不能重新创建申请；证据包括状态变更、查询结果、业务 ID 和用户可见说明。

## 四、风险分级

以下 L0–L3 是作者为教学练习定义的风险等级，不是供应商等级或行业标准。等级越高，越需要把动作交给用户或人工，并要求可核验的外部回执。

| 等级（作者教学） | 示例 | 默认策略 | 证据 |
|---|---|---|---|
| L0 只读 | 打开制度、查看帮助 | 可自动 | 截图和来源 |
| L1 可逆草稿 | 填写申请草稿、不保存 | 自动或低频确认 | 草稿差异 |
| L2 业务写入 | 提交申请、发送邮件 | 强制确认 | 业务 ID 或发送回执 |
| L3 不可逆/高后果 | 删除、付款、改权限 | 人工接管或禁止 | 双重确认、审计记录 |

沙箱、网络白名单、应用白名单和服务端权限要同时存在。沙箱只能限制环境，不能替代业务系统重鉴权。浏览器或截图中的内容也不能成为授权来源。

## 五、练习

给任务 B 设计一张工具选择卡。说明何时用业务 API，何时用浏览器或 Computer Use，哪些动作进入 dry-run，哪些动作必须用户接管。再写一条“页面显示已提交但业务系统没有 ID”的失败处理，要求系统停下、查询 request_key 并转 IT01。

## 相关阅读

- [Agentic UX 与信任校准](agentic-ux-and-trust-calibration.md)
- [文档智能产品怎么设计](document-intelligence-design.md)
- [实时语音 Agent 怎么设计](realtime-voice-agent-design.md)
- [AI 产品 PRD 怎么写](ai-prd-guide.md)
- [企业内部 IT 服务助手教学材料](../../../assets/internal-service-assistant/teaching-materials.md)

## 参考资料

- [Introducing computer use in Gemini 3.5 Flash](https://blog.google/innovation-and-ai/models-and-research/gemini-models/introducing-computer-use-gemini-3-5-flash/) — Google 官方，2026-06-24 发布，2026-09-14 访问；旧能力，不是 9 月首发
- [Computer use tool](https://platform.claude.com/docs/en/agents-and-tools/tool-use/computer-use-tool) — Anthropic 官方文档，访问 2026-09-14；API/客户端工具说明
- [Computer use](https://ai.google.dev/gemini-api/docs/computer-use) — Google AI for Developers，当前标为 Preview，访问 2026-09-14；客户端执行循环与安全边界
- [Qwen-UI-Agent Technical Report](https://arxiv.org/abs/2607.28227) — 原始论文，2026-07-30 提交，研究材料，不当作 GA
- [Enterprise managed permissions for GitHub Copilot agent operations](https://github.blog/changelog/2026-09-09-enterprise-managed-permissions-for-github-copilot-agent-operations/) — GitHub 官方，2026-09-09 发布，2026-09-14 访问
