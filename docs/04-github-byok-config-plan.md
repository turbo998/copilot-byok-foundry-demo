# 04 — GitHub Copilot BYOK 配置计划

> ⚠️ **执行人**：需 GitHub **Enterprise owner** 权限。`turbo998` 是普通用户，本步骤需协调 owner 操作，或在 demo 中以**截图模拟**方式呈现。

## 4.1 配置位置（待 GitHub 实际 UI 确认）

预期路径：
- GitHub.com → 头像 → **Your enterprises** → 选择企业 → **Policies** → **Copilot** → **Models** / **Custom models** / **BYOK**

如果 Enterprise 设置中没有此项 → BYOK 可能仍在 limited preview，需联系 GitHub 客户经理申请加入 allowlist。

## 4.2 配置字段（基于公开文档预期）

| 字段 | 值 |
|---|---|
| Provider | `Azure AI Foundry`（GitHub UI 内置选项） |
| Endpoint URL | `https://cpbyok-foundry.services.ai.azure.com` |
| Authentication method | **Entra ID (Service Principal)** ← 推荐 |
| Tenant ID | `<Step 4 输出的 tenant>` |
| Client ID | `<Step 4 输出的 appId>` |
| Client Secret | `<Step 4 输出的 password>` |
| Model mapping | `claude-opus-4-7` → `claude-opus-4-7-byok`（deployment name） |
| Model mapping | `claude-sonnet-4-5` → `claude-sonnet-4-5-byok` |

## 4.3 验证步骤

1. **Enterprise 侧**：保存配置后看是否显示 "Connection test: Success"
2. **Org 侧**：把 BYOK policy 应用到测试 org
3. **用户侧**：
   - 用测试用户登录 VS Code
   - 打开 Copilot Chat → 模型选择器应能看到 `Claude Opus 4.7 (BYOK)` 等条目
   - 发一个测试 prompt
4. **Azure 侧**：在 Foundry metrics 面板看到刚才请求的 token 计数

## 4.4 降级方案（如果 owner 不可协调）

| 场景 | 替代方案 |
|---|---|
| 完全无 Enterprise 权限 | demo 第 2 段用**截图 + 录屏**讲解配置流程 |
| 无法跑通端到端 Copilot Chat 调用 | 用 `curl` 直接打 Foundry endpoint（步骤 3.6）证明"如果 GitHub 配上了，就能这样调"，配合架构图说明 |
| BYOK 还在 preview | demo 强调"GA 后即可启用"，重点讲 Azure 侧已就绪 |

## 4.5 待实施时验证的不确定点

- [ ] GitHub Enterprise UI 实际入口路径
- [ ] 是否真支持 Entra SP 认证（也许只支持 API key）
- [ ] Model mapping 字段名和格式
- [ ] 是否需要在 GitHub 侧上传 Foundry resource 的证书 / 信任配置
- [ ] Copilot Chat 客户端最低版本要求
