# llm_wiki_prompt

面向 AutoClaw / OpenClaw 的 LLM Wiki 提示词仓库，灵感来自 Karpathy 的 [LLM Wiki gist](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f)。

这次重构把仓库拆成了 5 个层次，目的是把“模式”与“默认实现”分开，而不是继续堆一份越来越重的超长 prompt：

- [docs/core_principles.md](./docs/core_principles.md)
  抽象理念。说明 LLM Wiki 为什么成立，以及哪些地方应该保持可变。
- [prompts/llm_wiki_from_scratch.md](./prompts/llm_wiki_from_scratch.md)
  可直接复制给 agent 的启动 prompt。它是任务入口，不再承担全部实现细节。
- [profiles/default/SCHEMA.md](./profiles/default/SCHEMA.md)
  一个更轻、更诚实的默认 schema profile，附带模板。
- [examples/minimal-vault/README.md](./examples/minimal-vault/README.md)
  最小样例 vault，展示一次 ingest 和一次 query-writeback 之后，产物大概应该长什么样。
- [evals/regression_checklist.md](./evals/regression_checklist.md)
  回归检查清单。以后改 prompt 时，不用再只靠主观感觉判断好坏。

## 这次调整解决了什么

- 不再把“理念、协议、模板、SOP、输出格式”塞进同一个 500 行 prompt。
- 默认 schema 只保留最小必需元数据，空字段不再强制填满。
- `query -> writeback` 新增 `draft -> promote` 门槛，避免把聊天里的临时推断直接固化成“知识”。
- `index.md` 被明确定义为派生产物；页面本身才是真相源，`log.md` 只负责审计。
- `child_ids` 被移除，避免维护双向层级带来的重复真相源和长期漂移。
- `entity` 和 `concept` 的模板被真正区分开，不再只是两个名字不同的同构页面。
- `self-check` 与独立 QA 被明确区分，不能再把“自己复查自己”包装成同等级背书。
- 仓库补上了样例和回归基线，后续优化可以更稳定。

## 如何使用

1. 先读 [docs/core_principles.md](./docs/core_principles.md)，理解这个仓库想表达的模式。
2. 需要快速开始时，把 [prompts/llm_wiki_from_scratch.md](./prompts/llm_wiki_from_scratch.md) 直接发给 agent。
3. 需要更具体的默认实现时，再把 [profiles/default/SCHEMA.md](./profiles/default/SCHEMA.md) 也提供给 agent，或者要求它按这个 profile 初始化。
4. 初始化完成后，对照 [examples/minimal-vault/README.md](./examples/minimal-vault/README.md) 和 [evals/regression_checklist.md](./evals/regression_checklist.md) 做一次 sanity check。

## 现在的默认设计

- `raw/` 是不可变事实层。
- `pages/` 是持续重写、持续压缩的知识层。
- 页面文件本身是真相源；`index.md` 是导航派生产物；`log.md` 是时间线审计。
- frontmatter 默认只要求最小字段，其他字段按需出现，不维护一堆空数组。
- 可复用但尚未完成验证的 query 结果先写成 `draft`，再决定是否提升为 `stable` 或 `contested`。
- 默认先用 `index.md`、Obsidian 搜索和简单 grep；当规模让这些手段明显吃力时，再补本地搜索工具。

## 适合的场景

- 论文和研究知识库
- LLM / AI 工程知识库
- 产品与竞品研究库
- 长期个人学习与写作积累
- 小团队共享的结构化知识底座

## 不打算做的事

- 不把这个仓库写成某个领域唯一正确的固定制度。
- 不强迫所有场景都用同一套目录、字段和页面类型。
- 不用“字段更全”冒充“知识更真”。
