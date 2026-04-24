# minimal-vault

这是一个最小样例 vault，用来回答两个问题：

1. 第一次 ingest 之后，产物大概应该长什么样？
2. 一次 query-writeback 之后，什么内容应该先停留在 `draft`，而不是直接冒充稳定知识？

这个样例刻意保持很小：

- 1 个 raw 来源
- 1 个 `source` 页面
- 1 个 `concept` 页面
- 1 个 `entity` 页面
- 1 个 `analysis` 页面
- 1 份 `self_check` QA 报告

它不是唯一正确答案，但应该体现默认 profile 的几个关键原则：

- 页面比元数据更重要
- `index.md` 是派生导航，不是知识真相源
- query 产生的综合结论先保持 `draft`
- `self_check` 明确写出自身局限

## 推荐查看顺序

1. [raw/articles/attention-note.md](./raw/articles/attention-note.md)
2. [pages/sources/W-0001-attention-note.md](./pages/sources/W-0001-attention-note.md)
3. [pages/concepts/W-0002-attention.md](./pages/concepts/W-0002-attention.md)
4. [pages/entities/W-0003-transformer.md](./pages/entities/W-0003-transformer.md)
5. [pages/analyses/W-0004-attention-shortens-information-paths.md](./pages/analyses/W-0004-attention-shortens-information-paths.md)
6. [qa-reports/qa-2026-04-24-attention-self-check.md](./qa-reports/qa-2026-04-24-attention-self-check.md)
7. [index.md](./index.md) 和 [log.md](./log.md)

## 说明

- 这个样例复用了仓库的 [default schema profile](../../profiles/default/SCHEMA.md)。
- 为了保持样例易读，这里没有重复复制 `_templates/`；模板以 profile 中的版本为准。
