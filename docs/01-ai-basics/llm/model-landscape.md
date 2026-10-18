---
title: model-landscape
type: note
permalink: aipmwiki/docs/01-ai-basics/llm/model-landscape
---

# 主流大模型盘点与选型思路（动态版）

> **信息状态**：动态核验页｜**最后核验**：2026-09-14（Asia/Shanghai）

模型名、别名、价格、上下文上限和退役日期都可能改变。本文保留能在官方目录或定价页直接核对的字段；没有稳定官方字段的地方只给入口和核验方法，不把第三方榜单或旧快照当作当前事实。

## 先看“服务对象”，再看型号

产品实际购买的是“厂商 + 区域/部署 + 模型 ID + API 版本 + 限流/计费规则”这一组服务对象。一个别名可能指向新快照，两个平台也可能对同名模型提供不同的上下文、工具和数据处理条件。因此评审表至少记录：

| 字段 | 记录方式 | 为什么必须记录 |
|---|---|---|
| 模型 ID 与别名 | 复制官方 API ID，注明是否为 latest/alias | 便于回滚和复现实验 |
| 生命周期 | GA、预览、Beta、deprecated、retired | 预览能力不等于生产承诺 |
| 输入/输出与缓存价 | 标明币种、单位、阶梯、批处理 | 单价不能脱离 token 构成 |
| 上下文与最大输出 | 从官方模型目录/模型 API 读取 | 窗口和输出上限是两个字段 |
| 工具与区域 | 记录工具、地域、数据处理条款 | Agent 可用性由接口与合规共同决定 |

## 2026-09-14 可直接核验的官方入口

下表只列官方页面明确展示的例子，目的是教你如何读目录，不是给出永久排名。价格均为官方页面展示的每百万 token 参考值；调用前应重新打开链接并保存当日快照。

| 厂商 | 代表性官方快照（模型 ID） | 上下文 / 最大输出 | 价格与生命周期（访问日页面） |
|---|---|---|---|
| OpenAI | GPT-6 Astra (`gpt-6-astra`)、GPT-5.6 Sol (`gpt-5.6-sol`)、GPT-5.6 Terra (`gpt-5.6-terra`)、GPT-5.6 Luna (`gpt-5.6-luna`) | 均为 1.05M / 128K | Astra $10/$50、Sol $4/$20、Terra $2/$12、Luna $0.20/$1.20（输入/输出，美元/百万 token）；官方模型目录当前列为可选模型 |
| Anthropic | Claude Fable 5.1 (`claude-fable-5-1`)、Claude Opus 5 (`claude-opus-5`)、Claude Sonnet 5 (`claude-sonnet-5`)、Claude Haiku 4.5 (`claude-haiku-4-5-20251001`) | Fable/Opus/Sonnet 1M / 128K；Haiku 200K / 64K | 官方总览列输入/输出 $10/$50、$5/$25、$2/$10、$1/$5；状态为当前总览项，仍需按退役页核验 |
| Google Gemini API | Gemini 3.8 Flash (`gemini-3.8-flash`，稳定)；Gemini 3.1 Pro Preview (`gemini-3.1-pro-preview`，预览) | 均为 1,048,576 / 65,536 | 模型页直接给出 ID、上下文、输出与能力；价格和区域以定价页逐模型重核，Pro Preview 不能当作 GA |
| DeepSeek API | 以官方 `models`/pricing 页面返回的模型 ID 为准 | 页面列出的版本字段 | 按官方定价页核对版本与缓存；若存在时段优惠，再核其适用和生效时间；本次未将静态抓取不稳定页面中的数字抄入表格 |
| 阿里云 Model Studio | Qwen3.7 Max (`qwen3.7-max`，当前别名)；Qwen3 Max (`qwen3-max`，当前别名)；Qwen Long (`qwen-long`) | Qwen3.7 Max 1M；Qwen3 Max 256K；Qwen Long 以模型页为准 | 中国大陆实时输入/输出：Qwen3.7 Max ¥12/¥36（≤1M）；Qwen3 Max 按 32K/128K/256K 阶梯 ¥2.5/¥10、¥4/¥16、¥7/¥28；价格按页面和地域重核 |
| Moonshot AI / Kimi | Kimi K3 (`kimi-k3`，开放权重与 API 页面可见) | 1M / 最大输出以 API 模型页为准 | 2026-07-27 官方开放日发布权重与技术报告；API 入口展示 $0.30 缓存命中、$3 输入、$15 输出/百万 token；开放权重不等于托管 API 的可用性或 SLA |

**事实**：OpenAI、Anthropic、Google、DeepSeek、阿里云和 Moonshot AI 都提供官方模型目录或 API 入口；Google 的 Models API 还提供 `models.list`/`models.get`，可读取支持的动作和 token 限制。上表只抽取能在官方模型页直接定位的代表项；Qwen、Kimi 的价格仍受地域、模式、阶梯和缓存规则影响，不能用作跨厂商报价比较。

**产品推论**：模型表不应只放“最强/最便宜”标签。采购评审应先冻结模型 ID、状态、区域和价格版本，再用同一组业务样本测质量、延迟、失败率和总成本；当任一字段改变时触发回归，而不是自动切换别名。

