# Default LLM Wiki Schema

这是一个默认 profile，不是唯一正确实现。

它的目标是提供一套足够具体、但仍然保持轻量和可演化的执行协议。后续如果领域发生变化，可以修改这个 schema；不要把它当成不可变的法律文本。

## 1. 目标

- 把 `raw/` 保持为不可变事实层
- 把 `pages/` 保持为持续重写、持续压缩的知识层
- 让 query 优先复用 wiki，而不是每次都从 raw 重新发现知识
- 让 `index.md` 和 `log.md` 成为有用的导航与审计工具，而不是额外的负担

## 2. 默认目录

默认从下面的结构开始；它是候选起点，不是要求一次性补齐的空骨架。不用到的子目录可以不建：

```text
vault-root/
├── SCHEMA.md
├── README.md
├── index.md
├── log.md
├── _state/
│   └── id-counter.md
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

- 当前工作目录本身就是 vault 根目录
- 目录结构服务于维护效率，不服务于美观配额
- 如果某个子目录长期不会用到，可以不创建
- `index.md` 里也不必提前保留空分区；没有内容的类型可以暂时不出现
- `_state/id-counter.md` 是操作状态文件，用于分配新页面 ID；它不是知识页面，也不进入 `index.md`

## 3. 真相源与派生产物

请始终区分这三类文件：

### 页面文件

`pages/` 下的页面正文与最小 frontmatter 是知识真相源。

### `index.md`

`index.md` 是导航页，是派生产物。它应该从页面信息生成或更新，而不是反过来定义页面。若 `index.md` 与页面冲突，以页面为准。

### `log.md`

`log.md` 是 append-only 时间线，只负责记录发生过什么，不负责定义知识内容。

### 不维护的重复表示

- 不使用 `child_ids`
- 不维护双向层级数组
- 不把“正文里的相关页面列表”和 frontmatter 里的关系字段都当作同等级真相源
- 能通过 backlink、搜索或派生逻辑得到的关系，不必手工再记一份

## 4. 默认页面类型

### `entity`

命名对象。它有身份边界，通常能在时间上持续存在，例如人物、组织、模型、产品、论文、数据集。

### `concept`

抽象概念、机制、术语、模式、方法论。

### `source`

对一个 raw 来源或一个紧密来源包的摘要、索引与提炼入口。

### `analysis`

跨页面、跨来源的综合、比较、判断、推理结果。query 产生的高价值洞察通常先落在这里。

### `process`

可重复执行的流程、检查单、操作法。

### `question`

尚未解决、值得跟踪的开放问题。

## 5. 最小 frontmatter

默认只要求最小字段：

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

规则：

- 必需：`id`、`title`、`type`、`updated_at`
- 强烈推荐：`summary`
- 推荐但按需：`source_ids`
- 只在有真实信息时添加：`related_ids`、`parent_ids`、`conflict_ids`、`status`、`aliases`、`tags`
- `source_paths` 主要用于 `source` 页面；其他页面只有在直接锚定原始文件时再加
- 不要为了整齐保留空数组或空字段

### 状态字段

当 `status` 出现时，只使用：

- `draft`
- `stable`
- `contested`
- `outdated`

建议：

- 临时但可复用的综合结论，先写成 `draft`
- 当前可用、但尚未经过独立复看或人类确认的页面，优先省略 `status`；在 `index.md` 这类派生导航里可以显示为 `usable`
- 只有来源、结构、边界都较充分，且经过独立 sanity check、独立 agent 复看或明确人类确认后，才写成 `stable`
- 来源之间存在实质性冲突时写成 `contested`
- 保留旧页但已被更新内容取代时写成 `outdated`

不要把 `stable` 当作“当前最佳综合版本”的同义词。`self_check` 默认不足以单独支撑 `stable`。

如果页面没有特殊状态压力，允许省略 `status`，由系统默认将其视作当前可用页；这种隐式状态可在派生索引中显示为 `usable`，但不能显示为 `stable`。

### 关系字段

- `source_ids`：知识来源页
- `related_ids`：导航级关联
- `parent_ids`：真正的上位概念或上位结构
- `conflict_ids`：直接冲突页

说明：

- 不使用 `child_ids`
- 不使用 `contrast_ids`
- “对比关系”如果重要，放到 `analysis` 正文里写清楚
- 如果 `status=contested`，必须提供非空 `conflict_ids` 并在正文解释冲突点

## 6. 文件命名与链接

- 推荐文件名：`W-0001-short-slug.md`
- `short-slug` 追求稳定、短、可读，不追求绝对完美
- 正文链接优先使用 Obsidian wikilink，例如 `[[W-0007-attention|Attention]]`
- frontmatter 中保存 `id`，正文中保存给人看的链接

## 7. ID 规则

1. 默认使用 `_state/id-counter.md` 或等价的单写入机制分配新 `id`
2. `_state/id-counter.md` 至少记录下一枚可用 ID，例如：

   ```yaml
   next_id: W-0004
   ```

3. 创建新页面前，先读取并占用 `next_id`，再把计数器推进到下一枚 ID；页面文件和计数器更新应在同一次变更中完成
4. 如果当前环境没有计数器文件，先根据现有页面扫描最大编号，创建计数器文件，然后再分配新 ID
5. 不要在可能并行写入的场景里把“扫描最大值 + 1”当作可靠方案；如果无法确认单写入，先停下来让用户或上层工作流协调
6. `index.md` 只是对照表，不是唯一 ID 真相源
7. 旧 ID 不回收

## 8. 模板规则

- 所有新页面从 `_templates/` 起步
- 模板是起点，不是必须全部填满的表单
- 模板中不使用伪事实占位文本
- 页面写成后，删除不适用的空章节

## 9. Ingest 协议

1. 把新资料放入 `raw/` 对应目录，不修改原文件
2. 检查是否已存在同一路径或同一来源对应的 `source` 页面
3. 为每个新 raw 维护至少 1 个 `source` 页面
4. 根据资料密度与复用价值，更新少量关键页面
5. 新证据改变既有结论时，重写相关页面，而不是 append 零碎补丁
6. 完成后更新 `index.md` 与 `log.md`
7. 留下至少 1 份 QA 工件；没有独立审阅时，也要留下 `self_check`

### ingest 偏好

- 宁可 3 个高信号页面，也不要 12 个空壳 stub
- 不要因为“以后可能会用到”就提前创建页面；页面应由当前证据密度和真实查询压力长出来
- `source` 页面要说明“这份资料支持了什么”，而不是复制原文
- 概念页和实体页应该尽量吸收新证据，保持“当前最佳综合版本”

## 10. Query / Writeback 协议

1. 回答问题时先读 `index.md` 和相关页面
2. wiki 不足时，再回到 raw 或新增来源
3. 不是每次聊天都要回写
4. 满足下面条件的内容可以回写：
   - 超过当前聊天可复用
   - 能明确锚定到至少一个 `source` 页面或 raw
   - 不是简单复述对话

### `draft -> promote`

可复用但暂未完全验证的内容，先写成 `draft`，常见落点是：

- `analysis`
- `question`
- 某个既有页面中新加的暂定段落

只有满足下面条件，才从 `draft` 提升：

- 来源链接与引用对象明确，且证据量足以支撑当前结论强度
- 页面结构被整理过，不是聊天记录转存
- 冲突、边界和不确定性已经说明
- 至少经过独立 sanity check、独立 agent 复看或明确的人类确认
- `index.md`、`log.md` 与相关链接已同步

### 晋升建议

- `self_check` 可以帮助发现结构问题，但默认不足以单独支撑 `stable`
- 低风险、个人使用的页面，如果暂时拿不到独立复看，优先继续保留 `draft`
- 高风险、对外发布、结论性强的页面，优先等独立 QA 或人类确认
- 如果独立 QA 不可得，就保留 `draft` 或 `contested`

## 11. Lint 协议

定期检查：

1. 断链和失效 wikilink
2. 缺失 `source_ids` 或 `source_paths` 的页面
3. 重复 `source` 页面
4. `index.md` 中缺页或过时条目
5. 长期停留在 `draft` 的页面
6. `contested` 页面是否解释了冲突
7. 过长、主题混杂、应拆分的页面
8. 损坏的 Markdown 结构

## 12. QA 协议

### QA 类型

- `independent`
  来自独立上下文、独立 agent 或明确的人类复核
- `self_check`
  当前上下文中的自检

### 规则

1. 每次较大的 ingest 或重要 writeback 都要留下 QA 工件
2. 没有独立审阅时，也必须留下 `self_check`
3. `self_check` 必须明确写出它不是独立 QA
4. 对外部读者重要或风险较高的内容，优先等待 `independent` 或人类确认

### QA 报告建议字段

QA 报告 frontmatter 建议包含：

- `subject`
- `date`
- `reviewer`
- `qa_type`
- `status`

报告正文至少包含：

- 审核对象
- 来源范围
- 执行的检查
- 发现的问题
- 局限
- 结论

## 13. `index.md` 与 `log.md`

### `index.md`

- 顶部写明 `Last updated` 和 `Total pages`
- 按页面类型分区
- 表头建议至少包含：`ID | Title | Summary | Status | Updated`
- 条目应从页面派生；若无法立刻自动生成，也要把它当作“应同步的派生文件”
- `Status` 应来自页面 frontmatter；如果页面省略 `status`，可以显示为 `usable`，但不能自动显示为 `stable`

### `log.md`

- append-only
- 标题格式建议：`## [YYYY-MM-DD HH:MM] action | subject`
- 正文至少写清以下之一：
  - 受影响的 raw 路径
  - 受影响的页面 id
  - 受影响的文件路径

常见 action：

- `create`
- `ingest`
- `update`
- `query-writeback`
- `lint`
- `qa`

## 14. 搜索扩展

默认先用：

- `index.md`
- Obsidian 搜索
- 本地 grep / ripgrep

当这些方式已经不适合当前规模时，再加本地搜索层。是否引入 `qmd`、自定义脚本或别的工具，由当前环境决定。

## 15. 红线

1. 绝不修改 `raw/` 中的原始资料
2. 不凭空捏造知识页
3. 不用厚重元数据伪装真实理解
4. 不把聊天记录原样塞进正式页面
5. 不创建重复 `source` 页面表示同一个 raw
6. 不把 `self_check` 说成 `independent`
