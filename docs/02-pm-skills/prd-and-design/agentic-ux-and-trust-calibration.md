---
title: agentic-ux-and-trust-calibration
type: note
permalink: aipmwiki/docs/02-pm-skills/prd-and-design/agentic-ux-and-trust-calibration
---

# Agentic UX 与信任校准：用户何时该信、何时该介入

> **选题启发**：磊叔《关于 AI 产品经理的 100 个问题》Q15、Q33–35、Q51（仅选题结构，正文为原创）。
>
> **和既有文章的分工**：兜底控件与对话范式见 [兜底、反馈与预期管理](fallback-and-feedback-design.md)；Agent 任务卡、状态机、权限链见 [Agent 产品生产化](../ai-product-operations/agent-product-design.md)；黑箱对信任的影响见面试题 [AI 产品的黑箱问题](../../04-interview/basics/ai-blackbox-trust.md)。本文聚焦 **Agent 能改世界状态时**，交互层如何让用户把信任校准到「该信的时候信、该停的时候停」。

## 一、先分清：GUI、对话式 AI、Agentic UX

传统 GUI 把能力拆成按钮与表单；对话式 AI 主要产出**建议与文本**；Agentic UX 则允许系统在多步任务中**自主规划并调用工具**，可能改写外部状态（发邮件、下单、改配置、操作浏览器）。

因此，Agent 产品的核心体验问题不再是「回答好不好看」，而是：

1. 用户是否知道 Agent **正在做什么、做到哪一步**？
2. 有副作用的动作，是否在**不可逆前**被拦住？
3. 用户的信任程度是否与系统**真实能力**大致对齐？

人因工程里把后一点叫 **信任校准（trust calibration）**：信任过高（overtrust）容易误用、漏审；信任过低（undertrust）容易弃用、反复回人工。经典表述见 Parasuraman & Riley（1997）对 automation misuse / disuse 的区分，以及 Lee & See（2004）对 trust in automation 的综述（文末参考资料）。

> **事实**：公开 HCI / Human Factors 文献把 overtrust、undertrust 定义为信任与系统实际能力错位的两端，并强调目标是 *calibrated trust*，而不是「尽量让用户更相信 AI」。
>
> **建议**：AI PM 写需求时，默认目标应是「校准」，不是「拉高 NPS 式信任分数」。

## 二、Agentic UX vs 传统 GUI：对照表

| 维度 | 传统 GUI | Agentic UX |
|------|----------|------------|
| 控制方式 | 用户逐步点选，系统被动响应 | 用户给目标，系统规划并执行多步动作 |
| 状态可见性 | 页面/表单即状态，路径相对固定 | 需要显式展示计划、当前步骤、工具调用与中间结果 |
| 错误形态 | 校验失败、操作失败，通常即时、局部 | 规划错误、工具选错、中间状态污染、外部副作用已发生 |
| 确认时机 | 关键写操作前弹窗（删/付） | 按**副作用与可逆性**分级：只读可自动，不可逆必须确认 |
| 撤销 | Undo / 回滚常有明确入口 | 可逆动作要暴露撤销；不可逆动作只能靠事前确认 + 事后审计 |
| 进度与打断 | 进度条、取消多见于长任务 | 长时 Agent 需要可暂停、可接管、可拿部分结果 |
| 责任归因 | 「用户点了确认」边界相对清晰 | 需区分：模型误判 / 工具失败 / 权限过宽 / 用户过度授权 |

**产品含义**：把 Agent 当成「更快的按钮」会漏掉进度可见与确认分级；把 Agent 做成「全自动黑箱」又会推高 overtrust。可落地的折中是：**自主做低风险步骤，在高后果边界停下来等人**。

## 三、主流产品公开可见的 UX 模式（确认 / 可撤销 / 进度）

以下只归纳厂商**公开文档与产品说明**中的模式，不作未公开可用性实验结论。

| 模式 | 公开做法（示例） | PM 可抄的原则 |
|------|------------------|---------------|
| **后果前确认** | OpenAI 将 ChatGPT agent / Operator 描述为：对有外部副作用的动作（如下单、发邮件）会暂停并请求用户确认；敏感站点可要求「Watch mode」式监督 | 确认卡片要展示：动作、对象、数据范围、是否可撤销、拒绝/修改入口 |
| **随时打断与接管** | ChatGPT agent 公开说明用户可随时 interrupt、接管浏览器或停止任务，并拿到部分进度 | 长任务必须有 Stop / Pause / Take over，且停止后副作用不得静默重试 |
| **进度可见** | 任务进行中用 on-screen narration / 步骤叙述说明当前在做什么 | 进度文案应对齐真实工具状态，避免「假装在做」的动画 |
| **敏感输入人工接管** | Operator 文档描述：登录、支付等敏感信息进入 Takeover，由用户亲自输入 | 凭证与支付字段永不进「Agent 代填」默认路径 |
| **应用级授权** | Claude Cowork / Computer use 帮助中心：访问每个应用前需用户批准；可随时停止 | 权限按应用/连接器最小化；默认关闭不必要的 connector |
| **提示注入后强制确认** | Anthropic Computer Use 文档：截图侧分类器发现可疑注入时，会引导模型先向用户确认再继续 | 对外网页/不可信 UI 上的 Agent，确认不是「体验噪音」，而是安全控件 |

