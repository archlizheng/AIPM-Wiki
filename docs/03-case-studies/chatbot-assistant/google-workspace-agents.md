---
title: google-workspace-agents
type: note
permalink: aipmwiki/docs/03-case-studies/chatbot-assistant/google-workspace-agents
---

# Google Workspace Agent 案例拆解

**分类**：对话助手
**资料核验时间**：2026 年 9 月

本文依据 Google Workspace 官方产品公告整理，不代表作者实际使用。重点观察 Gemini 如何从当前应用承接任务，怎样读取用户选择或管理员允许的来源，以及交付物何时仍需要人审核。

## 一、产品背景

2026 年 9 月 9 日，Google Workspace 官方介绍了五种跨应用能力：从 Chat 生成 Slides、从 Drive 建立 Sheets、在 Docs 中草拟邮件、把 Gmail 长线程整理成可编辑 Brief，以及把 Docs 提案转换成可编辑品牌 Deck。公告称相关能力正在向符合计划的客户 rolling out；其中 Ask Gemini in Chat 标为 coming in the coming weeks，不能写成当前所有账号都能从 Chat 发起。

这里的产品单位已经从“回答一条消息”变成“在原应用里完成一段工作”。Gemini 读取的文件、邮件和聊天来源由用户选择或管理员启用。这个权限入口决定了答案能看到什么，也决定了企业能否接受它进入日常流程。

## 二、核心功能拆解

| 入口 | 交付物 | 来源 | 公告状态 | 人的动作 |
|---|---|---|---|---|
| Chat | 多页 Slides 草稿 | 项目、Chat、Gmail、任务项 | Ask Gemini in Chat：coming in the coming weeks；具体生成能力按账号核验 | 打开 Slides 继续编辑 |
| Drive | 结构化 Sheets | 当前文件夹和选定外部数据 | 公告列为 rolling out；Ask Gemini in Drive 可用性按账号核验 | 检查字段和公式 |
| Docs | 语境邮件 | 当前文档及允许的项目资料 | 公告列为 rolling out；发送前仍需用户审核正文、添加收件人 | 用户审核正文、添加收件人后再发送 |
| Gmail | 结构化 Brief | 当前线程、相关 Drive、邮件和 Chat | 公告列为 rolling out；具体入口和计划按账号核验 | 检查目标与下一步 |
| Docs | 品牌 Deck | 当前提案、品牌规范和模板 | 公告列为 rolling out；具体入口和计划按账号核验 | 审阅版式和事实 |

这些场景的共同流程是选择来源、生成可编辑文件、保留用户在原应用的工作位置。官方还提到日程、任务和 Workspace Studio 流程会继续扩展；表中 rolling out 与 coming in the coming weeks 都不能改写成所有账号当前 GA。

## 三、技术方案推测

官方确认的是 Workspace Intelligence、应用集成、来源选择和生成文件。内部检索、上下文拼装、模型路由和模板渲染方式没有完整公开。可以合理推测它包含来源检索、结构化生成和文件渲染，但不能把推测写成 Google 的内部架构事实。

## 四、商业模式与开放范围

公告列出的试用范围包括 Business Standard、Business Plus、Enterprise Standard、Enterprise Plus、Google AI Pro、Google AI Ultra 个人计划，以及 Google AI Pro for Education。产品是分批滚动开放，能力和地区要以账号页面为准。没有资料证明所有五种能力在所有计划同时可用，也没有资料证明用户可以让 Agent 自动发送任何邮件而跳过正文审核、添加收件人和发送动作。

## 五、PM 视角的启示

第一，来源选择应成为主流程控件。用户需要知道 Agent 看了哪些文件，管理员需要能限制哪些数据域。

第二，交付物比聊天文本更容易验收。可编辑文件保留了人工修改、版本和继续协作的空间，但也带来公式、引用、版式和收件人检查。

第三，跨应用动作要分草稿和写入。生成 Slides 或 Brief 可以先交付草稿，邮件发送和日程变更则应展示对象、范围和后果，保留用户审核。

## 六、教学演算

在内部 IT 服务场景中，Gmail Brief 只能借用“来源选择、证据保留、可编辑产物和人工审核”的设计边界，不能说它就是教学契约中的任务 A。若另行设计“生成申请表”，才可映射到任务 B 草稿：文档生成后 E01 仍要预览确认，提交只产生 `pending_approval`，M01 在业务系统审批，IT01 再人工登记交付。这个映射是虚构练习，不是 Google Workspace 的实际集成证明。

## 相关阅读

- [文档智能产品怎么设计](../../02-pm-skills/prd-and-design/document-intelligence-design.md)
- [AI 产品 PRD 怎么写](../../02-pm-skills/prd-and-design/ai-prd-guide.md)
- [通用助手的定位对比](assistant-positioning-comparison.md)
- [企业内部 IT 服务助手教学材料](../../../assets/internal-service-assistant/teaching-materials.md)

## 参考资料

- [Less switching, more flow: 5 new agentic capabilities across Google Workspace apps](https://workspace.google.com/blog/product-announcements/less-switching-more-flow-5-new-agentic-capabilities-across-google-workspace-apps) — Google Workspace 官方，2026-09-09 发布，2026-09-14 访问
