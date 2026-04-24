# Log

## [2026-04-24 09:00] create | initialize minimal example vault

- 受影响文件：`SCHEMA.md`、`index.md`、`log.md`
- 说明：建立最小样例 vault，用于展示默认 profile 的输出形态

## [2026-04-24 09:10] ingest | raw/articles/attention-note.md

- 受影响 raw：`raw/articles/attention-note.md`
- 受影响页面：`W-0001`、`W-0002`
- 说明：从单一原始笔记生成一个 source 页，并沉淀出 attention 的基础概念页

## [2026-04-24 09:18] query-writeback | attention 缩短了序列建模中的信息路径

- 受影响页面：`W-0003`
- 说明：把一次可复用的聊天综合写回为 analysis 页，并保留为 `draft`

## [2026-04-24 09:22] qa | attention-first-ingest

- 受影响文件：`qa-reports/qa-2026-04-24-attention-self-check.md`
- 说明：执行 self-check，确认当前结构可追溯，但不把其视为独立 QA，也不据此把概念页晋升为 `stable`
