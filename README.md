# llm_wiki_prompt

面向 AutoClaw / OpenClaw 的统一 LLM Wiki 提示词仓库。

AutoClaw 由 Z.ai 开发，并提供 OpenClaw 的一键配置；在创建 LLM Wiki 这件事上，两者几乎是同一种工作流，所以这里不再拆成两套 prompt，而是维护一份可同时用于 AutoClaw 和 OpenClaw 的完整提示词。

核心灵感来自 Karpathy 的 [LLM Wiki gist](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f)：
不要把知识管理做成“每次提问时临时检索 raw 的 RAG”，而要做成“在 ingest 阶段持续编译知识，query 优先利用已经被整理好的 wiki 知识层”。

## 仓库内容

- [prompts/llm_wiki_from_scratch.md](./prompts/llm_wiki_from_scratch.md)
  一份同时适用于 AutoClaw 和 OpenClaw 的从零构建 LLM Wiki 提示词。

## 这份 prompt 解决什么问题

这不是一个“帮你生成一堆 Markdown 文件”的脚手架 prompt，而是一份让 agent 搭建知识编译系统的协议 prompt。它显式约束了这些在实战里最容易踩坑的点：

1. 当前工作目录就是 vault 根目录，禁止再额外套一层 `llm-wiki/`。
2. `raw/` 是不可变事实层，`pages/` 才是知识编译层。
3. 模板必须强制使用，新页和重写页都要从 `_templates/` 起步。
4. `source_ids` 负责来源追溯，`parent_ids / child_ids` 只负责真实层级。
5. source 页面不能用 `child_ids` 表达“由这篇资料提炼出的页面”。
6. `status=contested` 时必须有 `conflict_ids`，而且 Lint 要检查它。
7. 没有独立 QA agent 时，也必须留下 self-check fallback 工件。
8. `log.md` 不能只写动作名，必须写清受影响对象。

## 如何使用

1. 新建一个空目录，作为你的 LLM Wiki vault 根目录。
2. 打开 [prompts/llm_wiki_from_scratch.md](./prompts/llm_wiki_from_scratch.md)。
3. 将完整 prompt 原文粘贴给 AutoClaw 或 OpenClaw。
4. 等骨架生成后，把第一份资料放进 `raw/`。
5. 再告诉 agent：`请 ingest raw/xxx.pdf`。
6. 检查 `index.md`、`log.md`、`qa-reports/` 是否同步更新。

## 最小验收标准

一个可用的 LLM Wiki，至少要满足：

- 有 `raw/`、`pages/`、`SCHEMA.md`、`index.md`、`log.md`、`qa-reports/`、`_templates/`
- `SCHEMA.md` 是执行协议，而不是介绍文章
- 模板、来源追溯、层级关系、冲突关系、QA fallback 都有明确定义
- 第一次 ingest 后，能留下页面、索引、日志和 QA 工件

## 适合的场景

- 论文知识库
- LLM / AI 工程知识库
- 产品与竞品研究库
- 个人长期技术积累
- 小团队共享的结构化知识底座