对话产品里已收敛的流式输出、Regenerate、引用溯源，仍适用于 Agent 的「建议层」；一旦进入**改世界状态**，必须叠加上表的确认、进度与接管。细节控件仍见 [兜底、反馈与预期管理](fallback-and-feedback-design.md)。

### 可逆性决定默认策略（经验规则，非实验数据）

| 动作类型 | 例子 | 默认 UX |
|----------|------|---------|
| 只读 / 可丢弃草稿 | 搜索、摘要、未发送草稿 | 可自动执行；保留来源与中间结果 |
| 可撤销写入 | 本地草稿保存、可回滚配置 | 先做后告知 + 一键撤销；记录审计 |
| 难撤销 / 对外副作用 | 邮件已发、订单已提交、权限已改 | **必须事前确认**；必要时二次认证 |
| 高风险拒做 | 银行转账、重大人事决策等（厂商常公开声明会拒绝或强限制） | 产品层直接拒绝或强制人工流程，不交给「点一次就全权」 |

## 四、信任校准：overtrust 与 undertrust

| 状态 | 用户表现 | 产品风险 | 典型触发 |
|------|----------|----------|----------|
| **Overtrust** | 不审确认卡片、把 Agent 输出当事实、授权过宽后走开 | 误操作、合规事故、资金/声誉损失 | 演示很顺、文案过度承诺、确认疲劳、缺少失败案例曝光 |
| **Undertrust** | 每一步都人工重做、拒用 Agent、有能力也不开 | 采纳失败、成本沉没、团队放弃迭代 | 早期翻车、进度不可见、出错难撤销、能力边界说不清 |
| **Calibrated** | 低风险交给 Agent，高后果会停下来核验 | 效率与风险可同时谈 | 能力边界可见、确认有信息量、失败可解释可退出 |

**设计目标**：让用户的依赖行为跟着**能力与风险**变，而不是跟着营销口号变。引用溯源、完成证据、活动日志，都是校准手段——答案引擎侧的引用设计见 [答案引擎的引用与信任设计](../../03-case-studies/search-and-rec/answer-engine-citation-trust.md)；Agent 侧更强调「完成证据 = 工具回执 + 业务校验」，见 [Agent 产品生产化](../ai-product-operations/agent-product-design.md)。

### 信任校准设计清单（可直接贴进 PRD）

- [ ] **身份与边界**：界面标明当前是 AI/Agent；写清擅长 / 不支持 / 需人工的场景。
- [ ] **计划可见**：执行前或执行中展示简要计划；允许用户改目标或缩小范围。
- [ ] **步骤与进度**：当前步骤、已完成、卡点原因对用户可见；长任务可暂停。
- [ ] **确认分级**：按副作用与可逆性分级，而不是「所有工具调用都弹一次」或「从不弹」。
- [ ] **确认信息密度**：确认卡片含对象、范围、后果、可撤销性；避免只有「是否继续」。
- [ ] **打断与接管**：Stop / Take over 随时可用；取消后不重放副作用。
- [ ] **可撤销与审计**：可逆动作暴露 Undo；不可逆动作保留活动记录与完成证据。
- [ ] **失败可理解**：失败时说明已完成什么、卡在哪、下一步谁处理（人 / 重试 / 降级）。
- [ ] **权限最小化**：默认最小工具集与连接器；敏感站点/应用单独授权。
- [ ] **防确认疲劳**：高频低风险不要刷确认；把确认预算留给高后果动作。
- [ ] **校准信号**：对用户暴露「建议再核验」的场景（金额、对外发送、删除），而不是只用页脚免责声明。
- [ ] **评测挂钩**：把「该确认时是否确认」「用户是否过早授权」纳入 Agent 评测与线上监控，而不只看任务成功率。

## 五、「用完回人工」归因框架

用户「试了 Agent → 又回到人工 / 传统流程」，不一定是模型不够聪明。建议用同一套归因表拆原因，再决定改交互、改权限、改模型还是改范围。

```text
现象：用户中止 Agent / 转人工 / 自行用 GUI 重做
          │
          ├─ A. 能力缺口：任务本就不在能力边界内
          ├─ B. 交互失配：会做，但用户看不见、不敢放、改不动
          ├─ C. 信任错位：overtrust 翻车后报复性 undertrust；或从未建立最低信任
          ├─ D. 成本与时延：等太久、步骤太多、确认过碎
          └─ E. 组织与流程：合规要求必须人工签字；KPI 不认 Agent 结果
```

