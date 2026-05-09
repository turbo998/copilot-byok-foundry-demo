# 05 — Demo 演示脚本（12 分钟，5 段）

## 受众

企业 IT / 安全合规 / 开发平台负责人。痛点：
- 想用 Claude，但不愿数据出租户
- 现有 GitHub Copilot Enterprise 已部署
- 需向合规团队解释"AI 代码助手的数据流"

## 总时长

约 12 分钟 + 3 分钟 Q&A buffer。

---

## 段 1 — Azure 侧资源展示（2 min）

**操作**：
- 打开 Azure Portal → `cpbyok-demo-rg`
- 展示 `cpbyok-foundry` resource
- 进入 **Model deployments** 页，指出 `claude-opus-4-7-byok`、`claude-sonnet-4-5-byok` 两个 deployment
- 切到 **Quotas** 页展示 Anthropic 配额

**话术**：
> "这是客户自己 Azure 租户里的 Foundry resource。Claude 模型已经部署好了，配额也是企业自己的。Region 在 East US 2，物理上数据不会离开这个区域。"

---

## 段 2 — GitHub Enterprise BYOK 配置（2 min）

**操作**（如有 owner 权限实操，否则截图）：
- GitHub Enterprise settings → Copilot → Custom models
- 展示已配置的 Foundry endpoint + SP 凭证 + model mapping

**话术**：
> "在 GitHub Enterprise 后台，管理员一次性配好 Foundry 的 endpoint 和 Entra Service Principal。注意我们用的是 SP 而不是静态 API key——可独立轮换、可审计、可随时禁用。Model mapping 把 GitHub 侧的模型名映射到 Foundry deployment name。"

---

## 段 3 — 开发者使用体验（3 min）

**操作**：
- 打开 VS Code，打开一个示例 repo
- 触发 Copilot Chat (`Ctrl+Alt+I`)
- 模型选择器切到 **Claude Opus 4.7 (BYOK)**
- 输入一个真实的代码问题，例如：
  > "Refactor this function to use async/await and add error handling."
- 展示 Claude 的回复

**话术**：
> "对开发者来说，体验和原来一模一样——还是熟悉的 Copilot Chat，只是模型选择器多了一个 BYOK 选项。背后的请求已经完全走客户自己的 Azure 了。"

---

## 段 4 — 可观测性 & 审计闭环（3 min）

**操作**：
- 切回 Azure Portal → `cpbyok-foundry` → **Metrics**
- 选 "Tokens" / "Total Calls"，时间范围最近 5 分钟
- 指出刚才那次调用的 token 消耗
- 切到 **Log Analytics** → 跑查询：
  ```kusto
  AzureDiagnostics
  | where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
  | where TimeGenerated > ago(10m)
  | project TimeGenerated, OperationName, ResultSignature, properties_s
  ```
- 展示刚才的调用日志

**话术**：
> "实时可见：这次调用消耗了多少 token、谁调的、几点调的、Foundry 返回了什么状态码。所有数据都在客户自己的 Log Analytics 里，可接入企业 SIEM、可做长期审计。"

---

## 段 5 — 控制力演示（2 min）

**操作**：
- 在 Foundry 把 `claude-opus-4-7-byok` deployment **禁用**（或删除并重建）
- 回到 VS Code Copilot Chat 再发一个请求
- 展示 Copilot 自动 fallback 到 GitHub 默认模型，或给出明确错误提示
- 重新启用 deployment，恢复正常

**话术**：
> "如果合规团队要求紧急停用，企业管理员在 Azure 这一侧就能直接关掉 deployment——不需要走 GitHub 工单、不需要等流程。这是 BYOK 真正的价值：控制权在自己手里。"

---

## 总结幻灯片要点（口播）

> "三个层次的价值：
> 1. **数据层面** — Prompt 和代码留在自己 Azure region
> 2. **商务层面** — 走 Azure EA 账单，用 commit 抵扣
> 3. **运营层面** — 模型生命周期、审计日志、紧急停用全部自主可控
>
> 而对开发者，零改变。"

## 演示前 Checklist

- [ ] Azure Portal 已登录，cpbyok-foundry 页面已打开
- [ ] VS Code 已登录测试用户，Copilot Chat 扩展为最新版
- [ ] 测试 repo 已 clone 到本地
- [ ] Log Analytics 查询已保存为收藏
- [ ] 屏幕分辨率/字号已放大到后排可见
- [ ] 网络稳定（Foundry 调用 < 3s）
- [ ] 准备一份 PDF 备份截图，演示翻车时使用
