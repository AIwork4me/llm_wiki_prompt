# AutoClaw / OpenClaw：从零构建 LLM Wiki 的完整提示词

请在**当前工作目录**中，非破坏式初始化一个可长期维护的 LLM Wiki。

AutoClaw 提供 OpenClaw 的一键配置，因此在本任务中可将 AutoClaw 与 OpenClaw 视为同一类 agent 工作流；请直接使用这一套统一协议执行，不要针对两者生成两套不同骨架。

当前工作目录就是唯一目标目录。不要额外创建 `llm-wiki/`、`wiki/` 或任何包裹目录。

这不是一次性笔记仓库，而是一个供后续 agent 与人类长期共同维护的**知识编译层**。目标不是把 Markdown 文件堆整齐，而是建立一套能稳定执行 `ingest / query / lint / QA / writeback` 的 wiki 协议。

如果当前目录不是完全空白，请先审计现状；优先原地补齐、修复和统一规范，而不是重建一套新项目。

## 核心目标

- 用 Obsidian 可直接打开和维护
- `raw/` 是不可变事实层
- `pages/` 是持续重写、持续演化的知识层
- `SCHEMA.md` 是未来所有 agent 的唯一执行协议
- 高价值回答必须沉淀回 wiki，而不是停留在聊天记录中
- 质量标准不是字段填满，而是准确、压缩、可导航、可追溯、可复用

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

说明：

- 当前工作目录本身就是 vault 根目录。
- “术语”不设单独目录，统一并入 `concepts/`。
- `source` 页面是对 raw 资料的索引与摘要页，不是原文复制。
- `analysis` 页面允许多源综合，鼓励跨 source 页面建模。

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

这些要求必须显式遵守，并在完成前自检：

1. 所有 Markdown 代码块必须使用三反引号 fenced code block。
2. 禁止使用单独一行的反引号伪装代码块。
3. 禁止出现损坏的 fenced code block。
4. 所有表格必须是合法 Markdown 表格。
5. 生成完成后，必须重新打开 `SCHEMA.md`、`README.md`、`index.md`、`log.md` 和全部模板文件，自查 Markdown 渲染是否完整。

## 页面类型

顶层类型固定为 6 种：

- `entity`
- `concept`
- `source`
- `analysis`
- `process`
- `question`

说明：

- `concept` 包含术语类页面。
- `source` 页面是 raw 资料的索引与摘要页。
- `analysis` 页面允许多源综合。

## 模板强制规则

请把这部分明确写进 `SCHEMA.md`：

- 模板不是可选参考，而是默认起点。
- 创建新页面时，必须从对应的 `_templates/*.md` 模板开始。
- 重写现有页面时，也必须先参照对应模板检查结构偏移。
- 不允许跳过模板直接从零写页面。
- QA 报告也必须优先从 `_templates/qa-report-template.md` 起步。

## 统一 frontmatter 规范

所有页面必须使用同一组核心字段。字段名使用 `snake_case`，枚举值使用英文，正文使用中文。

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

字段规则：

- `id`：格式为 `W-0001`，四位数字递增，不回收
- `title`：页面标题
- `type`：六种类型之一
- `summary`：一行摘要，用于 `index.md`
- `status`：`stable | tentative | contested | outdated`
- `created_at` / `updated_at`：日期
- `source_paths`：直接使用过的 raw 文件路径，统一使用 `/`
- `source_ids`：引用到的 `source` 页面 id 列表
- `related_ids`：普通关联关系
- `parent_ids / child_ids`：真实概念层级，必须双向一致
- `contrast_ids`：一般对比、并列或相邻概念
- `conflict_ids`：存在真实冲突关系的页面
- `aliases`：别名
- `tags`：标签
- `confidence`：`low | medium | high`
- `coverage`：`stub | partial | substantial`

不要使用这些字段作为长期真相源：

- `queried_count`
- `last_queried`
- `difficulty`
- `completeness: 80`

## 关系语义规则

请把这部分写进 `SCHEMA.md` 并作为硬规则：

