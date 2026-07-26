---
layout: document
title: 使用方法
description: NodeHarvest 面向用户的两条链路：把今天的工作沉淀成精炼文档；换设备、换工具时一键恢复。
permalink: /documents/usage.html
---

# 使用方法

> 状态：MVP 共识  
> 更新日期：2026-07-26

## 这份文档面向谁

面向**使用者**，不讲内部实现。复制下面的一行命令即可使用；不要在文档中寻找"为什么这样做"——那是实现层的事。

## 形态与分发

- 形态：CLI 工具
- 分发：通过 npm 安装/执行
- 唯一用户入口：`nodeharvest`
- 底层扫描依赖：HarnessKit 1.8.x（首次使用时自动准备，无需用户预装）

## 沉淀：把今天的工作存成一份精炼文档

### 方式 1：项目里直接沉淀

```bash
npx -y nodeharvest harvest
```

复制一行，回车即可。

适用场景：在某个项目里做完工作后，想把这次的工作留一份精炼的记录。

### 方式 2：把聊天记录喂给它

```bash
npx -y nodeharvest harvest --from <chat-export>.md
```

适用场景：你用 Claude Code、Codex、Cursor 等开发工具工作过，并已经导出了 Markdown 聊天记录。

聊天记录怎么导出：开发工具社区都有开源方案，你是开发者就能找到。

怎么用：把"导出的聊天记录 + 上面那行命令 + 示例 prompt"一起交给 NodeHarvest，工具会自己分析。

## 复用：换设备、换工具也能继续

```bash
# 在新设备/新工具上恢复
npx -y nodeharvest rehydrate --from <experience>.md

# 在当前环境只回放工作流（不迁移）
npx -y nodeharvest rehydrate --from <experience>.md --mode replay
```

适用场景：

- 在 NodeHarvest 里做了一份很棒的项目工作，想换到另一个工具里继续
- 换了一台设备，想把工作环境拉回来
- 想让另一台机器按原来用过的 Skill 继续干

底层逻辑（用户无需关心）：经验文档里记录了"用过的 Skill 清单"，NodeHarvest 会联动基础层（HarnessKit）把这些相关 Skill 迁移过去；完成后按原本记录的 Skill Flow 继续工作，**不踩重复的坑**。

## 速查表

| 想做什么 | 命令 |
|---|---|
| 把当前项目沉淀为经验文档 | `npx -y nodeharvest harvest` |
| 把聊天记录沉淀为经验文档 | `npx -y nodeharvest harvest --from chat.md` |
| 在新环境/新设备恢复 Skill 与工作流 | `npx -y nodeharvest rehydrate --from experience.md` |
| 在当前环境只回放工作流（不迁移） | `npx -y nodeharvest rehydrate --from experience.md --mode replay` |
| 自检扫描能力是否可用 | `npx -y nodeharvest doctor --json` |
| 列出本机已发现能力 | `npx -y nodeharvest scan --json` |

## 哪些是用户需要知道的边界

- 沉淀出来的文档是**精炼的**，不是聊天回放——只保留"做了什么、为什么、结果如何"。
- MVP 阶段先做 Skill 维度；通了之后其他工具（CLI / MCP / Hook 等）也按同样方式工作。
- 经验文档记录了"用过的 Skill 清单"，是复用链路成立的前提。
- 复用时不修改用户 shell 配置，不覆盖系统已有同名 Skill，不动你的 git 历史。

## 错误时怎么知道发生了什么

- 命令不工作时，复制行尾加 `--json` 可以得到机器可读的错误码与修复建议。
- 例如 `nodeharvest doctor --json` 失败时会告诉你"原因 + 怎么修"。

## 当前位置

- `harvest` / `rehydrate` 命令正在实施中；本页面记录的是面向用户的最终接口形态。
- 实际可用命令以 NodeHarvest 官方发版为准。

## 相关文档

- [产品状态与执行计划]({{ '/documents/product-status-and-plan.html' | relative_url }})
- [HarnessKit 基座与无感集成进展]({{ '/documents/harnesskit-foundation.html' | relative_url }})
- [三人协作与另外两位成员贡献指南]({{ '/documents/three-person-contribution-guide.html' | relative_url }})
