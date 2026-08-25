---
title: transformer-architecture
type: note
permalink: aipmwiki/docs/01-ai-basics/llm/transformer-architecture
---

# Transformer 架构:大模型的地基

## 一句话说清

今天几乎所有主流大语言模型(GPT、Claude、Gemini、DeepSeek……)的底层骨架,都是 **Transformer** 或其变体。你不需要会推公式,但需要知道它解决了什么问题、核心机制(注意力)在干什么、以及这对**上下文窗口、成本、长文本能力**意味着什么。

> 配图来自 Jay Alammar 的经典可视化文章 [The Illustrated Transformer](https://jalammar.github.io/illustrated-transformer/)(CC BY-NC-SA 4.0),与本仓库协议一致。

## 先把它当成一个黑盒

Transformer 最初是为**机器翻译**设计的:左边进一种语言,右边出另一种语言。

![Transformer 黑盒示意:输入法语句子,输出英语翻译](assets/transformer-architecture.png)

*图:把 Transformer 先看成「输入序列 → 输出序列」的转换器。来源:[Jay Alammar](https://jalammar.github.io/illustrated-transformer/)*

结合[大模型是怎么"想"的](how-llm-works.md)一文:训练目标是「预测下一个 token」;而 **Transformer 是实现这个目标的计算架构**——决定了模型如何把一长串 token「看」在一起、再一个个吐出后续 token。

## 编码器 + 解码器:原版长什么样

原版 Transformer 分成两半:


| 半边               | 干什么                | 类比       |
| ---------------- | ------------------ | -------- |
| **编码器(Encoder)** | 把输入整段读完,压成「理解后的表示」 | 先把整段原文读懂 |
| **解码器(Decoder)** | 一边看编码器的结果,一边逐词生成输出 | 再一句句写出译文 |


![编码器接收输入、解码器生成输出](assets/transformer-overview.png)

*图:左侧 Encoders 吃进输入,右侧 Decoders 吐出输出。来源:[Jay Alammar](https://jalammar.github.io/illustrated-transformer/)*

实际实现里,两边都是**多层堆叠**的(原论文是 6 层编码器 + 6 层解码器)。每一层做相似的事,但越往上越能抓住更抽象的关系。

![六层编码器与六层解码器堆叠](assets/transformer-stack.png)

*图:多层 Encoder / Decoder 堆叠;解码器每一层都能「看」到编码器的最终表示。来源:[Jay Alammar](https://jalammar.github.io/illustrated-transformer/)*

**PM 要记住的一点**:ChatGPT 一类对话模型,主流是 **Decoder-only**(只有解码器一侧)。它们把「用户输入 + 已生成内容」都当成同一条序列来续写,不再单独跑一个翻译式的编码器。BERT 一类偏「理解/分类」的模型则常是 **Encoder-only**。原版 Encoder-Decoder 仍广泛用于翻译、摘要等「输入一整段、输出另一整段」的任务。

## 核心机制:自注意力(Self-Attention)

Transformer 相对 RNN/LSTM 的关键突破,是 **注意力机制**:处理某个词时,可以**直接、并行地**参考句子里其他位置的词,而不必像 RNN 那样必须从左到右一个个「传」过去。

最直观的例子是代词指代——模型在处理 `it` 时,注意力会更多地落在真正的先行词上:

![自注意力可视化:it 指向 the animal](assets/self-attention-viz.png)

*图:处理* `it` *时,注意力权重集中在* `The animal` *上——模型在学「它指的是谁」。来源:[Jay Alammar](https://jalammar.github.io/illustrated-transformer/),可视化风格类似 BertViz*

可以把它想成:**每个词在问「我该多看谁一眼?」**,再按权重把相关词的信息揉进自己的表示里。这就是「上下文理解」在架构层面的来源之一。

### 多头注意力:多双眼睛同时看

实际模型不会只用一组注意力,而是并行跑多组(多个 **head**),再拼起来——不同的头可以分别关注语法、指代、局部搭配等不同模式。

![多头注意力:多组 Q/K/V 并行计算再拼接](assets/multi-head-attention.png)

*图:输入经 Embedding 后,拆成多个 head 分别算注意力,再拼接成最终表示。来源:[Jay Alammar](https://jalammar.github.io/illustrated-transformer/)*

面试里若被问到「多头注意力是干什么的」,用 PM 语言答即可:**同一句话用多组「关注方式」并行看一遍,再综合,比单组看得更全**——不必背矩阵公式。商汤等公司的面经里,这类题常作为技术边界探测(见[商汤 AI PM 面经](../../04-interview/experiences/sensetime-ai-pm-202607.md))。

## 为什么这件事改变了产品世界

相对 RNN/LSTM,Transformer 带来两个产品级后果:

1. **训练可大规模并行**:注意力对序列位置可并行计算,GPU/TPU 吃得下,才撑得起今天这种「海量数据 + 超大参数」的预训练。
2. **长距离依赖更容易建模**:任意两个位置可以直接建立联系,长文本里前后呼应、指代、跨段推理比 RNN 时代靠谱得多——这也是[上下文窗口](context-window.md)能一路做大的架构前提(窗口大小还受显存、位置编码、工程实现等约束,不是「有注意力就无限长」)。

历史脉络见[深度学习极简史](../machine-learning/deep-learning-brief-history.md)中「2017:Transformer」一节。

## 给 AI PM 的启示

- **架构决定能力边界的形状**:上下文多长、长文是否「后半段变傻」、多轮对话如何截断,背后都和注意力的计算方式与窗口设计有关。
- **成本随序列长度涨得很快**:自注意力的计算量大致随序列长度平方增长(工程上有各种稀疏/线性注意力变体在缓解)。产品上这意味着:无脑把整库文档塞进提示词,往往既贵又慢——更合理的是[RAG](what-is-rag.md)、摘要、分层检索。
- **「懂原理」到什么程度够用**:能讲清「注意力 = 动态决定看哪些词」「GPT 类是 Decoder-only」「窗口与成本的关系」即可;推导 Softmax(QKᵀ)V 不是 PM 面试的常规要求。



## 相关阅读

- B站:[直观解释注意力机制](https://www.bilibili.com/video/BV1TZ421j7Ke)（3Blue1Brown 官方中配，~87 万播放）
- B站:[一小时从函数到 Transformer](https://www.bilibili.com/video/BV1NCgVzoEG9)（飞天闪客，~125 万播放）
- B站:[20 分钟读懂 Attention Is All You Need](https://www.bilibili.com/video/BV1dyW9zsEk1)（李自然说，~44 万播放）
- [大模型是怎么"想"的:预测下一个 token 的直观解释](how-llm-works.md)
- [上下文窗口:限制、成本与产品设计影响](context-window.md)
- [什么是 RAG:检索增强生成入门](what-is-rag.md)
- [深度学习与神经网络:从感知机到 Transformer 的极简史](../machine-learning/deep-learning-brief-history.md)
- [AI 术语速查表](../glossary.md)



## 参考资料

- [Attention Is All You Need](https://arxiv.org/abs/1706.03762) — Vaswani et al.,2017(原论文)
- [The Illustrated Transformer](https://jalammar.github.io/illustrated-transformer/) — Jay Alammar(配图来源,CC BY-NC-SA 4.0)
- [But what is a GPT? Visual intro to Transformers](https://www.3blue1brown.com/lessons/gpt/) — 3Blue1Brown
- [The Illustrated GPT-2](https://jalammar.github.io/illustrated-gpt2/) — Jay Alammar(Decoder-only 视角补充)