1. `source_ids` 负责来源追溯。
2. `parent_ids / child_ids` 只用于真实概念层级或结构层级。
3. 禁止用 `parent_ids / child_ids` 表达“由某篇 raw 提炼而来”。
4. source 页面与其派生页面之间的关系，优先放在 `related_ids` 和正文导航中，不放进 `child_ids`，除非存在真实层级关系。
5. 只要 `status=contested`，就必须填写非空 `conflict_ids`，并在正文中解释冲突点。
6. `contrast_ids` 与 `conflict_ids` 不能混用。

## 文件命名与链接规范

### 文件命名

- 页面文件放到对应类型目录
- 推荐文件名：`W-0001-short-slug.md`
- `short_slug` 应稳定、简短、可读，优先英文或拼音
- 如果暂时无法确定 slug，允许使用 `W-0001.md`

### 正文链接规范

- frontmatter 中的关系字段保存 `id`
- 正文中的页面链接统一使用 Obsidian wikilink
- 推荐写法：`[[W-0007-attention|Attention]]`
- 不要混用纯文本页名、裸 `id`、相对路径链接作为主链接方式

### 关系与正文的一致性

- frontmatter 中的关系字段是结构化真相源
- 正文中的“相关页面”是给人读的导航区，应与 frontmatter 关系一致

## ID 分配规则

请在 `SCHEMA.md` 中明确写出：

1. 新建页面前，先扫描 `pages/` 下现有页面 frontmatter 中的全部 `id`
2. 取最大编号加一作为下一个 `id`
3. `index.md` 只作为展示和校对，不作为唯一 ID 真相源
4. 即使页面被删除，旧 ID 也不回收

## 去重与更新规则

请在 `SCHEMA.md` 中明确写出：

1. ingest 新 raw 前，先检查是否已有页面的 `source_paths` 包含相同 raw 路径
2. 如果已有相同 raw 的 `source` 页面，优先更新它，而不是新建重复 `source` 页面
3. 新证据进入时，知识页的目标是形成“当前最佳综合版本”
4. 更新现有知识页时，优先重写和整理相关段落，不要把页面做成 append-only 碎片堆积
5. append-only 只适用于 `log.md` 和 `qa-reports/`

## SCHEMA.md 必须表达的核心思想

请把 `SCHEMA.md` 写成未来 agent 的执行协议，并明确表达：

1. `raw/` 是不可变事实来源，禁止修改
2. `pages/` 是知识编译层，允许随新资料进入而更新、重写、重组和纠错
3. 好的 LLM Wiki 不是“每次 query 重新检索 raw”，而是“在 ingest 时把知识提前整理进 wiki，query 优先使用 wiki，再按需回 raw 验证”
4. 高价值回答如果产生可复用洞察，应直接回写为页面更新或新页面
5. 质量标准不是字段填满，而是准确、压缩、可导航、可追溯、可复用

## SCHEMA.md 必须包含的执行协议

### Ingest 协议

请在 `SCHEMA.md` 中明确写出以下规则：

1. 把新资料放入 `raw/` 对应目录，不修改原文件
2. 先检查是否已 ingest 过同一路径 raw
3. 对每一个新 raw，至少维护 1 个 `source` 页面
4. 再按信息密度创建或更新若干 `entity / concept / analysis / question` 页面
5. 不强制每个 raw 必须产出固定数量页面
6. 如果已有页面被新证据实质性改变，应更新这些页面
7. 若出现矛盾，不要强行统一口径，应标为 `contested` 并明确冲突点
8. 完成后更新 `index.md` 和 `log.md`
9. 如果没有可靠的独立 QA agent，也必须执行显式 self-check fallback，并写入 `qa-reports/`
10. 任何一次 ingest 都不能只写“未执行 QA”而不留下验证工件

### Query 协议

请在 `SCHEMA.md` 中明确写出以下规则：

