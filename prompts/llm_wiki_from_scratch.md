# AutoClaw / OpenClaw：初始化一个可长期维护的 LLM Wiki

请在**当前工作目录**中，非破坏式初始化一个可长期维护的 LLM Wiki。

当前工作目录就是目标 vault 根目录。不要额外创建 `llm-wiki/`、`wiki/` 或其他包裹目录。

这是一份**启动任务**，不是一部固定宪法。请用 Karpathy 的 LLM Wiki 模式工作：

- `raw/` 一类目录是不可变事实层
- wiki 页面层是持续编译、持续重写的知识层
- schema 是执行协议，应该和用户一起演化，而不是越写越僵

## 本次任务目标

初始化一套后续 agent 能继续接手维护的 wiki 工作流。

质量标准不是“字段越多越好”，而是：

- 准确
- 压缩
- 可追溯
- 可导航
- 可演化

## 工作顺序

1. 先审计当前目录已有文件和目录。
2. 保留与本任务无关的现有内容，不要随意删除、覆盖或移动。
3. 如果当前仓库里已有 `docs/core_principles.md`、`profiles/default/SCHEMA.md` 或类似文件，先阅读它们。
4. 把已有 profile 当成默认实现，而不是不可修改的法律；必要时按当前领域做本地化调整。
5. 如果目标文件已存在，优先理解和修复，而不是盲目重写。

## 初始化范围

本次只做“骨架和协议初始化”。

可以创建或修复：

- `SCHEMA.md`
- `README.md`
- `index.md`
- `log.md`
- `_state/id-counter.md`
- 当前任务真正需要的目录
- 当前任务真正需要的模板

不要凭空创建示例知识页，也不要 ingest 任意 raw 资料。目录、页面类型、frontmatter、ID 分配、ingest、query、lint、QA、writeback 等具体规则，以当前仓库选用的 schema/profile 为准。

如果没有可用 profile，请写一份小而明确的本地 `SCHEMA.md`，只覆盖当前任务真正需要的最小协议；不要发明一套厚重元数据系统。

## 红线

1. 绝不修改 `raw/` 中的原始资料。
2. 不凭空制造示例知识页。
3. 不用元数据伪装真实理解。
4. 不为了凑数创建无意义目录、空页面、空字段，也不保留空的 index 分区。
5. 不把 query 聊天记录原样塞进正式页面。
6. 不把 self-check 写成独立 QA。
7. 不把“当前可用”误标成 `stable`；`stable` 必须遵守当前 schema/profile 的晋升门槛。

## 完成前自检

结束前请检查并修复：

1. 当前任务真正需要的骨架是否存在
2. `SCHEMA.md`、`README.md`、`index.md`、`log.md`、模板和 `_state/id-counter.md` 是否能正常渲染或读取
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