| 归因 | 可观察信号（举例） | 优先改什么 |
|------|-------------------|------------|
| **A 能力缺口** | 同类任务反复失败；工具返回业务拒绝；评测集上该意图成功率低 | 收窄宣传与默认入口；拒答/转人工话术；补工具或改 Workflow |
| **B 交互失配** | 用户频繁 Pause/Take over；改同一参数多次；找不到撤销 | 进度可见、确认卡片信息、编辑计划、Undo |
| **C 信任错位** | 早期一次高后果事故后 DAU 断崖；或从不点确认直接全权 | 风险分级确认、失败案例教育、渐进授权（先建议后自动） |
| **D 成本时延** | 中位完成时长过长；确认次数过高；中途离开 | 合并确认、并行只读步骤、异步通知「做完再叫我」 |
| **E 组织流程** | 工单系统强制人工字段；审计要自然人签名 | 把 Agent 定位为预填/起草；保留人工终签，而不是硬刚流程 |

**用法**：复盘时先归到 A–E 再开需求，避免所有流失都开成「换更强模型」。A 与 E 往往不是 UX 能单独救的；B/C/D 才是 Agentic UX 与信任校准的主战场。

## 六、何时该信、何时该介入：给用户的产品规则

把规则写进产品，比写进帮助文档更有效：

| 用户可感知规则 | 产品如何兑现 |
|----------------|--------------|
| 只读与草稿：可以先信，再改 | 默认自动；结果可编辑 |
| 要花钱、外发、删除、改权限：必须我点头 | 强制确认 + 展示后果 |
| 我看不懂它在干嘛：我不该盲信 | 步骤叙述 + 可随时接管 |
| 它说「完成了」：我要看到证据 | 工具回执 / 链接 / 变更摘要，而不是一句自然语言 |
| 它卡住或越权：应停，而不是硬闯 | 预算上限、拒答、转人工（见生产化文中的失败分类） |

渐进授权的常见路径（与黑箱题中的「渐进信任」一致，见 [AI 产品的黑箱问题](../../04-interview/basics/ai-blackbox-trust.md)）：

```text
建议（人决定） → 人确认后执行 → 白名单场景自动执行 → 高后果仍人工
```

每升一级，都应有：可观测的成功率/事故率、可撤回的权限开关、可回退的默认策略。

## 小结

Agentic UX 的工作，是把「模型会规划」翻译成用户可理解的**控制面**：进度、确认、撤销、接管与完成证据。信任校准的工作，是让依赖强度跟着真实能力走——压低 overtrust，抬升不必要的 undertrust。当用户「用完又回人工」时，用能力 / 交互 / 信任 / 成本 / 组织五类归因，才能避免把所有问题甩给模型团队。

## 相关阅读

- [怎么设计 Agent 的信任校准，避免过度信任或不信任？](../../04-interview/product-design/agent-trust-calibration-design.md)
- [兜底、反馈与预期管理:AI 产品的交互设计三件套](fallback-and-feedback-design.md)
- [AI 产品 PRD 怎么写:与传统 PRD 的核心差异](ai-prd-guide.md)
- [Agent 产品生产化：从会执行到可负责地完成任务](../ai-product-operations/agent-product-design.md)
- [AI 安全与 Agent 权限：把“能做什么”变成可治理的边界](../ai-product-operations/guardrails-and-agent-security.md)
- [AI 产品的黑箱问题如何影响用户信任?产品层面怎么做?](../../04-interview/basics/ai-blackbox-trust.md)
- [答案引擎的引用与信任设计](../../03-case-studies/search-and-rec/answer-engine-citation-trust.md)
- [Agent 入门：规划、记忆、工具调用](../../01-ai-basics/llm/agent-basics.md)

## 参考资料

- Parasuraman, R., & Riley, V. (1997). Humans and automation: Use, misuse, disuse, abuse. *Human Factors*, 39(2), 230–253. —— overtrust / undertrust（misuse / disuse）经典框架；2026-08-06 核验引用信息
- Lee, J. D., & See, K. A. (2004). Trust in automation: Designing for appropriate reliance. *Human Factors*, 46(1), 50–80. —— appropriate / calibrated reliance；2026-08-06 核验引用信息
- [Measuring and Understanding Trust Calibrations for Automated Systems](https://dl.acm.org/doi/10.1145/3544548.3581197) —— CHI 2023 信任校准综述；2026-08-06 访问
- [Introducing ChatGPT agent](https://openai.com/index/introducing-chatgpt-agent) —— OpenAI：确认、打断、进度叙述；2026-08-06 访问
- [Computer-Using Agent](https://openai.com/index/computer-using-agent/) —— OpenAI CUA / Operator：确认、Takeover、Watch mode；2026-08-06 访问
- [Introducing Operator](https://openai.com/index/introducing-operator/) —— OpenAI Operator 安全与确认说明；2026-08-06 访问
- [Computer use tool](https://platform.claude.com/docs/en/agents-and-tools/tool-use/computer-use-tool) —— Anthropic：人确认、提示注入防护；2026-08-06 访问
- [Let Claude use your computer in Cowork](https://support.claude.com/en/articles/14128542-let-claude-use-your-computer-in-cowork) —— Claude 应用级授权与可停止；2026-08-06 访问