1. 回答时优先查 `index.md` 和相关页面
2. wiki 信息不足时，再回到 `raw/` 或新来源补充
3. 普通 query 不要为了埋点去修改页面
4. 如果 query 产生了可复用洞察、对比、流程或新问题，应直接回写为页面更新或新页面
5. 完成回写后，记录到 `log.md`
6. README 与 SCHEMA 必须对这一行为保持一致

### Lint 协议

请在 `SCHEMA.md` 中明确写出以下检查项：

1. 断链检查：正文 wikilink、关系 id、`index.md` 条目是否有效
2. 关系一致性检查：`parent_ids` 与 `child_ids` 是否双向一致
3. 层级语义检查：source 页面不得用 `child_ids` 表达来源派生关系
4. 孤立页面检查：既无关系字段又缺少正文导航链接的页面
5. 来源追溯检查：页面是否缺少 `source_paths` 或 `source_ids`
6. 冲突检查：凡是 `status=contested` 的页面，必须存在非空 `conflict_ids`，且引用有效
7. 过时页面检查：`status=outdated` 的页面
8. 证据不足检查：`confidence=low` 且长期未补充的页面
9. 可拆分检查：过长、主题混杂的页面
10. Markdown 健康检查：是否存在损坏的 code block、表格或空标题

### QA 协议

请在 `SCHEMA.md` 中明确写出以下规则：

1. 优先由独立上下文或独立 agent 执行 QA
2. 如果当前环境没有可靠的独立 agent，也要执行显式 self-check fallback，并说明局限
3. QA 重点检查：
   - 是否忠于 raw
   - 是否压缩成高信号内容
   - 是否保留来源追溯
   - 是否建立合理链接
   - 是否正确处理冲突和不确定性
   - 是否引入了 raw 之外的后验背景信息
4. 如果页面引入了 raw 之外的后验背景信息：
   - 必须补充额外来源；或
   - 明确标注其不是当前 raw 直接支持的内容，并降低相关段落的置信度或确定性
5. QA 报告写入 `qa-reports/`，append-only
6. QA 报告文件命名建议：`qa-YYYY-MM-DD-HHMM-{subject}.md`

每份 QA 报告至少包含这些章节：

```text
# {title}

## 审核对象
## 来源范围
## 发现的问题
## 结论评分
## 是否需要修正
```

如果使用 fallback 自检，报告中必须明确说明：

- 这是 self-check fallback
- 不是独立 agent QA
- 有哪些局限

## index.md 要求

请创建或修复 `index.md`，要求：

- 顶部写明 `Last updated` 和 `Total pages`
- 按 6 种类型分区
- 每个分区用表格
- 表头至少包含：`ID | Title | Summary | Status | Updated`
- 初始化时表格可以为空，但结构必须完整
- 在 `SCHEMA.md` 中说明：后续维护时按 `type` 分区、按 `id` 升序排列

## log.md 要求

请创建或修复 `log.md`，要求：

- append-only
- 统一格式：`## [YYYY-MM-DD HH:MM] action | subject`
- 初始化时至少包含 1 条 `create | initialize wiki skeleton`
- 每条日志正文至少记录以下三类信息中的一项：
  - 受影响的 raw 路径
  - 受影响的页面 id
  - 受影响的文件路径
- 不允许只写动作名称而不写受影响对象

请在 `SCHEMA.md` 中补充常见 action 建议，例如：

- `create`
- `ingest`
- `update`
- `query-writeback`
- `lint`
- `qa`

## README.md 要求

请写一份简洁、可执行的中文 README，至少包含：

- 这是什么
- 目录结构
- 如何 ingest 第一篇资料
- 如何 query
- 如何运行 lint / QA
- 指向 `SCHEMA.md`

README 与 `SCHEMA.md` 必须保持行为一致。

## 模板要求

请在 `_templates/` 中创建或修复 7 个模板。要求：

- 共享统一 frontmatter
- 正文结构按类型区分
- 模板中的占位内容优先使用 HTML 注释，不要使用可能泄漏成正式内容的伪事实占位

### entity-template.md

正文必须包含：

