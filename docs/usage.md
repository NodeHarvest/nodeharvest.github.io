---
layout: document
title: 使用方法
description: NodeHarvest 面向用户的沉淀与复用——复制一行命令即可使用。
permalink: /documents/usage.html
---

# 使用方法

> 更新日期：2026-07-26

## 沉淀

在项目里做完一段工作后，复制这一行回车：

```bash
npx -y nodeharvest harvest
```

会得到一份精炼的 Markdown 经验文档，记录这次使用了哪些 Skill、为什么用、结果如何。

如果你用 Claude Code、Codex 等工具，并已把聊天记录导出为 Markdown，可以这样：

```bash
npx -y nodeharvest harvest --from chat.md
```

把导出的 Markdown 连同这一行命令一起喂给 Agent，工具会替你把聊天记录里的决策节点和经验节点精炼出来。

## 复用

在另一台设备或另一个工具里想接着上次的路径继续干：

```bash
npx -y nodeharvest rehydrate --from experience.md
```

工具会读取经验文档中记录过的 Skill，把相关能力迁移到当前环境，然后按原本的 Skill Flow 继续工作。

只想看回原来的工作流、不做迁移：

```bash
npx -y nodeharvest rehydrate --from experience.md --mode replay
```

## 速查

| 想做什么 | 命令 |
|---|---|
| 把当前项目沉淀为经验文档 | `npx -y nodeharvest harvest` |
| 把聊天记录沉淀为经验文档 | `npx -y nodeharvest harvest --from chat.md` |
| 在新环境/新设备恢复 Skill 与工作流 | `npx -y nodeharvest rehydrate --from experience.md` |
| 在当前环境只回放工作流 | `npx -y nodeharvest rehydrate --from experience.md --mode replay` |
| 自检扫描能力是否可用 | `npx -y nodeharvest doctor --json` |
| 列出本机已发现能力 | `npx -y nodeharvest scan --json` |

## 错误与边界

- 经验文档已存在时，新内容写到旁边，不覆盖。
- 缺少经验文档或基础层不可用时，`rehydrate` 拒绝执行，不静默回退。
