# llm_wiki_prompt

面向 AutoClaw / OpenClaw 的 LLM Wiki 提示词仓库。

核心灵感来自 Karpathy 的 [LLM Wiki gist](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f)：  
不要把知识管理做成“每次提问临时检索 raw 的 RAG”，而要做成“在 ingest 阶段持续编译知识、后续 query 优先使用 wiki 的知识层”。

## 这个仓库包含什么

- [prompts/openclaw_llm_wiki_from_scratch.md](./prompts/openclaw_llm_wiki_from_scratch.md)
  OpenClaw 版从零初始化提示词，适合先搭骨架、再逐步 ingest。
- [prompts/autoclaw_llm_wiki_from_scratch.md](./prompts/autoclaw_llm_wiki_from_scratch.md)
  AutoClaw 版从零初始化提示词，强化了自治执行、自检和 QA fallback。
- [articles/wechat_hardcore.md](./articles/wechat_hardcore.md)
  2000 字以内的微信技术文章：《在AutoClaw中搭建会进化的 Karpathy  LLM Wiki 》。

## 设计原则

这些不是“写提示词时顺手加的限制”，而是实战后证明必须前置的协议：

1. 当前工作目录就是 vault 根目录，禁止再套一层 `llm-wiki/`。
2. `raw/` 是不可变事实层，`pages/` 才是知识编译层。
3. 模板不是参考，而是强制起点；新页和重写页都必须从 `_templates/` 开始。
4. `source_ids` 负责来源追溯，`parent_ids / child_ids` 只负责真实层级，不能拿 source 页去挂一串“派生子页”。
5. 冲突必须结构化：`status=contested` 时必须有 `conflict_ids`，Lint 也要检查它。
6. QA 不能只写“未执行”；没有独立 agent 时，也必须留下 self-check fallback 报告。
7. LLM Wiki 允许少量后验背景补充，但 QA 不能把它误判成“完全来自 raw”。

## OpenClaw 和 AutoClaw 的区别

### OpenClaw

- 更适合交互式搭骨架和多轮修正。
- 提示词更强调“当前工作目录”“非破坏式修复”“模板和 schema 落地”。
- 适合先把 wiki 协议写稳，再手动驱动 ingest。

### AutoClaw

- 更适合自治执行 inspect -> scaffold -> self-check -> fallback QA 的闭环。
- 提示词更强调阶段化执行、最终自检和日志/QA 工件必须落盘。
- 适合持续 ingest、周期性 lint 和自动回写。

## 推荐用法

1. 新建一个空目录，作为 LLM Wiki 的 vault 根目录。
2. 把对应提示词完整粘给 AutoClaw 或 OpenClaw。
3. 等骨架生成后，放入第一份 raw 资料。
4. 再告诉 agent：`请 ingest raw/xxx.pdf`。
5. 检查 `index.md`、`log.md`、`qa-reports/` 是否同步更新。

## 最小验收标准

一个可用的 LLM Wiki，至少要满足：

- 有 `raw/ pages/ SCHEMA.md index.md log.md qa-reports/ _templates/`
- `SCHEMA.md` 是真正的执行协议，不是介绍文章
- source 追溯、层级关系、冲突关系、QA fallback 都有明确定义
- 第一次 ingest 后，能留下页面、索引、日志和 QA 工件

## 适合什么场景

- 论文知识库
- LLM / AI 工程知识库
- 产品 / 竞品研究库
- 个人技术长期积累
- 小团队共享的结构化知识底座
