---
layout: document
title: HarnessKit 基座与无感集成进展
description: NodeHarvest 对 HarnessKit v1.8.0 的源码审阅、架构决策、无感接入实现与后续执行计划。
permalink: /documents/harnesskit-foundation.html
---

# HarnessKit 基座与无感集成进展

> 状态：实施中<br>
> 更新日期：2026-07-26<br>
> 对应阶段：Scan 基座 → Harvest → Invoke

## 摘要

NodeHarvest 已完成第一阶段的架构收敛、HarnessKit v1.8.0 源码审阅和首批无感集成实现。

当前明确的产品与工程方向是：

- NodeHarvest 是面向 Agent 的 **CLI 工具**，不是 GUI 或独立 Agent；
- 整体采用 **Scan → Harvest → Invoke** 三层架构；
- HarnessKit 作为扫描与能力管理 provider，NodeHarvest 聚焦经验沉淀、能力匹配和结果回流；
- 用户只安装 NodeHarvest，扫描 provider 由 NodeHarvest 自动准备、校验并接入；
- 扫描层发生故障时，不能让 Harvest 与 Invoke 无条件误用错误或过期数据；
- 产品决策、研究证据、计划、验证结果和重要问题必须进入项目仓库并形成可审阅提交。

## 一、架构结论

NodeHarvest 采用 **托管 HarnessKit provider + 独立进程 + 版本化 JSON 契约**。

```text
nodeharvest scan
  ├─ 使用显式指定且兼容的 hk
  ├─ 使用系统中已有且兼容的 hk
  └─ 自动下载固定版本的官方 hk
          ↓ SHA-256 校验
     私有版本目录原子安装
          ↓ 版本与契约校验
     hk list --json
          ↓ 严格适配
     CapabilityInventory
          ↓
     Harvest / Index / Invoke
```

这条路径保留了 HarnessKit 的进程隔离和独立升级能力，同时让用户获得单一安装入口。NodeHarvest 不合并上游 Rust workspace，也不复制其 GUI、品牌或受限美术资源。

## 二、HarnessKit 源码研究

本轮研究固定在以下上游基线：

- 上游仓库：`RealZST/HarnessKit`
- 版本：`v1.8.0`
- 提交：`db2d8a8`
- Git 历史：627 个提交、23 个版本标签
- 审阅账本：249 个受跟踪工程源码与配置文件

审阅覆盖 Rust workspace、11 个 Agent 适配器、扫描器、统一模型、SQLite 存储、服务层、管理与部署、审计、Marketplace、Kit、CLI、Web、Tauri 桌面端、安装脚本、更新机制和发布流程。

本次结论不是根据 README 或经验推断得出；相关文件范围、摘要和验证限制均已记录在项目仓库的源码审阅报告与逐文件账本中。

## 三、源码核验后的关键修正

### 1. `hk list --json` 不是绝对零写入

该命令不会在扫描过程中主动改写 Agent 配置，但 HarnessKit 会创建或迁移自己的元数据目录与 SQLite 数据库，并同步扫描结果。因此准确表述应为：

> 对 Agent 配置只读，但会维护 HarnessKit 自身元数据。

### 2. `list` 契约适合能力清单，不等于完整数据源

HarnessKit v1.8.0 的 `list --json` 提供分组后的名称、类型、Agent、Pack、信任分数、启用与状态信息，适合建立 NodeHarvest 能力清单；但它不包含完整路径、来源、作用域、权限与审计发现。

NodeHarvest 不读取 HarnessKit 私有 SQLite 模式，也不解析终端表格文本。后续字段应通过稳定 JSON 契约或上游贡献补丁获得。

### 3. 上游安装脚本不适合作为无感集成基础

上游安装脚本会追随最新版本并直接覆盖目标二进制，脚本自身没有执行完整性校验。NodeHarvest 因此改用固定版本、固定平台资产摘要、临时下载和原子安装，拒绝使用未验证文件。

### 4. 扫描自动化不代表写操作自动化

HarnessKit 的安装、启停、删除和配置变更可能影响多个 Agent。NodeHarvest 首阶段只开放诊断和扫描入口；未来任何写能力都必须先预览、再明确确认，并具备回滚与审计路径。

## 四、已实现的无感接入

首批实现已覆盖：

