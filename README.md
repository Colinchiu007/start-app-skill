# start-app skill

启动 / 重启 Multi-Publish 桌面应用（最新代码 + 已登录 profile）的 agent skill。

## 这是什么

`start-app` 是一个 agent skill：用户说「启动应用」「重启应用」「跑一下最新代码」时，agent 据此执行完整的启动契约——同步最新代码、检查环境、用持久化登录态启动桌面应用并验证。

单一事实源是 [SKILL.md](SKILL.md)（v1.6.0），包含：

- **一键启动工作流**（默认路径）：调用 Multi-Publish 主仓的 `scripts/sync-app.ps1`（同步+依赖哈希门禁+启动编排）与 `scripts/mp-applive-launcher.ps1`（WMI 脱离会话启动器）
- **备选长流程**：手动同步 + `start-desktop.ps1`（特殊 worktree/环境要求时用）
- **沙箱纪律**：agent 会话内禁整树 git 重写 + worktree 撕裂恢复 runbook
- **验证清单**：窗口、端口、CDP 登录态/账号
- **Windows / WSL 双环境**支持

## 脚本归属说明

启动脚本本体位于 Multi-Publish 主仓（`scripts/sync-app.ps1`、`scripts/mp-applive-launcher.ps1`，经 [mulpub PR #2040](https://github.com/Colinchiu007/mulpub/pull/2040) 合入），因为它们是项目资产——依赖仓库内的 `dev-ports.js`、`ensure-electron.js`，且走主仓的 PR/CI 质量门禁。本 skill 仓持有操作手册（SKILL.md），只引用不持有代码。

## 安装到各 agent

本 skill 的完整定义只维护一份（本仓 SKILL.md）。各 agent 通过各自约定目录的轻量入口加载：

| Agent | 入口路径 |
|-------|---------|
| WorkBuddy | `~/.workbuddy/skills/start-app/SKILL.md` |
| Codex | `.codex/skills/start-app/SKILL.md` |
| Claude Code | `.claude/commands/启动应用.md` |
| Cursor | `.cursor/commands/启动应用.md` |

克隆本仓后，把 `SKILL.md` 复制（或 junction）到上述入口位置即可。

```bash
git clone https://github.com/Colinchiu007/start-app-skill.git
# WorkBuddy 示例
cp start-app-skill/SKILL.md ~/.workbuddy/skills/start-app/SKILL.md
```

## 版本历史

- v1.6.0 — 集成一键启动工作流（sync-app.ps1 + WMI 脱离启动器）为默认路径
- v1.5.0 — CDP 服务清单核验、账号数据验证、WMI 独立启动器坑补全
- v1.3.0 — shared-user-data 锚点、双环境（Windows/WSL）
