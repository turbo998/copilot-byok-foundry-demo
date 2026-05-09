# 03 — Azure 侧搭建计划

> 实施时按顺序执行。所有命令暂以伪代码形式列出，**实施时再填入具体参数**。

## 3.1 命名约定

| 资源 | 名称 |
|---|---|
| Resource Group | `cpbyok-demo-rg` |
| Location | `eastus2`（待 02 验证后定稿） |
| Foundry resource | `cpbyok-foundry` |
| Claude Opus deployment | `claude-opus-4-7-byok` |
| Claude Sonnet deployment | `claude-sonnet-4-5-byok` |
| Service Principal | `cpbyok-github-sp` |
| Log Analytics Workspace | `cpbyok-law` |
| Diagnostic Setting | `cpbyok-diag-to-law` |

## 3.2 步骤

### Step 1: 创建 Resource Group
```bash
az group create -n cpbyok-demo-rg -l eastus2
```

### Step 2: 创建 Foundry resource
```bash
az cognitiveservices account create \
  -n cpbyok-foundry \
  -g cpbyok-demo-rg \
  -l eastus2 \
  --kind AIServices \
  --sku S0 \
  --custom-domain cpbyok-foundry
```
> `--custom-domain` 是 Entra ID 认证必需。

### Step 3: 部署 Claude 模型
```bash
# Opus 4.7
az cognitiveservices account deployment create \
  -n cpbyok-foundry -g cpbyok-demo-rg \
  --deployment-name claude-opus-4-7-byok \
  --model-name <实际 model name，待 catalog 查> \
  --model-version <版本> \
  --model-format Anthropic \
  --sku-capacity 100 --sku-name GlobalStandard

# Sonnet 4.5（同上 model 换名字）
```
⚠️ 实施时通过 Foundry portal "Deploy" 按钮拿到准确的 model-name/version/SKU 组合更稳。

### Step 4: 创建 Service Principal
```bash
az ad sp create-for-rbac \
  --name cpbyok-github-sp \
  --role "Cognitive Services OpenAI User" \
  --scopes /subscriptions/<sub>/resourceGroups/cpbyok-demo-rg/providers/Microsoft.CognitiveServices/accounts/cpbyok-foundry
```
保存输出的 `appId` / `password` / `tenant` —— GitHub BYOK 配置需要。

### Step 5: 创建 Log Analytics（可选 demo 第 4 段）
```bash
az monitor log-analytics workspace create \
  -g cpbyok-demo-rg -n cpbyok-law -l eastus2

# Diagnostic 配到 Foundry
az monitor diagnostic-settings create \
  -n cpbyok-diag-to-law \
  --resource $(az cognitiveservices account show -n cpbyok-foundry -g cpbyok-demo-rg --query id -o tsv) \
  --workspace cpbyok-law \
  --logs '[{"category":"Audit","enabled":true},{"category":"RequestResponse","enabled":true}]' \
  --metrics '[{"category":"AllMetrics","enabled":true}]'
```

### Step 6: 验证 Foundry endpoint 可用
```bash
ENDPOINT="https://cpbyok-foundry.services.ai.azure.com"
TOKEN=$(az account get-access-token --resource https://cognitiveservices.azure.com --query accessToken -o tsv)

curl -X POST "$ENDPOINT/openai/deployments/claude-opus-4-7-byok/chat/completions?api-version=2024-12-01-preview" \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"messages":[{"role":"user","content":"Say hi from Claude on Foundry"}],"max_tokens":50}'
```
> Anthropic 在 Foundry 上的实际 API 路径/版本以 portal "View code" 给出的为准。

## 3.3 一键销毁

```bash
az group delete -n cpbyok-demo-rg --yes --no-wait
az ad sp delete --id <appId>
```

## 3.4 待实施时填空的占位

- [ ] 准确的 Anthropic model name + version
- [ ] Foundry API path（`/openai/deployments/...` vs `/anthropic/...`，以 portal 为准）
- [ ] api-version 字符串
- [ ] 如果 SP 创建时 scope 用 resource id 失败，回退到 RG scope
