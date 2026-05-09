# 06 — 风险与待验证事项

## 6.1 阻塞性风险（必须实施前解决）

| # | 风险 | 影响 | 缓解 |
|---|---|---|---|
| R1 | **GitHub Copilot BYOK 仍在 limited preview，无法启用** | 整个场景跑不通 | 提前联系 GitHub 客户经理申请；准备"仅 Azure 侧 + curl 调用"的降级 demo |
| R2 | **`turbo998` 无 Enterprise owner 权限** | 段 2 无法实操 | 协调真实 owner；或纯截图模拟 |
| R3 | **Anthropic Claude 在选定 region 无 serverless 部署** | 模型部署不出来 | 实施前先 `az` 查 region 可用性，必要时换 Sweden Central |
| R4 | **首次 Anthropic 模型访问需 use-case 审批 1-2 天** | demo 临时准备来不及 | 提前 1 周申请 |
| R5 | **配额不足** | demo 中卡顿/限流 | 申请 Tier 1 以上；demo 前用 curl 预热验证 |

## 6.2 中等风险

| # | 风险 | 影响 | 缓解 |
|---|---|---|---|
| R6 | GitHub Copilot 客户端版本旧，模型选择器看不到 BYOK 模型 | 段 3 失败 | 演示机提前升级到最新 stable |
| R7 | Foundry endpoint 路径/api-version 与文档不一致 | curl 验证失败 | 用 portal "View code" 自动生成的 snippet 兜底 |
| R8 | Entra SP 认证不被 GitHub BYOK 支持，只能用 API key | 卖点削弱 | 备选 API key 方案，但强调"建议生产用 SP" |
| R9 | Diagnostic Logs 延迟（5-10min）在 demo 中显得"没数据" | 段 4 尴尬 | 提前 10 min 预热调用，让日志先到 |

## 6.3 待验证开放问题

- [ ] **Q1**：GitHub BYOK 当前 GA / preview 状态？官方文档链接？
- [ ] **Q2**：BYOK 是否支持 Anthropic 模型，还是仅 OpenAI/Azure OpenAI？（关键，可能整个场景不成立）
- [ ] **Q3**：GitHub 路由层调用 Foundry 时用的具体 API 协议（OpenAI 兼容 vs Anthropic 原生）？
- [ ] **Q4**：BYOK 配额扣减发生在 GitHub 侧还是 Azure 侧？是否双重计费？
- [ ] **Q5**：BYOK 模式下 Copilot 的代码补全（Ghost text）是否也走自定义模型，还是仅 Chat？
- [ ] **Q6**：合规问题——GitHub 路由层是否会临时缓存 prompt？官方声明在哪？

> ⚠️ **Q2 是最大的不确定性**。如果 GitHub BYOK 目前只接 OpenAI 类模型，本 demo 需要改为"用 Foundry 上的 OpenAI 模型 BYOK"或"等 GitHub 支持 Anthropic"。实施第一步必须验证这个。

## 6.4 降级方案矩阵

| 触发条件 | 降级到 |
|---|---|
| BYOK 不支持 Anthropic | 改用 Foundry 上的 GPT-4.x BYOK，文章里加一句"Anthropic 支持已在 roadmap" |
| BYOK 完全不可用 | 改 demo 场景为：Claude Code CLI + LiteLLM 代理 → Foundry（场景 A） |
| Owner 权限不可得 | 段 2 全程截图，重点放段 1/3/4 实操 |
| 时间不够申请 Anthropic 访问 | 先用 OpenAI 模型完成 demo 框架，Anthropic 切换留作后续迭代 |
