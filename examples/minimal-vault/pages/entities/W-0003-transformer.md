---
id: W-0003
title: Transformer
type: entity
updated_at: 2026-04-24
summary: Transformer 是以自注意力为核心通信机制的序列模型架构。
source_ids:
  - W-0001
related_ids:
  - W-0002
tags:
  - model
  - sequence-modeling
---

# Transformer

## 它是什么

在这个样例里，Transformer 被当作一个架构对象来维护：它以 self-attention 作为主要的信息交互机制，而不是依赖 recurrence 逐步传递信息。

## 为什么值得单独成页

因为它是多个概念页的汇聚点。后续只要继续加入序列建模、位置编码、训练效率或长程依赖相关来源，这个页面都会被反复更新。

## 关键事实

- 它用 self-attention 让每个 token 在单层内访问全局上下文
- 去掉 recurrence 使并行训练更容易
- 因为 attention 不自带顺序信息，所以需要额外的位置机制

## 时间线 / 版本 / 状态

这个样例未展开时间线。后续加入更正式来源后，可补充论文时间点、架构变体和相关实现版本。

## 与其他页面的关系

- 机制核心见 `[[W-0002-attention|Attention]]`
- 暂定综合判断见 `[[W-0004-attention-shortens-information-paths|attention 缩短信息路径]]`

## 证据与来源

- `[[W-0001-attention-note|Attention Note]]`

## 仍不确定的地方

- 目前只有一份短来源，不足以比较 Transformer 与其他序列模型的全部优劣
