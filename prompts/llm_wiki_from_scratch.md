# AutoClaw / OpenClaw：初始化一个可长期维护的 LLM Wiki

请在**当前工作目录**中，非破坏式初始化一个可长期维护的 LLM Wiki。

当前工作目录就是目标 vault 根目录。不要额外创建 `llm-wiki/`、`wiki/` 或其他包裹目录。

这是一份**启动任务**，不是一部固定宪法。请用 Karpathy 的 LLM Wiki 模式工作：

- `raw/` 一类目录是不可变事实层
- wiki 页面层是持续编译、持续重写的知识层
- schema 是执行协议，但应该和用户一起演化，而不是越写越僵

如果当前仓库里已经有 `docs/core_principles.md`、`profiles/default/SCHEMA.md` 或类似文件，请先阅读它们，把它们当成默认实现，而不是不可修改的法律。

## 本次任务的目标

初始化一套能稳定执行 `ingest / query / lint / QA / writeback` 的 wiki 工作流，并让后续 agent 能继续接手维护。

质量标准不是“字段越多越好”，而是：

- 准确
- 压缩
- 可追溯
- 可导航
- 可演化

## 工作方式

1. 先审计当前目录已有文件和目录。
2. 保留与本任务无关的现有内容，不要随意删除、覆盖或移动。
3. 如果目标文件已存在，优先理解和修复，而不是盲目重写。
4. 本次只做“骨架和协议初始化”，不要凭空创建示例知识页，也不要 ingest 任意 raw 资料。
5. 如果仓库里已经存在一个默认 profile，可以采用它，但要在必要处做本地化调整。

## 至少要有的骨架

请至少创建或修复这些内容：

- `SCHEMA.md`
- `README.md`
- `index.md`
- `log.md`
- `raw/`
- `pages/`
- `qa-reports/`
- `_templates/`

是否需要 `raw/articles/`、`raw/papers/`、`pages/entities/` 这类子目录，请按当前领域决定；不要为了凑结构而制造不会用到的层级。

## 默认页面类型

默认可以从下面 6 类页面开始，但如果当前领域明显不需要全部类型，可以精简：

- `entity`
  命名对象，如人物、组织、模型、论文、产品、数据集。
- `concept`
  抽象概念、机制、术语、模式。
- `source`
  对单个 raw 来源或紧密来源包的摘要与索引。
- `analysis`
  可复用的综合、比较、推理、判断。
- `process`
  可重复执行的流程、操作法、检查单。
- `question`
  值得继续追踪的开放问题。

如果保留 `entity` 和 `concept`，请确保它们在 schema 和模板中真的有不同职责，不要只换名字不换结构。

## 单一真相源

请在 `SCHEMA.md` 中明确：

- 页面文件本身是知识真相源
- `index.md` 是导航和派生产物；如果它和页面冲突，以页面为准
- `log.md` 是 append-only 审计时间线，不负责定义知识本身
- 不要维护明显重复的双向关系字段；能通过搜索、backlink 或派生逻辑得到的关系，不必再手工写第二份

## 最小 frontmatter

默认只要求真正必要的字段。推荐从下面开始：

```yaml
---
id: W-0001
title: Attention
type: concept
updated_at: YYYY-MM-DD
summary: 一句话摘要
source_ids:
  - W-0003
related_ids:
  - W-0007
parent_ids:
  - W-0002
conflict_ids:
  - W-0012
status: draft
aliases:
  - self-attention
tags:
  - transformer
---
```

要求：

- `id`、`title`、`type`、`updated_at` 是默认必需字段
- `summary`、`source_ids` 强烈推荐，但可以在确实未知时稍后补齐
- 其余字段按需出现；不要为了“整齐”维护一堆空数组
- 不要使用 `child_ids`
- 不要引入 `confidence=high`、`coverage=substantial` 这类容易制造伪精确感的字段，除非当前领域真的需要且用户同意

## 模板原则

请创建或修复模板，但把模板当作**可删减的起点**，不要当作必须全部填满的表单。

模板要求：

- 共享最小 frontmatter
- 每种页面类型正文结构不同
- 占位内容优先使用 HTML 注释
- 页面创建后，可以删除不适用的章节，不要为了对齐模板保留空壳标题

## Ingest 规则

请在 `SCHEMA.md` 中写清：

1. `raw/` 中的原始资料不可修改。
2. ingest 时先检查是否已有对应 `source` 页面或重复路径。
3. 每个新 raw 至少要有一个 `source` 页面。
4. 根据资料密度和复用价值，创建或更新少量高信号页面；宁可少而准，不要批量造 stub。
5. 如果新资料改变了既有结论，要重写相关页面，而不是简单 append。
6. 完成后更新 `index.md` 和 `log.md`。

## Query / Writeback 规则

请在 `SCHEMA.md` 中写清：

1. query 时优先查 wiki 页面，再按需回 raw 验证。
2. 普通聊天回答不需要强行回写。
3. 真正可复用的 query 结果才回写。
4. 回写时采用 `draft -> promote` 两段式：
   - 先把尚未完全验证的综合、比较或新问题写成 `draft`
   - 只有在来源、结构和状态都检查过之后，才提升为 `stable` 或 `contested`
5. 任何由 query 产生的页面更新都要记录到 `log.md`。

## Lint 规则

请在 `SCHEMA.md` 中至少定义这些检查：

- 断链检查
- 缺失来源检查
- 重复 source 检查
- 过时或漂移的 index 检查
- 主题混杂或过长页面检查
- `draft` 页面是否长期无人处理
- `contested` 页面是否明确解释冲突
- Markdown 健康检查

## QA 规则

请在 `SCHEMA.md` 中明确区分两种 QA：

- `independent`
  来自独立上下文、独立 agent 或明确的人类复核
- `self_check`
  当前上下文中的自检

要求：

1. 没有独立 QA 时，也要留下 `self_check` 工件。
2. `self_check` 不能伪装成独立审阅。
3. 对高风险、外部发布或结论性很强的内容，优先保留 `draft`，直到有独立 QA 或人类确认。
4. QA 报告写入 `qa-reports/`，append-only。

## 扩展与规模

请在 `SCHEMA.md` 中明确：

- 小到中等规模时，`index.md` + Obsidian 搜索 + 简单 grep 就够用
- 当 index 已经不适合在一个上下文里快速扫读，或页面规模让检索明显吃力时，再引入本地搜索工具
- 搜索层是增强项，不是初始化阶段的硬依赖

## 红线

请遵守这些红线：

1. 绝不修改 `raw/` 中的原始资料。
2. 不凭空制造示例知识页。
3. 不用元数据伪装真实理解。
4. 不为了凑数创建无意义目录、空页面、空字段。
5. 不把 query 聊天记录原样塞进正式页面。
6. 不把 self-check 写成独立 QA。

## 完成前自检

结束前请检查并修复：

1. 必要骨架是否存在
2. `SCHEMA.md`、`README.md`、`index.md`、`log.md`、模板是否能正常渲染
3. README 与 SCHEMA 的行为描述是否一致
4. 是否只保留了最小且有信息量的元数据
5. 是否为 query-writeback 建立了 `draft -> promote` 门槛
6. 是否明确了 `index.md` 是派生产物、`log.md` 是审计记录
7. 是否诚实区分了 `self_check` 和 `independent`

## 完成后的输出格式

完成后请只输出高信号结果：

1. 列出新建或更新的文件路径
2. 用 5-10 行说明关键设计决定
3. 说明做了哪些自检
4. 给出下一步最小可执行操作
5. 如有未执行项，明确说明原因
