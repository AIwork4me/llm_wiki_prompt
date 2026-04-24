---
id: W-0002
title: Attention
type: concept
updated_at: 2026-04-24
summary: Attention 是一种按当前上下文动态读取并汇总相关信息的机制。
source_ids:
  - W-0001
related_ids:
  - W-0003
status: draft
tags:
  - transformer
  - mechanism
---

# Attention

## 一句话定义

Attention 是一种根据当前查询动态选择相关信息并做加权汇总的机制。

## 直觉

它不像固定窗口那样死板地看邻近 token，而是让当前 token 去“向全局找自己现在最需要的信息”。

## 机制或结构

在这份样例来源里，attention 的关键作用是让 token 可以按相关性读取其他 token 的信息。自注意力进一步把这个能力放到同一序列内部，让每个位置都能在一层里访问全局上下文。

## 边界与反例

- attention 本身不提供顺序信息，因此需要额外位置机制
- attention 不是长期记忆的同义词
- 单靠 attention 也不能自动等同于完整推理能力

## 常见混淆

最容易把 attention 混同为“模型已经理解了全部上下文”或“模型拥有记忆”。这份资料只支持它是一种动态读取机制，不支持更强的能力主张。

## 相关页面

- `[[W-0003-attention-shortens-information-paths|attention 缩短信息路径]]`

## 证据与来源

- `[[W-0001-attention-note|Attention Note]]`

## 当前状态说明

当前只有一份短来源，因此这个概念页可以作为可用的工作草稿继续存在，但还不应该因为结构完整就被视为充分沉淀的 `stable` 页面。