## 选型方法：用档位和约束做决策

### 1. 能力：让真实任务胜过榜单

准备几十条脱敏业务样本，覆盖正常、边界、拒答、工具失败和长上下文场景。每次记录模型 ID、提示版本、工具版本和人工评分。把“看起来会推理”拆成可验收的字段，例如 JSON 合法率、引用正确率、工具参数准确率和任务完成率。

### 2. 成本：按一次任务而不是一次请求估算

```text
单任务成本
= 未命中缓存的输入 token × 适用输入单价
+ 输出 token（含计费的思考 token）× 输出单价
+ 缓存写入/命中 token × 各自适用单价（避免与普通输入重复计费）
+ 工具、检索、运行时和重试成本
```

对长任务再记录上下文重放、摘要/压缩和失败重试次数。不同厂商的缓存、批处理、阶梯价格和工具费不能混成一个“每百万 token 单价”。

### 3. 延迟与可靠性：用分位数和降级链

在线对话关注首 token、完整响应和 P95/P99；工具型 Agent 关注从计划到完成的总耗时、重试率和人工接管率。预先写好超时、重试、降级到轻量模型和人工接管条件，避免把“模型答得像样”误当成服务可用。

### 4. 合规与替换成本：把区域和许可写进门槛

检查数据驻留、训练使用政策、保留期、审计日志、私有化/专属部署、开源许可和供应商退出路径。国产接口的地域与合规优势也要按实际合同、部署和数据类型确认，不能从“国产”二字推导出自动合规。

## 一个可复用的评审表

| 维度 | 问题 | 证据/阈值 | 结论 |
|---|---|---|---|
| 质量 | 30 条验收样本能否完成关键动作？ | 任务完成率、字段准确率 | 通过/补测 |
| 成本 | 每个请求的真实 token 构成是什么？ | 计费账单 + token 日志 | 元/任务 |
| 可靠性 | 超时、工具失败、限流如何恢复？ | 线上 trace、P95、重试率 | 是否可上线 |
| 合规 | 何种数据可发往何处？ | 合同、DPA、区域配置 | 允许的数据级别 |
| 可替换 | 换一个供应商需要改什么？ | 适配层、结构化输出、回归集 | 人日/风险 |

## 常见误区

- 把“上下文窗口大”当作“长文档一定答得好”；有效检索、排序、压缩和引用仍要评测。
- 把预览、Beta 或参考架构当作 GA；状态必须来自官方生命周期页或产品公告。
- 把某日价格复制成长期事实；价格表要记录访问日、生效日、币种、区域和计费模式。
- 把“开源”当作一个二元标签；开放权重、许可、商用条件、自部署成本和托管 API 是不同问题。

## 练习：为内部 IT 服务助手做一次选型

使用教学契约中的 E01/M01/IT01/ADM01、租户 A/B 和任务 A/B/C 建立 30 条评测样本：任务 A 是制度问答，任务 B 是员工确认后的受控申请提交，任务 C 是授权状态查询。可先读[内部服务需求发现实践](../../02-pm-skills/practice/internal-service-discovery.md)、[内部服务助手 PRD](../../02-pm-skills/practice/internal-service-prd.md)和[内部服务采用评估实践](../../02-pm-skills/practice/internal-service-adoption.md)，再冻结三档模型的官方 ID 和价格版本，测试分类是否正确、是否遵守“提交≠批准≠履行”、是否只保存明确允许的界面语言、`request_key` 重试是否幂等。最后用同一输入/输出分布计算元/任务，并写出升级到旗舰模型、降级到轻量模型和转人工的边界。教学中的成本数字只用于算术练习，不是线上账单。

## 相关阅读

- [上下文窗口：限制、成本与产品设计影响](context-window.md)
- [Context Engineering：从写 Prompt 到拼上下文系统](../prompt-engineering/context-engineering.md)
- [Agent 入门：规划、记忆、工具调用](agent-basics.md)
- [Agent 平台选型与治理](../../02-pm-skills/cost-and-tech/agent-platform-selection-and-governance.md)
- [Agent 评测与可观测性](../../02-pm-skills/ai-product-operations/agent-evaluation-and-observability.md)

## 官方资料（访问 2026-09-14）

- [OpenAI Models](https://developers.openai.com/api/docs/models)；[OpenAI Compare models](https://developers.openai.com/api/docs/models/compare)；[OpenAI API Pricing](https://developers.openai.com/api/docs/pricing)
- [Anthropic Models overview](https://platform.claude.com/docs/en/models/overview)；[Model deprecations](https://platform.claude.com/docs/en/about-claude/model-deprecations)；[Pricing](https://platform.claude.com/docs/en/about-claude/pricing)
- [Gemini API Models API](https://ai.google.dev/api/models)；[Gemini API Pricing](https://ai.google.dev/gemini-api/docs/pricing)
- [DeepSeek Models & Pricing](https://api-docs.deepseek.com/quick_start/pricing)
- [Alibaba Cloud Model Studio model pricing](https://help.aliyun.com/en/model-studio/model-pricing)
- [Kimi K3 Open Day](https://www.kimi.com/news/kimi-k3-open-source)；[Kimi API Platform](https://platform.kimi.ai/)
