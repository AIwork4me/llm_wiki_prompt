# llm_wiki_prompt

面向 AutoClaw / OpenClaw 等 Deep Agents的 LLM Wiki 提示词仓库。

这个仓库不是在卖一份“万能 prompt”，而是在分享我们学习完 Karpathy 的 [LLM Wiki gist](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f) 之后，对 LLM Wiki 应该如何设计、如何落地、如何长期维护的一套完整思考。

我们更关心的不是“怎么让 agent 一次性生成一堆 Markdown 文件”，而是：

- 如何把 `raw` 资料层、wiki 知识层、schema 协议层真正分开
- 如何让知识随着 ingest 和 query 持续积累，而不是每次都临时重建
- 如何避免把 wiki 做成重元数据、重表单、轻理解的伪知识系统
- 如何让 agent 在长期维护中保持结构、可追溯性和诚实的质量边界

## 我们从 Karpathy LLM Wiki 学到了什么

Karpathy 那份 gist 最打动人的地方，不是某个具体目录结构，而是一个判断：

知识管理不应该只停留在“提问时临时去 raw 里找片段”的 RAG 形态，而应该在 ingest 阶段持续把知识编译进一个持久存在的 wiki。

这意味着：

- `raw/` 是事实层，尽量不改
- `pages/` 是知识层，会持续重写、持续压缩、持续联结
- schema 不是介绍文案，而是让 agent 按同一工作流维护 wiki 的执行协议
- 高价值问答不应该蒸发在聊天记录里，而应该在合适时机回写进 wiki

我们把这些理解进一步展开成了更完整的设计取舍。

## 这份仓库想回答的设计问题

### 1. 什么才是 LLM Wiki 的真相源

我们的默认答案是：

- 页面文件本身是真相源
- `index.md` 是导航页和派生产物
- `log.md` 是 append-only 的审计时间线

这背后的原因很直接：同一份知识如果要在 frontmatter、正文、索引、日志里反复手工同步，LLM 长期维护时一定会漂。

### 2. 元数据应该有多重

我们的取向是最小但有信息量的元数据。

我们不认为“字段更全”就等于“知识更真”。相反，很多看似整齐的字段会制造伪精确感，例如把一堆空数组、空标签、主观置信度长期留在页面里。默认 profile 只要求最小 frontmatter，其他字段按需出现。

### 3. query 的结果该不该写回

答案是：该，但要有门槛。

不是所有聊天结论都应该立刻进入正式知识层。仓库里默认采用 `draft -> promote` 的两段式：

- 可复用但尚未充分验证的综合、比较、推理，先落成 `draft`
- 只有来源、结构、状态和边界都清楚后，才晋升为 `stable` 或 `contested`

这样做是为了避免 wiki 慢慢变成“读取自己的总结，再生成新的总结”。

### 4. QA 应该怎么诚实表达

我们明确区分：

- `independent`
  独立上下文、独立 agent 或人类复核
- `self_check`
  当前上下文中的自检

`self_check` 仍然有价值，但它不能伪装成独立 QA。这一点对长期可信度非常重要。

### 5. 目录和类型应该固定到什么程度

Karpathy 原始 gist 的精神是 “communicate the pattern, then instantiate with the agent”，不是把所有实现细节在第一天写死。

所以这个仓库采取的是：

- 给出默认起点，而不是最低合规模板
- 提供清晰的设计理由
- 保持可裁剪、可演化

你可以照着默认 profile 起步，也可以按你的领域删减目录、页面类型和模板。

## 仓库结构

- [docs/core_principles.md](./docs/core_principles.md)
  我们对 LLM Wiki 模式的抽象理解，重点解释为什么这样设计。
- [prompts/llm_wiki_from_scratch.md](./prompts/llm_wiki_from_scratch.md)
  可直接发给 agent 的启动 prompt，用来初始化一个可长期维护的 LLM Wiki。
- [profiles/default/SCHEMA.md](./profiles/default/SCHEMA.md)
  一个默认落地方案，包含目录、最小 frontmatter、ingest/query/lint/QA 约定与模板。
- [profiles/default/_templates/](./profiles/default/_templates/)
  默认模板集合；profile 入口见 [profiles/default/README.md](./profiles/default/README.md)。
- [examples/minimal-vault/README.md](./examples/minimal-vault/README.md)
  最小样例 vault，展示一次 ingest 和一次 query-writeback 之后，产物应该是什么形态。
- [evals/regression_checklist.md](./evals/regression_checklist.md)
  手工回归清单，避免 prompt 和 schema 继续滑向越来越重、越来越假的方向。

## 默认设计结论

- `raw/` 是不可变事实层
- `pages/` 是持续编译、持续重写的知识层
- 页面文件是真相源，`index.md` 是派生产物，`log.md` 是审计记录
- 默认结构是候选起点，不是必须先补齐的空骨架
- ID 分配默认通过 `_state/id-counter.md` 这类单写入状态文件完成，而不是长期依赖“扫描最大值 + 1”
- frontmatter 默认只要求最小字段，其他字段按需添加
- `entity` 和 `concept` 应该真的承担不同职责，而不是只换名字
- query 产生的高价值内容允许回写，但先经过更保守的 `draft -> promote`
- `self_check` 与 `independent` 必须诚实区分
- 小到中等规模时，先依赖 `index.md`、Obsidian 搜索和简单 grep；再按规模引入搜索工具

## 如何使用

### 如果你想先理解理念

先读 [docs/core_principles.md](./docs/core_principles.md)。

### 如果你想直接启动一个 LLM Wiki

把 [prompts/llm_wiki_from_scratch.md](./prompts/llm_wiki_from_scratch.md) 发给你的 agent。

### 如果你想采用仓库里的默认实现

把 [profiles/default/SCHEMA.md](./profiles/default/SCHEMA.md) 一起提供给 agent，或者要求它按 default profile 初始化。

### 如果你想判断产物是否靠谱

对照：

- [examples/minimal-vault/README.md](./examples/minimal-vault/README.md)
- [evals/regression_checklist.md](./evals/regression_checklist.md)

做一次 sanity check。

## 适合的场景

- 论文与研究知识库
- LLM / AI 工程知识库
- 产品与竞品研究库
- 长期个人学习与写作积累
- 小团队共享的结构化知识底座

## 这份仓库不主张什么

- 不主张所有领域都照抄同一套目录结构
- 不主张为了“看起来完整”先建空分区、空目录、空页面
- 不主张用越来越重的规则堆出“严谨感”
- 不主张用厚重元数据代替真实理解
- 不主张把聊天记录原样沉淀成正式知识
- 不主张把 self-check 包装成独立审阅

## 一句话总结

这不是一份“帮你生成 wiki 文件夹”的脚手架仓库，而是一份关于如何把 Karpathy 的 LLM Wiki 思路，扩展成一套可长期维护、可追溯、可演化知识系统的设计分享与默认实现。
