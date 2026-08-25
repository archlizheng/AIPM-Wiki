---
title: what-is-rag
type: note
permalink: aipmwiki/docs/01-ai-basics/llm/what-is-rag
---

# 什么是 RAG:检索增强生成入门

## 一句话解释

RAG(Retrieval-Augmented Generation,检索增强生成)= 先从你的知识库里**检索**出相关内容,再把它们和用户问题一起交给大模型**生成**答案。相当于让模型"开卷考试"而不是"闭卷硬答"。

## 为什么需要 RAG

大模型有三个天然缺陷:

1. **知识过时**:训练数据有截止日期,不知道昨天发生了什么
2. **不懂私有知识**:你公司的产品文档、内部制度,模型从没见过
3. **幻觉**:不知道的问题也会一本正经地编

RAG 一次性缓解这三个问题:答案基于检索到的真实材料生成,还可以附上引用来源。

## 工作流程

下面是最常见的入门形态(有时叫 Naive RAG):固定切块、固定 Top-K、再拼进 Prompt。它适合理解原理,但**不等于生产级 RAG 的全部**——混合检索、重排、以及由 Agent 动态决定「查什么」的演进,见[《RAG 没死:死的是 Naive 固定流水线》](rag-evolution-naive-to-agentic.md)。

```
用户提问
   ↓
① 把问题转成向量(Embedding)
   ↓
② 在向量数据库中检索最相关的文档片段(通常取 top 3-10 段)
   ↓
③ 把「文档片段 + 用户问题」拼成 Prompt 交给大模型
   ↓
④ 模型基于给定材料生成答案(可附引用)
```

## PM 需要关心的设计决策

| 决策点 | 说明 |
|--------|------|
| 文档切片策略 | 切太碎丢上下文,切太大检索不准——直接影响答案质量 |
| 检索不到怎么办 | 是老实说"不知道",还是让模型自由发挥?前者体验差,后者有幻觉风险 |
| 引用展示 | 是否展示来源、怎么展示,是建立用户信任的关键产品设计 |
| 知识库更新机制 | 文档变更后多快生效?谁来维护知识库质量? |
| 评估方法 | 检索准不准(召回率)和答得好不好(答案质量)要分开评估 |

## 典型面试追问

- RAG 和微调的区别?什么场景选哪个?(参考:知识频繁更新、需要溯源 → RAG;改变模型说话风格和格式 → 微调;两者不互斥)
- 用户反馈"答非所问",你怎么排查?(拆解:是检索环节没找到对的材料,还是生成环节没用好材料)

## 相关阅读

- B站:[RAG 工作机制详解](https://www.bilibili.com/video/BV1JLN2z4EZQ)（马克的技术工作坊，~33 万播放）— 知识库全流程
- B站:[RAG、Memory 与向量检索原理](https://www.bilibili.com/video/BV1RCGR6yEEw)（小白debug，~6.5 万播放）
- B站:[向量数据库技术鉴赏（上）](https://www.bilibili.com/video/BV11a4y1c7SW)（Ele实验室，~28 万播放）
- [Context Engineering：从写 Prompt 到拼上下文系统](../prompt-engineering/context-engineering.md)
- [Prompt、RAG、微调:三种让模型「更懂你」的手段](prompt-rag-finetuning.md)
- [RAG 没死:死的是 Naive 固定流水线](rag-evolution-naive-to-agentic.md)
- [长上下文 vs RAG:什么时候塞全库,什么时候检索](long-context-vs-rag.md)
- [Agentic Retrieval:检索如何变成 Agent 的工具](agentic-retrieval.md)
- [工业级 RAG 清单:混合检索、重排、评测与知识治理](production-rag-checklist.md)
- [NL2SQL:自然语言查库,为什么学术高分上不了生产](nl2sql-for-ai-pm.md)
- [AI 术语速查表](../glossary.md)
- 面试题:[你怎么看「RAG 已死」这说法?](../../04-interview/basics/is-rag-dead.md)
- 面试题:[04-interview/basics](../../04-interview/basics/README.md)