# NodeHarvest 公开文档站点

本仓库承载 NodeHarvest 的公开共识、产品状态、执行计划、架构进展和研究摘要：<https://nodeharvest.github.io/>。

## 内容维护

- `index.html`：产品需求与共识首页；
- `docs/product-status-and-plan.md`：当前产品状态与执行计划；
- `docs/usage.md`：面向用户的使用方法（沉淀 harvest / 复用 rehydrate）；
- `docs/*.md`：公开专题文档的 Markdown 内容源；
- `_layouts/document.html`：专题文档的统一 HTML 布局；
- `_config.yml`：GitHub Pages 构建配置。

专题 Markdown 使用 `layout: document` 和固定 `permalink`。提交到 `main` 后，GitHub Pages 会通过 Jekyll 自动生成对应 HTML 页面，因此 Markdown 变更会自动同步到公开页面，无需跨仓库密钥或额外工作流写权限。

> NodeHarvest 主源码仓库目前为私有仓库。需要公开的研究或决策应先整理、脱敏并同步到此处；私有内容不会被自动公开。
