# OpenClaw：从零初始化 LLM Wiki 的完整提示词

请在**当前工作目录**中，非破坏式初始化一个可长期维护的 LLM Wiki。

以当前工作目录作为唯一权威目标目录。不要额外创建 `llm-wiki/`、`wiki/` 或任何包裹目录。

这不是一次性笔记仓库，而是一个供后续 agent 与人类长期共同维护的**知识编译层**。目标不是把 Markdown 文件堆整齐，而是建立一个能稳定执行 `ingest / query / lint / QA / writeback` 的 wiki 协议。

## 核心目标

- 用 Obsidian 可直接打开和维护
- `raw/` 是不可变事实层
- `pages/` 是持续维护、持续重写的知识层
- `SCHEMA.md` 是未来所有 agent 的唯一执行协议
- 高价值回答必须沉淀回 wiki，而不是停留在聊天记录里

## 工作模式

1. 先检查当前目录已有文件和目录。
2. 保留现有内容，不要删除、覆盖或移动与本任务无关的文件。
3. 如果目标文件已存在，优先理解现状并修复或补齐，而不是盲目重写。
4. 本次任务只做“骨架和协议初始化”，不要凭空创建示例知识页，也不要 ingest 任意 raw 资料。
5. 如果环境支持 git，可以保留 git 说明，但不能把 `git commit` 当作初始化成功的前提。

## 目录结构

请确保项目结构至少包含：

```text
vault-root/
├── SCHEMA.md
├── README.md
├── index.md
├── log.md
├── .obsidian/
├── raw/
│   ├── articles/
│   ├── papers/
│   ├── transcripts/
│   └── assets/
├── pages/
│   ├── entities/
│   ├── concepts/
│   ├── sources/
│   ├── analyses/
│   ├── processes/
│   └── questions/
├── qa-reports/
└── _templates/
```

## 必须创建或修复的文件

- `SCHEMA.md`
- `README.md`
- `index.md`
- `log.md`
- `_templates/entity-template.md`
- `_templates/concept-template.md`
- `_templates/source-template.md`
- `_templates/analysis-template.md`
- `_templates/process-template.md`
- `_templates/question-template.md`
- `_templates/qa-report-template.md`

## 强制格式要求

1. 所有 Markdown 代码块必须使用三反引号 fenced code block。
2. 禁止使用单独一行的反引号伪装代码块。
3. 所有表格必须是合法 Markdown 表格。
4. 生成完成后，必须重新打开 `SCHEMA.md`、`README.md`、`index.md`、`log.md` 和全部模板文件，自查 Markdown 渲染是否完整。

## 页面类型

固定 6 种顶层类型：

- `entity`
- `concept`
- `source`
- `analysis`
- `process`
- `question`

“术语”并入 `concept`。

## 模板强制规则

请把这部分明确写进 `SCHEMA.md`：

- 模板不是可选参考，而是默认起点。
- 创建新页面时，必须从对应 `_templates/*.md` 模板开始。
- 重写现有页面时，也必须先参照对应模板检查结构偏移。
- QA 报告也必须优先从 `_templates/qa-report-template.md` 起步。

## 统一 frontmatter

所有页面必须使用统一字段：

```yaml
---
id: W-0001
title: Attention
type: concept
summary: 一句话摘要
status: stable
created_at: YYYY-MM-DD
updated_at: YYYY-MM-DD
source_paths:
  - raw/papers/transformer.pdf
source_ids:
  - W-0003
related_ids: []
parent_ids: []
child_ids: []
contrast_ids: []
conflict_ids: []
aliases: []
tags: []
confidence: high
coverage: partial
---
```

其中：

- `source_ids` 负责来源追溯
- `parent_ids / child_ids` 只负责真实层级
- **禁止**用 `child_ids` 表达“由某篇 raw 提炼而来”
- `status=contested` 时必须填写非空 `conflict_ids`

## 关键协议

### Ingest

请在 `SCHEMA.md` 中明确写出：

1. 新资料进入 `raw/` 后不可修改。
2. 先检查是否已 ingest 过同一路径 raw。
3. 每个 raw 至少维护 1 个 `source` 页面。
4. 再按信息密度创建或更新 `entity / concept / analysis / question` 页面。
5. source 追溯使用 `source_ids`，不是层级关系。
6. 更新知识页时优先重写，不做 append-only 碎片堆积。
7. 完成后更新 `index.md` 和 `log.md`。
8. 没有独立 QA agent 时，也必须写 self-check fallback 到 `qa-reports/`。

### Query

1. 优先查 `index.md` 和相关页面。
2. wiki 不足时再回 raw。
3. 普通 query 不写页面。
4. 形成可复用洞察时，必须直接回写页面并记录到 `log.md`。

### Lint

至少检查：

1. wikilink、关系 id、index 条目是否有效
2. `parent_ids / child_ids` 是否双向一致
3. source 页面是否误用 `child_ids`
4. 是否缺少 `source_paths` / `source_ids`
5. `status=contested` 的页面是否有非空 `conflict_ids`
6. Markdown 是否有坏掉的 code block / 表格 / 空标题

### QA

至少要求：

1. 优先独立 agent QA
2. 无独立 agent 时，必须 fallback 自检
3. 检查是否忠于 raw、是否高信号压缩、是否保留来源追溯、是否引入 raw 之外的后验补充
4. 每份 QA 报告必须包含：
   - 审核对象
   - 来源范围
   - 发现的问题
   - 结论评分
   - 是否需要修正

## log.md 要求

- append-only
- 格式：`## [YYYY-MM-DD HH:MM] action | subject`
- 每条日志正文至少记录一项受影响对象：
  - raw 路径
  - 页面 id
  - 文件路径

## README.md 要求

至少包含：

- 这是什么
- 目录结构
- 如何 ingest 第一篇资料
- 如何 query
- 如何运行 lint / QA
- 指向 `SCHEMA.md`

README 与 `SCHEMA.md` 必须保持行为一致。

## 红线

1. 绝不修改 `raw/`
2. 不凭空创建示例知识页
3. 不把单一来源伪装成多源综合
4. 不制造伪链接、伪关系、伪页面
5. 不把 query 埋点写进每个页面
6. 不创建重复 source 页面
7. 不允许 source 页面用 `child_ids` 表达“由这篇资料提炼出的页面”

## 最终输出

完成后只输出高信号结果：

1. 列出所有新建或更新的文件
2. 用 5-10 行概括关键设计决定
3. 说明完成了哪些自检
4. 给出下一步最小操作：
   - 把原始资料放进 `raw/`
   - 告诉 agent：`请 ingest raw/xxx.md`
   - agent 更新 `index.md`、`log.md`、`qa-reports/`
