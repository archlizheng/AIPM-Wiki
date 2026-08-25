---
title: model-landscape
type: note
permalink: aipmwiki/docs/01-ai-basics/llm/model-landscape
---

# 主流大模型盘点与选型思路(2026 年中)

> **信息状态**：动态快照｜**最后核验**：2026-07

> **数据截至 2026 年 7 月**,来自 Anthropic、OpenAI、Google、DeepSeek、阿里云等官方文档。大模型行业发布节奏是"数周级别",请把本文当作一次快照,实际选型前务必去官方页面核对最新数字(见文末链接)。

"我们该用哪个模型"常没有标准答案——**没有最好的模型,只有最合适的模型**。本文先给一份 2026 年 7 月的盘点,再给一套不会过时的选型方法。

## 三大闭源旗舰对比

| 厂商 | 型号 | 定位 | 上下文窗口 | 参考价格(输入/输出,每百万 token) |
|------|------|------|-----------|--------------------------------|
| Anthropic | Claude Fable 5 | 最强旗舰,面向长时间自主运行的 Agent | 1M tokens | $10 / $50 |
| Anthropic | Claude Opus 4.8 | 复杂编程与企业级任务主力 | 1M tokens | $5 / $25 |
| Anthropic | Claude Sonnet 5 | 速度与智能平衡,默认选择 | 1M tokens | $2/$10(2026-08-31 前引导价,后为 $3/$15) |
| Anthropic | Claude Haiku 4.5 | 最快,近旗舰智能 | 200K tokens | $1 / $5 |
| OpenAI | GPT-5.5 | 旗舰通用模型 | 1M tokens(API) | $5 / $30 |
| OpenAI | GPT-5.4 | 主力生产型号 | 1M tokens | $2.50 / $15 |
| OpenAI | GPT-5.4-mini | 高性价比 | 400K tokens | $0.75 / $4.50 |
| Google | Gemini 3.5 Flash | 官方标注最智能的稳定旗舰(注意命名含 Flash) | 1M tokens | $1.50 / $9 |
| Google | Gemini 3.1 Pro | 复杂推理/超长文档 | 最高 1M tokens(模型卡口径) | $2/$12(≤20万 token)、$4/$18(超出);注:API 侧截至 2026-07 仍以 -preview 端点提供,适用 Pre-GA 条款 |
| Google | Gemini 3 Flash(预览) | 高性价比全能 | 1M tokens | $0.50 / $3 |

提醒:Opus 4.7 起使用新分词器,相同文本产生的 token 数比旧版多约 30%,换算成本要留意;Gemini 3.5 Flash 虽名字带"Flash",却是官方定位的综合最强稳定旗舰,选型不能只看名字。

## 开源与中国模型对比

| 模型 | 厂商 | 定位 | 上下文窗口 | 价格 / 开放方式 |
|------|------|------|-----------|-----------------|
| DeepSeek V4 Pro | DeepSeek | 开源旗舰 MoE(1.6T 总参数/49B 激活) | 1M tokens 输入,384K 输出 | $0.435 / $0.87(每百万 token) |
| DeepSeek V4 Flash | DeepSeek | 轻量版,同架构 | 1M tokens | $0.14 / $0.28 |
| Qwen3.7-Max | 阿里云 | 闭源 API 旗舰,强 Agent/编程能力 | 1M tokens | 列表价 $2.50 / $7.50(常有促销) |
| Qwen3.7-Plus | 阿里云 | 多模态,高性价比 | 1M tokens(含 256K 思维链预算) | 约 $0.4 / $1.6 |
| Llama 4 Scout/Maverick | Meta | 开源可商用 MoE,原生多模态 | Scout 最高 10M tokens | 免费下载自部署,成本取决于托管方案 |
| Llama 5 | Meta | 最新旗舰,600B+ 参数 | — | 自定义社区许可,非 OSI 认证开源 |
| Kimi K2.6 | Moonshot(月之暗面) | 中国开源旗舰 MoE(1T 总参数/32B 激活) | 256K tokens | 开放权重(Modified MIT),价格约为同水平闭源模型的 1/5 |

