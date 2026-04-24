---
id: W-0001
title: Attention Note
type: source
updated_at: 2026-04-24
summary: 一份关于 attention、自注意力与 Transformer 工程取舍的简短原始笔记。
source_paths:
  - raw/articles/attention-note.md
related_ids:
  - W-0002
  - W-0003
  - W-0004
tags:
  - sequence-modeling
---

# Attention Note

## 这是什么资料

一份简短的原始笔记，集中描述 attention 的工作方式、自注意力的通信路径优势，以及它与顺序信息、记忆能力之间的边界。

## 核心信息

- attention 会根据当前查询动态地对 token 做加权汇总
- self-attention 让每个 token 在单层内读取全序列信息
- 去掉 recurrence 后，信息传播路径更短，并且更利于并行训练
- attention 本身不编码顺序，需要额外位置机制
- attention 的能力不能直接等同于长期记忆或完整推理

## 对现有 wiki 的影响

这份资料足以支持：

- 新建 `[[W-0002-attention|Attention]]` 概念页
- 新建 `[[W-0003-transformer|Transformer]]` 实体页
- 形成一个暂定分析：`[[W-0004-attention-shortens-information-paths|attention 缩短了序列建模中的信息路径]]`

## 可提炼 / 需更新的页面

- `W-0002` 需要明确区分“动态加权读取”与“记忆”
- `W-0003` 需要明确写出 Transformer 为什么能摆脱 recurrence 作为主要通信路径
- `W-0004` 应继续补更多来源，验证“并行性”与“表达能力”之间的关系

## 原始路径

- `raw/articles/attention-note.md`

## 遗留问题

- 这份资料只有单一来源，还不足以支撑强结论
- 如果后续加入 RNN、Transformer 原论文或综述，应该回头重写 `W-0004`
