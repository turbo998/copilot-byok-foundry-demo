# 实施当天 Checklist（一页速查）

> 打印或单开一屏。所有时间从"目标 demo 开始时间 T"倒推。

---

## ⏰ T-7 天：访问申请（一次性）

- [ ] 联系 GitHub 客户经理，确认 **BYOK GA/Preview 状态 + 是否支持 Anthropic** ← **这是最大风险，必须先确认**
- [ ] 如在 preview，提交 allowlist 申请
- [ ] Azure Portal → Foundry Model catalog → 申请 Anthropic Claude 模型访问（首次需 use-case，1-2 工作日审批）
- [ ] 确认 Foundry Anthropic 配额 ≥ Tier 1（≥100K TPM 较稳）
- [ ] 协调 GitHub Enterprise owner：约一个 30 分钟时间窗口配 BYOK，或拿到 owner 操作的录屏/截图

## ⏰ T-1 天：环境搭建 + 端到端预演

### Azure 侧（按 03-azure-setup-plan.md 执行）

- [ ] 跑 `01-azure-setup.sh` 创建 RG/Foundry/deployment/SP/RBAC/LAW
- [ ] 保存 SP 的 `appId/password/tenant` 到密码管理器（不要进 git）
- [ ] 跑 `02-verify-foundry.sh`，确认 curl 直打 Foundry endpoint 返回 Claude 响应
- [ ] 在 Foundry Metrics 页确认能看到刚才那次调用的 token 计数
- [ ] 在 Log Analytics 跑 KQL 查询，确认日志能看到（可能延迟 5-10 min）

### GitHub 侧（owner 操作，或截图）

- [ ] Enterprise settings → Copilot → Custom models → 填 Foundry endpoint + SP 凭证 + model mapping
- [ ] 点 "Test connection" 看到 Success
- [ ] 把 BYOK policy 应用到测试 org

### 客户端侧

- [ ] 演示机 VS Code 升级到最新 stable
- [ ] GitHub Copilot Chat 扩展升级到最新
- [ ] 用测试用户登录，确认模型选择器看得到 `Claude Opus 4.7 (BYOK)`
- [ ] 跑一遍完整 demo 5 段，全程录屏作为 backup

### 物料

- [ ] `demo/test-prompts.md` 中的 prompt 全部预跑一遍，挑出 Claude 答得最漂亮的一条作为段 3 主用
- [ ] 备份 PDF (`demo/backup-slides.pdf`) 已生成
- [ ] 速查卡 `demo/cheatsheet.md` 打印或开窗口

---

## ⏰ T-30 min：临门预热

- [ ] **预热 Foundry**：跑 3 次 curl 调用，让 Log Analytics 提前有数据，避免段 4 "查不到日志"尴尬
- [ ] Azure Portal 已登录，浏览器打开 3 个 tab：
  1. Foundry resource overview
  2. Foundry Model deployments 页
  3. Log Analytics 工作区 + KQL 查询已粘好
- [ ] VS Code 已打开示例 repo，Copilot Chat 已打开，模型已切到 Claude Opus 4.7 (BYOK)
- [ ] 网络检查：`ping cpbyok-foundry.services.ai.azure.com` < 100ms
- [ ] 屏幕字号放大（Ctrl+= 至少 2 次），关闭通知
- [ ] 手机静音，关闭 Teams/微信弹窗
- [ ] 准备一杯水

---

## ⏰ T-0：开始演示

按 `docs/05-demo-script.md` 5 段顺序走。

---

## 🚨 演示中应急预案

| 翻车场景 | 应对（≤ 30 秒决策） |
|---|---|
| **VS Code Copilot Chat 看不到 BYOK 模型** | 切到备份录屏，口播"这是预演时录的同一流程"；同时让 owner 后台检查 policy 是否生效 |
| **Foundry 调用超时/限流** | 切到段 4 先讲架构和审计，给 Foundry 缓冲时间；或者用 curl 在终端演示替代段 3 |
| **Log Analytics 查询无数据** | 改用 Foundry Metrics 面板（基本实时），跳过 KQL 部分 |
| **模型回答质量差** | 立即切下一个测试 prompt（`demo/test-prompts.md` 准备了 3 条 backup） |
| **网络断了** | 全程切到 PDF 备份，重点放在架构和价值讲解 |
| **客户问"那 GitHub 路由层会不会缓存我的代码"** | 引用 06 文档 Q6 的官方声明链接（实施前补全）；诚实回答"路径上必经 GitHub，但合规承诺如下…" |

---

## ⏰ T+演示后

- [ ] 立即记录：哪段顺、哪段卡、客户问了什么没答上来 → 写入 `docs/08-implementation-log.md`
- [ ] 把客户提的新问题加到 `docs/10-faq-for-customers.md`
- [ ] 演示当晚或次日：跑 `03-teardown.sh` 销毁资源（除非要继续给下一个客户演示）
- [ ] 把可复用的踩坑沉淀进 Hermes skill

---

## 关键凭证存放（演示当天速查）

> ⚠️ **不要写进本文件**。临时记在密码管理器或加密 note，演示后立即从剪贴板清除。

- Azure SP: `appId` / `password` / `tenant`
- 测试用户 GitHub 账号 / 密码 / 2FA
- 演示用 repo URL
- Foundry endpoint URL
- 备份录屏路径
