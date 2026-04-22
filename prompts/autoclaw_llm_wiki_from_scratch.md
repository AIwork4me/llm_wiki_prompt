# AutoClaw：从零初始化 LLM Wiki 的自治提示词

请在**当前工作目录**中，自主完成一个可长期维护的 LLM Wiki 初始化任务。

以当前工作目录作为唯一权威目标目录。不要额外创建 `llm-wiki/`、`wiki/` 或任何包裹目录。

你需要一次性完成：检查现状、初始化骨架、写出协议、创建模板、自检 Markdown、生成初始化日志。不要停在分析阶段。

## 总目标

把当前目录初始化成一个“会进化的 LLM Wiki”：

- `raw/` 保存不可变原始资料
- `pages/` 保存持续维护的知识层
- `SCHEMA.md` 作为唯一执行协议
- 未来可稳定执行 `ingest / query / lint / QA / writeback`

## 执行阶段

### 阶段 1：检查并原地初始化

1. 审计当前目录已有内容。
2. 非破坏式创建或修复以下结构：

```text
vault-root/
├── SCHEMA.md
├── README.md
├── index.md
├── log.md
├── .obsidian/
├── raw/{articles,papers,transcripts,assets}
├── pages/{entities,concepts,sources,analyses,processes,questions}
├── qa-reports/
└── _templates/
```

3. 不创建示例知识页，不 ingest raw。

### 阶段 2：写协议，而不是写介绍

请把 `SCHEMA.md` 写成未来 agent 的唯一执行协议，必须覆盖：

- 模板强制使用
- 统一 frontmatter
- `source_ids` 与层级关系的职责分离
- `status=contested` 与 `conflict_ids` 的联动
- ID 分配规则
- 去重规则
- Ingest / Query / Lint / QA 协议
- QA fallback 规则
- `index.md` / `log.md` / `qa-reports/` 的维护方式

### 阶段 3：创建模板

必须创建：

- `_templates/entity-template.md`
- `_templates/concept-template.md`
- `_templates/source-template.md`
- `_templates/analysis-template.md`
- `_templates/process-template.md`
- `_templates/question-template.md`
- `_templates/qa-report-template.md`

模板要求：

- 页面模板共享统一 frontmatter：
  - `id`
  - `title`
  - `type`
  - `summary`
  - `status`
  - `created_at`
  - `updated_at`
  - `source_paths`
  - `source_ids`
  - `related_ids`
  - `parent_ids`
  - `child_ids`
  - `contrast_ids`
  - `conflict_ids`
  - `aliases`
  - `tags`
  - `confidence`
  - `coverage`
- frontmatter 字段名用 `snake_case`
- 正文用中文
- 占位内容用 HTML 注释，不要用 `关键词1 / 主张1 / 证据1`

### 阶段 4：内置硬规则

请把以下规则写入 `SCHEMA.md`：

1. `raw/` 永不修改
2. `source_ids` 负责来源追溯
3. `parent_ids / child_ids` 只用于真实层级
4. source 页面不得用 `child_ids` 表达“由这篇 raw 提炼出的页面”
5. `status=contested` 时必须有非空 `conflict_ids`
6. Lint 必须检查 contested/conflict_ids 约束
7. 无独立 QA agent 时，也必须生成 fallback 自检报告
8. 任何 ingest 都不能只写“QA 未执行”而不留下验证工件

### 阶段 5：最终自检

在结束前必须重新打开并自检：

- `SCHEMA.md`
- `README.md`
- `index.md`
- `log.md`
- 全部模板文件

自检项目：

1. Markdown code block 是否完好
2. 表格是否完好
3. README 与 SCHEMA 是否一致
4. 模板是否无伪事实占位
5. `qa-report-template.md` 是否与 QA 协议一致

## README.md 最低要求

至少包含：

- 这是什么
- 目录结构
- 如何 ingest 第一篇资料
- 如何 query
- 如何运行 lint / QA
- 指向 `SCHEMA.md`

## index.md 最低要求

- 顶部写 `Last updated` 和 `Total pages`
- 按 6 种类型分区
- 每区用表格
- 表头：`ID | Title | Summary | Status | Updated`

## log.md 最低要求

- append-only
- 统一格式：`## [YYYY-MM-DD HH:MM] action | subject`
- 初始化时写入 `create | initialize wiki skeleton`
- 日志正文至少记录 raw 路径、页面 id、文件路径中的一类受影响对象

## 风格要求

- 全部中文
- 不要 emoji
- 不要写花哨介绍
- 结果必须可直接执行

## 任务完成后的输出

只输出：

1. 新建或更新的文件路径
2. 关键设计决定摘要
3. 已完成的自检项
4. 下一步最小操作
