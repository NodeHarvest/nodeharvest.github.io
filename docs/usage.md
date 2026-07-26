---
layout: document
title: 使用方法
description: NodeHarvest 面向用户的沉淀（harvest）与复用（rehydrate）两条链路与一行命令。
permalink: /documents/usage.html
---

# 使用方法

> 状态：MVP 共识  
> 更新日期：2026-07-26

## 设计原则

- **精炼，不冗余**：只提取决策节点与经验节点，不复述整段聊天或代码改动。
- **一行命令**：复制一行即可使用，不要求阅读源码。
- **Skill 优先**：MVP 先把 Skill 维度打透，再扩到其他工具。
- **不抢权威**：沉淀是参考输入，最终决策仍由人确认。

## 形态与分发

- 形态：CLI 工具
- 分发：通过 npm 安装/执行
- 唯一用户入口：`nodeharvest`
- 底层扫描依赖：HarnessKit 1.8.x（首次使用时自动准备，无需用户预装）

## 两条用户链路

### 沉淀（harvest）

把当前项目里"做了什么、用了什么 Skill、为什么、结果如何"沉淀成一份可复用的经验 Markdown 文档。

```bash
# 从当前工作目录直接沉淀
npx -y nodeharvest harvest

# 从聊天记录沉淀
npx -y nodeharvest harvest --from chat-export.md
```

输出约定：

- 一份 Markdown 文件，包含 3 类节点：Work（工作）、Skill（技能）、Experience（经验）
- 每个节点有唯一 id、来源引用、时间戳
- 文档头部列出本次使用的 Skill 清单
- 总长度按"精炼"原则裁剪，不做聊天回放

### 复用（rehydrate）

把沉淀下来的经验 Markdown 文档，连同基础层（HarnessKit）的一键迁移能力，恢复出可继续工作的环境与 Skill Flow。

`rehydrate` 一词由 `re-` + `hydrate` 组合而成：`hydrate` 在云原生/Docker 生态中表示"把声明恢复成可运行实例"，对应"换设备 / 换工具后恢复 Skill 环境"；`re-` 强调"按原路径再走一遍"，对应"沿用沉淀文档里的 Skill Flow"。一个词同时覆盖"恢复"与"回放"两层含义。

```bash
# 在新环境/新设备直接恢复
npx -y nodeharvest rehydrate --from experience.md

# 在当前环境只回放工作流（不迁移）
npx -y nodeharvest rehydrate --from experience.md --mode replay
```

前置条件：

- 当前环境已具备 Node.js 20+
- 首次使用时会自动拉取并校验 HarnessKit 1.8.x
- 迁移过程只读经验文档与 HarnessKit 仓库，不修改用户仓库的 git 历史

## 速查表

| 想做什么 | 命令 |
|---|---|
| 把当前项目沉淀为经验文档 | `npx -y nodeharvest harvest` |
| 把聊天记录沉淀为经验文档 | `npx -y nodeharvest harvest --from chat.md` |
| 在新环境/新设备恢复 Skill 与工作流 | `npx -y nodeharvest rehydrate --from experience.md` |
| 在当前环境只回放工作流（不迁移） | `npx -y nodeharvest rehydrate --from experience.md --mode replay` |
| 自检扫描能力是否可用 | `npx -y nodeharvest doctor --json` |
| 列出本机已发现能力 | `npx -y nodeharvest scan --json` |

## 错误与边界

- `harvest` 解析失败时返回稳定错误码与可读建议；不输出半成品文档覆盖原文件
- `rehydrate` 在缺少经验文档、HarnessKit 不可用或来源不完整时拒绝执行
- 所有写操作（生成经验文档、安装/迁移 Skill）默认要求目标路径可写；冲突时优先保留既有文件并提示

## 相关文档

- [产品状态与执行计划]({{ '/documents/product-status-and-plan.html' | relative_url }})
- [HarnessKit 基座与无感集成进展]({{ '/documents/harnesskit-foundation.html' | relative_url }})
- [三人协作与另外两位成员贡献指南]({{ '/documents/three-person-contribution-guide.html' | relative_url }})
