# 02 — 前置条件清单

实施前必须逐项确认。任何一项 ❌ 都会阻塞 demo。

## 2.1 GitHub 侧

| 项 | 要求 | 当前状态 | 备注 |
|---|---|---|---|
| GitHub Enterprise Cloud 订阅 | 必需 | ❓ 待确认 | BYOK 是 Enterprise 专属能力 |
| Copilot Enterprise 席位 | ≥1 个测试用户 | ❓ 待确认 | 普通 Copilot Business 不支持 BYOK |
| Enterprise owner 权限 | 必需（仅配置阶段） | ❌ `turbo998` 是普通用户 | **需协调 owner 协助，或全程截图模拟** |
| 测试 org | 至少 1 个 | ❓ | BYOK 可在 enterprise 级或 org 级配置 |
| VS Code + GitHub Copilot Chat 扩展 | 最新版 | ❓ | 模型选择器需支持自定义模型显示 |

## 2.2 Azure 侧

| 项 | 要求 | 当前状态 | 备注 |
|---|---|---|---|
| Azure 订阅 | 有 owner / contributor | ✅ 现有订阅可用 | |
| Foundry resource (新建) | `cpbyok-foundry` | 📋 待创建 | demo 专用，不复用 `qichen2-7212-resource` |
| Region 选定 | East US 2 / Sweden Central | ⚠️ 待验证 | 必须支持 Anthropic Claude serverless deployment |
| Anthropic 模型 catalog 访问 | 已申请通过 | ⚠️ 需提前申请 | 首次需提交 use-case，~1 工作日审批 |
| Foundry 配额 (Anthropic) | Tier 1+ | ⚠️ 待查 | 与 OpenAI 模型独立池 |
| Service Principal `cpbyok-github-sp` | 待创建 | 📋 | 用于 GitHub BYOK Entra 认证 |
| RBAC 分配 | `Cognitive Services OpenAI User` on Foundry resource | 📋 | 给上面的 SP |
| Log Analytics Workspace (可选) | demo 第 4 段需要 | 📋 | 演示审计闭环 |

## 2.3 网络（可选增强）

| 项 | 要求 | 备注 |
|---|---|---|
| VNet | 用于 Private Endpoint | 仅在演示"流量不出 VNet"卖点时需要 |
| Private DNS Zone | `privatelink.openai.azure.com` 等 | |
| Private Endpoint | 指向 Foundry resource | |

> **Demo MVP 不需要网络隔离**——基础架构跑通后再加。

## 2.4 工具/凭证

- ✅ `az` CLI ≥ 2.85
- ✅ `gh` CLI（已在 Hermes 环境配好 `turbo998` token）
- ✅ VS Code + GitHub Copilot Chat（在客户演示机上）
- 📋 一个 demo 用 GitHub repo（用于在 Copilot Chat 中演示代码上下文）

## 2.5 首要验证项（实施第一步必须先做的 3 件事）

1. **BYOK 是否 GA**：登录 GitHub Enterprise settings → Copilot → 看是否能看到 "Custom models" / "BYOK" 选项；如果在 limited preview，需申请 allowlist
2. **Anthropic Claude 在 Foundry 哪个 region 可部署**：
   ```bash
   az cognitiveservices account list-models --name <any-foundry> --resource-group <rg> | grep -i anthropic
   ```
   或在 Foundry portal Model catalog 筛选 Anthropic
3. **配额是否够 demo**：申请通过后看 quota 面板，至少 100K TPM 才够流畅 demo