- 用户显式 provider 路径优先；
- 系统兼容 `hk` 自动复用；
- 无兼容版本时自动识别系统与架构；
- 支持 macOS arm64/x64、Linux arm64/x64、Windows x64；
- 固定 HarnessKit v1.8.0 官方资产与 SHA-256；
- 临时文件下载、完整性校验和原子安装；
- 不修改 shell 配置，不覆盖系统已有 `hk`；
- 不支持平台提前拒绝；
- 摘要不匹配时拒绝落盘；
- 未知 JSON 契约版本拒绝解析。

## 五、验证结果

已完成的 NodeHarvest 验证包括：

- 自动化测试：4 项通过；
- 发布包内容检查：通过；
- Linux x64 官方二进制真实下载：通过；
- 官方 SHA-256 真实校验：通过；
- `doctor --json` 正确识别 HarnessKit 1.8.0；
- `scan --json` 返回 NodeHarvest schema 1 / HarnessKit contract 1；
- 未发现能力时返回有效空清单，不误报为扫描故障。

当前验证环境没有 Rust `cargo`，且未安装 HarnessKit 前端依赖，因此没有把上游 Rust/前端测试描述为已复跑通过。该限制已写入研究记录，后续应由固定 toolchain 的 CI 补齐。

## 六、供应链与合规边界

HarnessKit 源代码采用 Apache License 2.0。NodeHarvest 当前遵循以下边界：

- 保留并随分发提供完整许可证与必要归属；
- 固定并校验官方发布资产摘要；
- 不复用 HarnessKit Logo、图标、mascot、商标或明确排除的美术资源；
- 不把下载的第三方二进制直接提交到源码仓库；
- provider 升级必须包含源码差异审阅、契约测试、摘要更新和回滚说明。

## 七、扫描层故障隔离与安装恢复

扫描是第二、第三层的基础，因此不能把 provider 的一次故障直接传播成全链路不可用或错误推荐。当前已经完成：

1. provider 状态机：`ready / unavailable / incompatible / corrupt`；
2. 结构化错误分类与可操作诊断；
3. 最后一次成功扫描快照、生成时间与过期标识；
4. Harvest/Invoke 的数据质量标记：新鲜、过期、不可用；
5. 安装锁和并发启动保护；
6. 下载失败清理、损坏隔离、版本保留与自动回滚；
7. 来源、版本、平台、摘要和 Apache-2.0 许可证随 provider 落盘。

降级原则是：可以使用明确标注为过期的最后成功结果，但不能静默把旧数据当作新扫描结果；没有可信快照时，上层必须得到明确的不可用状态。新版本准备失败时，只会回滚到来源记录完整、兼容且摘要仍匹配的已验证版本。

当前剩余工作是 macOS、Linux、Windows 的真实平台矩阵验证。Linux 环境已完成故障注入；macOS 与 Windows 尚需对应系统验收。

## 八、MVP 执行顺序

### 阶段 1：可靠、无感的 Scan 基座

provider 状态机、缓存与过期标识、离线/损坏/版本不兼容降级、安装恢复、回滚和许可证落盘已完成首版；完成真实多平台验收后进入维护状态。

### 阶段 2：最小 Harvest 模型

定义 Work、Skill、Result/Experience 三类节点及关系，同时提供简洁 Markdown 表达和可版本化 JSON，并使用真实已结束项目验证经验提取质量。

### 阶段 3：最小 Invoke 闭环

实现“任务 → 匹配经验 → 推荐或调用 → 记录结果 → 更新经验”，输出排序依据和历史证据，不做不可解释的静默执行。

### 阶段 4：受控能力管理

在稳定 JSON 契约、预览、确认、回滚和审计机制到位后，再逐步开放 HarnessKit 的写能力。

## 九、仓库记录政策

从 2026-07-26 起，NodeHarvest 执行以下协作约束：

- 所有产品与工程决策必须形成文档；
- 所有研究必须记录固定版本、提交、范围和可复核证据；
- 实现、测试、架构、合规和运行说明应同批更新；
- 每个可审阅工作单元必须提交并推送到项目组织仓库；
- 不在仓库、日志或公开文档中写入密钥、令牌、个人路径或受限资源；
- 标记任务完成、关闭产品问题或记录治理决策前，仍需取得明确确认。

## 十、当前代码状态

当前功能批次位于分支中，已形成以下可追溯提交：

- `1a4bde4`：搭建 CLI 与 HarnessKit 只读适配基座；
- `39bc453`：记录 HarnessKit 源码审阅与集成决策；
- `339ff9e`：自动引导并校验 HarnessKit provider；
- `18297ff`：增加扫描诊断与安全降级；
- `e84649d`：增加 provider 安装恢复与回滚。

主分支尚未修改，拉取请求与合并仍应经过正常评审流程。
