---
title: prompt-rag-finetuning
type: note
permalink: aipmwiki/docs/01-ai-basics/llm/prompt-rag-finetuning
---

# Prompt / RAG / 微调:三种让模型更懂你的方式怎么选

大模型出厂时是"通才":不懂你的业务、不知道昨天发的公告、也不一定按你要的格式说话。让模型"更懂你"的手段主要有三种,选错了会白花预算,甚至练出一个"记住错误答案"的模型。

## 一句话对比

- **Prompt**:不改模型,只改"怎么问"。
- **RAG**:不改模型,只改"给它看什么资料",先检索再生成。
- **微调**:改模型本身,用数据继续训练,把行为模式"焊"进权重里。

类比:Prompt 是给应聘者一份工作说明书,RAG 是配一个随时可查的资料库,微调是送他脱产培训、变成肌肉记忆。原理细节见 [Prompt、RAG、SFT 分别是什么?](../../04-interview/basics/prompt-rag-sft-difference.md),本文重点讲**怎么选**。

## 对比表

| 维度 | Prompt 工程 | RAG | 微调 |
|------|------------|-----|------|
| 改变什么 | 输入(指令、示例) | 输入中的知识 | 模型权重 |
| 前期投入 | 几乎为零 | 中等:建库 + 检索链路 | 较高:标注数据 + 训练算力 |
| 迭代速度 | 分钟级 | 小时到天级 | 周级 |
| 数据要求 | 几个好示例即可 | 结构化知识文档 | 几百到几万条高质量样本 |
| 可溯源性 | 高 | 高,可展示引用来源 | 低,行为烙印进权重 |
| 维护负担 | 低,但会随需求膨胀 | 中,需持续维护知识库质量 | 高,模型升级常需重新微调 |
| 擅长解决 | 格式、角色、简单约束 | 私有知识问答、时效性、需溯源 | 固定话术、稳定格式、领域表达习惯 |
| 成本结构 | 每次请求的 token 开销 | token + 建库/检索成本 | 一次性训练成本 + 推理成本 |

## 决策树

```
模型答得不够好
   │
   ▼
是"不知道"还是"知道但做不好"？
   ├─ 不知道 → 知识形态是什么？
   │             ├─ 文档/制度/FAQ,且要稳定可控、可引用 → RAG / 混合检索
   │             ├─ 要看库里的数、跨表指标 → 先想语义层 + NL2SQL,而不是硬塞文档 RAG
   │             ├─ 多步骤、要动态决定查什么(代码/排障/开放域研究) → Agent + 检索工具
   │             └─ 知识很少且几乎不变 → 直接写进 Prompt 即可
   └─ 知道但做不好 → 先把 Prompt 优化到极致
                       └─ 还不行 → 是风格/格式类问题？
                             ├─ 是 → 考虑微调
                             └─ 否(其实是知识/检索问题) → 回头检查 RAG / 工具调用
```

经验法则:**先 Prompt,不够上 RAG(或 NL2SQL),任务变复杂再上 Agent,还不够且预算允许再微调**——试错成本从低到高,永远从最轻的手段开始。演进细节见[《RAG 没死》](rag-evolution-naive-to-agentic.md)与[《NL2SQL》](nl2sql-for-ai-pm.md)。

## 常见组合拳与真实产品例子

- **Prompt + RAG**(最常见):检索到资料后,还需 Prompt 告诉模型"只根据资料回答,没有就说不知道"。典型如企业知识助手(接入 Wiki/工单系统的客服机器人)——制度天天变,微调追不上更新速度,用户还需要"答案出自哪份文档"的可溯源性。
- **微调 + RAG**:微调让模型学会领域说话方式和合规话术,RAG 补充实时更新的知识(法规、最新数据)。
- **纯 Prompt**:很多 AI 写作/翻译工具早期就是"通用模型 + 系统 Prompt",无额外训练也无知识库,成本最低。
- **三者叠加**:企业级编码助手常见架构是"代码风格微调模型 + RAG 检索内部代码库/文档 + 定义安全边界的系统 Prompt"。

## 给 AI PM 的选型建议

1. 问题本质是"不知道"还是"做不好"?——决定走 RAG 还是微调。
2. 知识更新有多快?——越快越该用 RAG,微调维护成本会拖垮团队。
3. 团队有没有持续维护能力?——都没有时,把 Prompt 做到极致往往性价比最高。
4. 要不要给用户看引用来源?——合规、医疗、法律场景,RAG 几乎是唯一选项。

## 相关阅读

- B站:[微调：从原理到实操](https://www.bilibili.com/video/BV13QFxzCEXb)（费曼学徒冬瓜，~6.4 万播放）
- B站:[大模型微调看这个视频就够了](https://www.bilibili.com/video/BV1gmWDeLEMZ)（RethinkFun，~5.9 万播放）
- [什么是 RAG:检索增强生成入门](what-is-rag.md)
- [RAG 没死:死的是 Naive 固定流水线](rag-evolution-naive-to-agentic.md)
- [Agentic Retrieval:检索如何变成 Agent 的工具](agentic-retrieval.md)
- [NL2SQL:自然语言查库,为什么学术高分上不了生产](nl2sql-for-ai-pm.md)
- [面试题:Prompt、RAG、SFT 分别是什么?有什么区别?](../../04-interview/basics/prompt-rag-sft-difference.md)
- [面试题:RAG 和微调有什么区别?什么场景该用哪个?](../../04-interview/basics/rag-vs-finetuning.md)
- [面试题:你怎么看「RAG 已死」这说法?](../../04-interview/basics/is-rag-dead.md)
- [AI 术语速查表](../glossary.md)

## 参考资料

- [Prompt engineering overview](https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/overview) — Anthropic 官方文档,2026-07 访问
- [Fine-tuning guide](https://platform.openai.com/docs/guides/fine-tuning) — OpenAI 官方文档,2026-07 访问
- [Prompt engineering guide](https://platform.openai.com/docs/guides/prompt-engineering) — OpenAI 官方文档,2026-07 访问