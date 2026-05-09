# 07 — 交付物 Checklist

POC 实施完成后应产出以下文件，全部纳入本仓库。

## 7.1 文档类

- [ ] `docs/08-implementation-log.md` — 实施过程记录（命令、报错、解决方案）
- [ ] `docs/09-blog-draft-zh.md` — 中文博客初稿（对标 builder.aws 原文风格）
- [ ] `docs/09-blog-draft-en.md` — 英文版
- [ ] `docs/10-faq-for-customers.md` — 客户常见问题答疑（合规、计费、性能）

## 7.2 脚本类（占位文件已在 `scripts/`）

- [ ] `scripts/01-azure-setup.sh` — 一键搭建 Azure 侧（RG + Foundry + deployment + SP + RBAC + LAW）
- [ ] `scripts/02-verify-foundry.sh` — curl 验证 Foundry endpoint 通
- [ ] `scripts/03-teardown.sh` — 一键销毁所有资源
- [ ] `scripts/04-list-anthropic-models.sh` — 查 region 内可用的 Anthropic 模型

## 7.3 视觉素材

- [ ] `assets/architecture.svg` — 架构图（Azure 配色，沿用 azure-style-presentation skill）
- [ ] `assets/data-flow.svg` — 一次请求的时序图
- [ ] `assets/screenshots/` — Demo 5 段的关键截图（备演示翻车用）

## 7.4 演示物料

- [ ] `demo/cheatsheet.md` — 演示当天的速查卡（命令、URL、登录信息）
- [ ] `demo/backup-slides.pdf` — 备用 PDF 截图版（网络/环境出问题时使用）
- [ ] `demo/test-prompts.md` — 演示段 3 用的测试问题集（已验证 Claude 回答效果好的）

## 7.5 复用产物

- [ ] 把通用步骤沉淀为 Hermes skill：`azure-copilot-byok` 或合并到 `azure-foundry-managed-identity`
- [ ] 在飞书/Notion 同步一份给团队