- `# {title}`
- `## 一句话`
- `## 为什么重要`
- `## 要点`
- `## 定义`
- `## 解释`
- `## 例子`
- `## 相关页面`
- `## 来源`
- `## 未决问题`

### concept-template.md

正文必须包含：

- `# {title}`
- `## 一句话`
- `## 为什么重要`
- `## 要点`
- `## 定义`
- `## 解释`
- `## 例子`
- `## 相关页面`
- `## 来源`
- `## 未决问题`

### source-template.md

正文必须包含：

- `# {title}`
- `## 这是什么资料`
- `## 为什么重要`
- `## 关键主张`
- `## 可提炼页面`
- `## 相关页面`
- `## 原始路径`
- `## 未决问题`

额外要求：

- 不复制 raw 全文
- 重点说明这份资料说了什么、支持了哪些页面

### analysis-template.md

正文必须包含：

- `# {title}`
- `## 核心结论`
- `## 比较或综合`
- `## 证据`
- `## 启示`
- `## 相关页面`
- `## 来源`
- `## 未决问题`

额外要求：

- 鼓励多源综合
- 不要把 analysis 写成 source 摘要的拼接

### process-template.md

正文必须包含：

- `# {title}`
- `## 目标`
- `## 前置条件`
- `## 步骤`
- `## 输出`
- `## 常见失败模式`
- `## 相关页面`
- `## 来源`

### question-template.md

正文必须包含：

- `# {title}`
- `## 问题`
- `## 为什么重要`
- `## 已知`
- `## 未知`
- `## 还需要什么证据`
- `## 相关页面`
- `## 来源`

### qa-report-template.md

必须包含：

- frontmatter：`subject`, `date`, `reviewer`, `status`
- `# QA Report: {subject}`
- `## 审核对象`
- `## 来源范围`
- `## 发现的问题`
- `## 结论评分`
- `## 是否需要修正`

## 红线

请把这些红线写入 `SCHEMA.md` 并遵守：

1. 绝不修改 `raw/` 中的原始资料
2. 不凭空创建看起来像知识的示例页面
3. 不把单一来源伪装成多源综合，也不把多源综合伪装成单一来源
4. 不为了配额制造伪链接、伪关系、伪页面
5. 不把 query 埋点写进每个页面
6. 不创建重复 `source` 页面来表示同一个 raw
7. 不允许 source 页面用 `child_ids` 表达“由这篇资料提炼出的页面”
8. 删除或大改现有文件前，必须先确认是否属于本次任务范围

## 最终自检清单

在结束前，必须逐项自检并修复发现的问题：

1. 所有要求的目录和文件都存在
2. `SCHEMA.md`、`README.md`、`index.md`、`log.md`、全部模板文件能正常渲染
3. 不存在孤立反引号、损坏的 fenced code block、损坏表格
4. README 与 SCHEMA 的行为表述一致
5. 模板没有伪事实占位文本
6. `SCHEMA.md` 已写清模板强制使用、ID 分配、关系语义、去重、冲突规则、Lint/QA fallback、index/log/QA 维护规则
7. `qa-report-template.md` 已存在且与 `SCHEMA.md` 的 QA 章节一致

## 完成后的输出格式

完成后请只输出高信号结果：

1. 列出所有新建或更新的文件路径
2. 用 5-10 行概括你做出的关键设计决定
3. 明确说明你完成了哪些自检
4. 给出下一步的最小可执行操作：
   - 把原始资料放进 `raw/` 对应目录
   - 告诉 agent：`请 ingest raw/xxx.md`
   - agent 按 `SCHEMA.md` 执行 ingest，并更新 `index.md`、`log.md`、`qa-reports/`
5. 如有未执行项，明确说明原因

## 语言与风格

- `SCHEMA.md`、`README.md`、模板正文、日志说明全部使用中文
- frontmatter 字段名使用 `snake_case`
- frontmatter 枚举值使用英文
- 不要使用 emoji
- 不要写花哨介绍，只给可执行结果