要点:①"开源"程度差异很大,同一家公司的旗舰不一定开源(Qwen3.7-Max 闭源、Llama 5 用限制性许可),选型前需确认许可条款;②中国模型价格优势明显,常是同能力档位海外模型的几分之一到十几分之一;③标称的超长上下文(如 10M)未必等于"有效可用"的上下文,不是唯一指标。

## 选型维度

- **能力**:不要只看榜单排名(存在被针对性优化的风险),用自己的真实业务场景建小型评测集更可靠。
- **成本**:真实成本 = 单价 × 单次消耗量 × 频率。输出 token 通常比输入贵 5-6 倍,思考过程也计费;提示词缓存、批处理能显著降本;开放权重模型可自建推理,把"按 token 付费"换成"按算力付费"。
- **延迟**:同厂商通常有旗舰/轻量两套体系,实时对话产品延迟权重高于离线批处理任务。
- **生态**:是否有成熟 SDK/Agent 框架、是否支持 MCP、能否多云部署避免锁定;中文场景下国产模型在语感和本土知识上通常有优势。
- **合规**:数据能否留境内、能否私有化部署(涉密/金融场景常是唯一选项)、供应链/地缘政治风险、开源许可条款(是否允许商用、有无规模上限)。

## "没有最好只有最合适"的方法论

1. 用"旗舰/中端/轻量"档位而非具体型号名字做决策框架——名字每隔几周就变,分层逻辑是稳定的。
2. 建自己的评测集,几十条真实案例配人工评分,比任何公开榜单更能反映"适不适合我们"。
3. 算总拥有成本,把工程集成、多模型路由、私有化运维都计入,不能只看 API 单价表。
4. 考虑多模型路由:简单任务给便宜快速的轻量模型,复杂任务给旗舰模型。
5. 避免单一厂商锁定,保留架构上的可替换性。
6. 定期回看官方页面——价格和上下文窗口随时会变,决策前必核对最新数字,不要依赖包括本文在内的过时资料。

## 相关阅读

- [什么是 RAG:检索增强生成入门](what-is-rag.md)
- [Prompt / RAG / 微调:三种让模型更懂你的方式怎么选](prompt-rag-finetuning.md)
- [Agent 入门:规划、记忆、工具调用](agent-basics.md)
- [AI 术语速查表](../glossary.md)

## 参考资料

- [Pricing - Claude Platform Docs](https://platform.claude.com/docs/en/about-claude/pricing) — Anthropic 官方文档,2026-07 访问
- [Models overview - Claude Platform Docs](https://platform.claude.com/docs/en/about-claude/models/overview) — Anthropic 官方文档,2026-07 访问
- [Pricing | OpenAI API](https://developers.openai.com/api/docs/pricing) — OpenAI 官方文档,2026-07 访问
- [Models | Gemini API | Google AI for Developers](https://ai.google.dev/gemini-api/docs/models) — Google 官方文档,2026-07 访问
- [Gemini API 官方定价页](https://ai.google.dev/gemini-api/docs/pricing) — Google 官方文档,2026-07 访问
- [Models & Pricing | DeepSeek API Docs](https://api-docs.deepseek.com/quick_start/pricing) — DeepSeek 官方文档,2026-07 访问
- [Alibaba Cloud Model Studio model pricing](https://www.alibabacloud.com/help/en/model-studio/model-pricing) — 阿里云官方文档,2026-07 访问
- [The Llama 4 herd](https://ai.meta.com/blog/llama-4-multimodal-intelligence/) — Meta 官方博客,2026-07 访问
- [moonshotai/Kimi-K2.6 · Hugging Face](https://huggingface.co/moonshotai/Kimi-K2.6) — Moonshot AI 官方模型页,2026-07 访问