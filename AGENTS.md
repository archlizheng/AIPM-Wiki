---
title: AGENTS
type: note
permalink: aipmwiki/agents
---

# AGENTS.md

This file provides guidance to Codex (Codex.ai/code) when working with code in this repository.

## What this repository is

AIPM Wiki (`archlizheng/AIPM-Wiki`) is a **content-only knowledge base**, not a software project — there is no build, lint, or test tooling. It is authored as an Obsidian vault and published as a GitHub repo (bilingual: content is Chinese). All work here is writing/editing Markdown, organizing cross-links, and keeping indexes in sync — never code changes.

## Repository structure

```
docs/
  00-roadmap/          转行指南、能力模型、岗位盘点 — entry point, numbered reading order
  01-ai-basics/        ML、大模型、Prompt 工程、术语表 (has llm/, machine-learning/, prompt-engineering/ subdirs)
  02-pm-skills/        PRD 写法、模型评估、数据标注、成本测算 (cost-and-tech/, data-annotation/, model-evaluation/, prd-and-design/, vibe-coding/)
  03-case-studies/      产品案例拆解 (aigc/, chatbot-assistant/, search-and-rec/, vertical/)
  04-interview/         面试题库 + 面经 (basics/, behavioral/, case-analysis/, experiences/, product-design/)
  05-resources/         书籍、课程、工具、必读文章
templates/              Required structure for interview questions, interview experiences, case studies
assets/                  Images, organized in per-topic subdirectories, referenced with relative paths
web-articles/            Gitignored staging area — clipped raw web pages/articles, local only (see below)
.research/               Gitignored raw research material — local only, feeds the wiki-research skill
```

The `docs/` numbering (00–05) is a deliberate reading order for newcomers (see README's 建议学习路径), not just a filing scheme — new content should slot into this progression rather than inventing new top-level sections.

## Content conventions (from CONTRIBUTING.md)

1. **File naming**: lowercase English + hyphens (e.g. `what-is-rag.md`); the Chinese title goes on the first line as `# 标题`.
2. **Placement**: content goes under the matching numbered `docs/` subdirectory; images go in `assets/<same-name-subdir>/`, referenced with relative paths.
3. **Templates are mandatory** for their content types — do not freehand the structure:
   - `templates/interview-question.md` — 题目 → 考察点 → 参考答案 → 追问延伸 → 相关阅读
   - `templates/interview-experience.md`
   - `templates/case-study.md` — 产品背景 → 核心功能拆解 → 技术方案推测 → 商业模式 → PM 视角的启示 → 参考资料
4. **Sourcing**: original content or cite sources; no reproducing paid-course material. Technical/pricing claims should be verified against official docs (Anthropic/OpenAI/Google) with an access-date noted (this repo tracks LLM pricing/model landscape and treats currency of that data as important — see `docs/01-ai-basics/llm/model-landscape.md`).
5. **Cross-linking is load-bearing**: 面试题、正课、案例之间应充分交叉引用（"相关阅读" sections). When adding an article, also update the index in that directory's `README.md` — every `docs/<section>/README.md` is a curated table of contents, not auto-generated, so new files are invisible until linked there.
6. **Neutral tone** in 面经 (interview experiences): no personal attacks, company-related claims must be factual.

## Working with `.research/` and `web-articles/`

`.research/<topic>/` holds gitignored raw research material (fetched pages, briefs) staged before writing a polished article — it is local-only scratch space, never committed, and is consumed by the separate `wiki-research` Cursor skill (lives outside this repo, in the user's global Cursor skills). `web-articles/<source>/` is a similar local staging area for clipped web articles/courses (e.g. Anthropic learning resources, DeepLearning.AI pages) saved with their images before being rewritten into a proper `docs/` article. Don't treat files under either directory as publishable content or link to them from `docs/` — they're raw input, not output.

## Feishu (飞书) sync

`docs/` content is one-way synced to a public Feishu wiki via the `feishu-wiki-sync` skill (`.cursor/skills/feishu-wiki-sync/SKILL.md`) using `lark-cli`. Key rules if asked to sync:
- GitHub is the sole source of truth; the Feishu side is read-only display and gets overwritten on every sync — never reconcile edits backward from Feishu.
- Only HEAD's **committed** content syncs; uncommitted drafts are treated as unreviewed and skipped.
- Sync state (node token mappings, last synced commit) lives in `.feishu-sync.json` at repo root — this file is intentionally committed (contains no secrets).
- `templates/`, `.github/` are excluded from sync.

## Contribution/PR flow

Standard fork → branch (e.g. `add/rag-interview-question`) → PR flow. Issues should be claimed before writing longer knowledge articles to avoid duplicate work (per CONTRIBUTING.md). License is CC BY-NC-SA 4.0 (attribution required, non-commercial).