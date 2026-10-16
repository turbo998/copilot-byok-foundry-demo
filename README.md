# GitHub Copilot Enterprise + Azure AI Foundry BYOK (Claude) — Demo Plan

> **状态**：📋 计划阶段，未实施。后续测试实施时按本文档逐项执行。
>
> **灵感来源**：[在 Amazon Bedrock 上运行 Claude Code 和 Claude Desktop](https://builder.aws.com/content/3DT8Jx3zabcNSn26DYmMrWgCxS0/amazon-bedrock-claude-code-claude-desktop) — 把 AWS 上"Claude 推理留在自家云"的思路对应到 Azure。

## 一句话目标

> 企业开发者在 VS Code 里用 GitHub Copilot Chat 切到 Claude Opus 4.x，**推理请求落到自己 Azure 租户的 Foundry resource**，数据不出租户、费用走 Azure EA、审计日志在自己 Log Analytics——开发者完全无感。

## 文档导航

| 文档 | 用途 |
|---|---|
| [01-architecture.md](docs/01-architecture.md) | 架构设计、组件映射、数据流 |
| [02-prerequisites.md](docs/02-prerequisites.md) | GitHub / Azure / 网络 前置条件清单 |
| [03-azure-setup-plan.md](docs/03-azure-setup-plan.md) | Azure 侧资源搭建计划（Foundry + Claude deployment + SP + RBAC） |
| [04-github-byok-config-plan.md](docs/04-github-byok-config-plan.md) | GitHub Copilot BYOK 配置步骤（需 Enterprise owner 协助） |
| [05-demo-script.md](docs/05-demo-script.md) | 12 分钟 demo 演示脚本（5 段） |
| [06-risks-and-open-questions.md](docs/06-risks-and-open-questions.md) | 已知风险、待验证事项、降级方案 |
| [07-deliverables-checklist.md](docs/07-deliverables-checklist.md) | POC 完成后的交付物 checklist |
| [IMPLEMENTATION-DAY-CHECKLIST.md](docs/IMPLEMENTATION-DAY-CHECKLIST.md) | 🔥 实施当天一页速查（T-7d / T-1d / T-30min / T-0 / 应急预案） |

## 已确认的关键决策

- ✅ **GitHub 角色**：当前账号（`turbo998`）是普通用户，非 Enterprise owner → BYOK 配置环节需协调 owner 操作或截图模拟
- ✅ **Foundry resource**：新建专用资源（不复用现有 `<existing-foundry-resource>`），demo 后可一键销毁
- ✅ **Region**：暂定 **East US 2**（待验证 Anthropic 模型可用性后定稿）
- ✅ **命名前缀**：`cpbyok-`（copilot-byok 缩写）
- ✅ **本仓库当前阶段**：仅放计划文档，不创建实际 Azure 资源 / 不写可执行脚本（脚本骨架放 `scripts/` 仅供后续实施时填充）

## 后续实施时的执行顺序

1. 读 [02-prerequisites.md](docs/02-prerequisites.md) 完成全部前置确认
2. 按 [06-risks-and-open-questions.md](docs/06-risks-and-open-questions.md) 跑通"首要验证项"（Claude 在 Foundry 的可用性 + BYOK GA 状态）
3. 执行 [03-azure-setup-plan.md](docs/03-azure-setup-plan.md) 搭 Azure 侧
4. 协调 GitHub Enterprise owner 按 [04-github-byok-config-plan.md](docs/04-github-byok-config-plan.md) 配 BYOK
5. 按 [05-demo-script.md](docs/05-demo-script.md) 彩排
6. 按 [07-deliverables-checklist.md](docs/07-deliverables-checklist.md) 产出最终交付物

---
作者：Chen Qi (`turbo998`) · 创建于 2026-05
