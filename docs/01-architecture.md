# 01 — 架构设计

## 1.1 整体架构（文字版）

```
开发者 VS Code
     │ (Copilot Chat: 模型选择器选 "Claude Opus 4.7 (BYOK)")
     ▼
GitHub Copilot 服务（路由层，不持久化 prompt）
     │ (按 Enterprise BYOK 配置路由 + 客户凭证)
     ▼ HTTPS + Entra ID / API Key
┌─────────────────────────────────────────────┐
│  Azure 客户租户                              │
│  ┌──────────────────────────────────────┐   │
│  │ Resource Group: cpbyok-demo-rg       │   │
│  │  ┌────────────────────────────────┐  │   │
│  │  │ Azure AI Foundry resource      │  │   │
│  │  │   cpbyok-foundry               │  │   │
│  │  │   ├─ deployment: claude-opus-4-7-byok │
│  │  │   └─ deployment: claude-sonnet-4-5-byok │
│  │  └────────────────────────────────┘  │   │
│  │  ┌────────────────────────────────┐  │   │
│  │  │ Service Principal              │  │   │
│  │  │   cpbyok-github-sp             │  │   │
│  │  │   role: Cognitive Services     │  │   │
│  │  │         OpenAI User            │  │   │
│  │  └────────────────────────────────┘  │   │
│  │  ┌────────────────────────────────┐  │   │
│  │  │ Log Analytics Workspace (可选) │  │   │
│  │  │   ← Diagnostic Settings        │  │   │
│  │  └────────────────────────────────┘  │   │
│  │  ┌────────────────────────────────┐  │   │
│  │  │ Private Endpoint (可选增强)    │  │   │
│  │  └────────────────────────────────┘  │   │
│  └──────────────────────────────────────┘   │
└─────────────────────────────────────────────┘
```

## 1.2 AWS Bedrock vs Azure Foundry 对应关系

| AWS 原方案 | Azure 对应 | 备注 |
|---|---|---|
| Amazon Bedrock | Azure AI Foundry (Foundry Models) | Anthropic 模型已在 Foundry catalog |
| Inference Profile ID (`us.anthropic.claude-...`) | Foundry **deployment name**（自定义） | Azure 不需要"跨区域 profile"，由 SKU 决定 |
| IAM Policy + Role | Azure **RBAC role assignment** | `Cognitive Services OpenAI User` |
| AWS SDK 凭证链 | **DefaultAzureCredential** / SP / Managed Identity | |
| Bedrock VPC Endpoint | Foundry **Private Endpoint** + Private DNS | Azure 端粒度更细 |
| CloudTrail | Azure **Diagnostic Settings → Log Analytics** | |

## 1.3 数据流（一次 Copilot Chat 请求）

1. 开发者在 VS Code Copilot Chat 输入问题，模型选 Claude Opus 4.7
2. Copilot 客户端把请求发到 GitHub Copilot 服务
3. GitHub 路由层根据 Enterprise BYOK 配置：
   - 拿出客户配置的 Foundry endpoint URL + 凭证
   - 转发请求到 `https://cpbyok-foundry.services.ai.azure.com/...`
4. Foundry 用 deployment name 路由到 Anthropic Claude，调用推理
5. 响应原路返回；同时 Foundry 把调用元数据写入客户的 Log Analytics
6. token 消耗计入客户 Azure 订阅账单

## 1.4 安全/合规要点

- **凭证存储**：GitHub 侧用 Entra ID SP（client_id + secret）优于静态 API key——可独立轮换、可审计、可禁用
- **最小权限**：SP 只授予 Foundry resource scope 上的 `Cognitive Services OpenAI User`，不给订阅级权限
- **数据驻留**：Prompt/Completion 都在客户 Azure region 内处理；**但请求路径会经过 GitHub 服务做路由**——合规团队可能追问，需准备 GitHub 官方 BYOK 数据声明
- **审计**：Foundry diagnostic logs 可记录调用次数、token 量、调用者；prompt 内容默认不落盘（如需可启用 content logging）

## 1.5 与原 AWS 方案的差异化卖点

1. **Managed Identity 替代静态密钥**——VM/AKS 上部署 Copilot Workspace 时零密钥
2. **Private Endpoint + VNet 注入**——比 Bedrock VPC Endpoint 更细粒度
3. **Foundry Agent Service**——后续可演进为托管 agent runtime
4. **配额 Tier 1-6**——Anthropic 模型在 Foundry 上有独立配额池，与 OpenAI 模型分开管理
