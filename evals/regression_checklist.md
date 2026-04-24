# Regression Checklist

每次修改 prompt、schema 或模板后，都至少过一遍这份清单。它的目的不是追求“每次都完全一样”，而是避免仓库重新滑回“越写越重、越写越假精确”的老路。

## 1. 主 prompt 仍然是启动任务，而不是超级手册

- `prompts/llm_wiki_from_scratch.md` 应该能被快速读完，并把实现细节留给 schema/profile
- prompt 应强调“模式 + 协作实例化”，而不是把所有默认值写死

## 2. 模式与实现仍然分层

- 抽象理念主要放在 `docs/core_principles.md`
- 默认实现主要放在 `profiles/default/SCHEMA.md`
- 主 prompt 不应重新复制整份 schema

## 3. 元数据仍然保持最小

- 必需字段仍然只有少数几个
- 页面不应被空数组和空字段填满
- 不应重新引入 `child_ids`
- 不应重新引入没有明确收益的伪精确字段

## 4. 页面类型要么真正不同，要么应该被合并

- `entity` 和 `concept` 模板必须能看出职责差异
- 如果后续演化中两者再次变成同构页面，应考虑合并而不是继续保留假差异

## 5. `query -> writeback` 仍然有晋升门槛

- 新的综合判断应先落到 `draft`
- 只有通过来源和结构检查后才晋升
- 不能把聊天记录原样写进正式页面

## 6. QA 级别仍然诚实

- `self_check` 必须明确说明不是独立 QA
- 高风险内容不能只靠同上下文自检就直接包装成“稳定知识”

## 7. `index.md` 与 `log.md` 的定位没有漂移

- `index.md` 仍然被当作导航派生产物
- `log.md` 仍然是 append-only 审计文件
- 页面内容才是知识真相源

## 8. 规模扩展路径仍然存在

- 默认流程仍然先依赖 index、搜索和简单工具
- 仓库没有把重型搜索基础设施变成初始化硬依赖

## 9. 最小样例仍然合理

对照 [examples/minimal-vault](../examples/minimal-vault/README.md) 检查：

- 一个简单来源能否产生一个 `source` 页面和少量高信号知识页
- analysis 页是否仍以 `draft` 展示未完全验证的综合
- QA 报告是否诚实写出局限

## 10. README、prompt、schema 三者仍然一致

- README 不应承诺 prompt 或 schema 做不到的事
- schema 不应重新引入 README 已明确移除的重规则
- 三者对“真相源、草稿门槛、QA 区分”的表述应一致
