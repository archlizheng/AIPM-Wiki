# 值得上手的 AI 工具与平台

> 对 AI PM 来说，「用过」比「听过」重要十倍。这里收录建议亲自上手的工具。  
> 截至 2026-07，定价与权益可能变化，具体以官网为准。

## 对话助手（至少深度使用 2-3 个）

- **Claude**：长文本分析、文档协作和复杂任务拆解能力稳定，适合深度内容工作。
- **ChatGPT**：生态最完整，适合做通用助手、原型验证和跨模态实验。
- **Gemini**：与 Google 生态联动强，适合已在 Workspace 体系工作的团队。
- **DeepSeek**：中文场景和性价比表现突出，适合做成本敏感型方案对照。
- **Kimi**：国内可用性与长上下文体验较好，适合中文资料密集场景。

## 工作流 / Agent 搭建平台(不写代码做 Demo)

编程 Agent、全栈应用生成平台、低代码工作流平台的完整分类盘点与选型建议,见 [Vibe Coding 工具盘点与选型(2026 年中)](../02-pm-skills/vibe-coding/tool-landscape.md)。

## 效率工具

- **NotebookLM**：做「基于你资料」的调研与问答非常高效，适合快速吸收长文档。
- **Perplexity**：带引用搜索体验好，适合先做信息扫描再深入查证。
- **Notion AI**：知识库、文档协作、会议整理一体化，适合团队协同。
- **Granola**：低打扰会议笔记工具，适合重视私密与轻量流程的团队。
- **Fireflies**：自动记录与整理访谈效率高，适合高频访谈或销售协同团队。



## 评测 / 标注 / 原型（可选进阶）

- **Braintrust**：适合搭建 LLM 应用评测闭环，把「感觉好用」变成可比较指标。
- **Label Studio Community**：开源标注工具，适合中小团队建立训练与评测数据集。
- **Figma AI**：适合快速做产品草图与交互迭代，降低从想法到可视化原型的成本。

## 求职作品集自检

- **[Offer库 AI 产品经理作品集自检](https://chanpinjingli.org/tools/ai-pm-portfolio-check)**：免费、无需登录或上传材料，按 12 项公开规则检查问题证据、AI 取舍、评测集、失败处理、项目真实性、指标和面试表达；结果只用于安排修改优先级，不预测招聘结果。



## 面试 Skills（作者原创）

- [interview-self-introduce](https://github.com/archlizheng/interview-self-introduce)：根据 JD、简历和面试轮次生成 30/60/90/120 秒自我介绍，支持中文、英文、双语和反模板改写。
- [interview-assessment](https://github.com/archlizheng/interview-assessment)：基于 JD、简历和面试记录做岗位匹配、面试准备和面试后复盘，支持候选人和招聘方视角。

安装：

```bash
npx skills add archlizheng/interview-self-introduce
npx skills add archlizheng/interview-assessment
```



## 选择建议

- 个人学习：先固定 2 个对话助手 + 1 个检索工具，避免来回切换导致方法不成型。
- 团队协作：优先统一文档、会议、知识沉淀工具，再考虑引入复杂 Agent 平台。
- 工具评估：先做 1 个真实场景 A/B 试用，再决定是否长期采购。
