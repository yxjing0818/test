---
title: Claude Code 命令与操作完整培训
date: 2026-04-25
tags:
  - Claude Code
  - AI 编程
  - 终端
  - 自动化
draft: false
---

这是一份用于系统学习 **Claude Code** 的训练笔记，同步维护于独立培训网页：

- [打开完整培训网页](https://liyongzheng666.github.io/fish-terminal-setup-guide-pages/claude-code-training/)
- [查看静态页仓库](https://github.com/liyongzheng666/fish-terminal-setup-guide-pages)

本文按 2026-04-25 的 Claude Code 官方文档核对，重点覆盖 CLI、斜杠命令、权限模式、MCP、Hooks、Subagents、Skills 和日常开发工作流。

## 学习路线

不要从背命令开始。更稳的顺序是：

1. 会启动：掌握 `claude`、`claude -p`、`--continue`、`--resume`、安装、登录和更新。
2. 会控场：掌握 `/plan`、`/permissions`、`/model`、`/effort`、`/compact`、`/rewind`。
3. 会交付：能让 Claude Code 完成探索、修改、验证、解释和 PR 准备。
4. 会扩展：把 MCP、Hooks、Skills、Subagents 和插件接进真实工具链。

## CLI 入口

常用启动命令：

```bash
claude
claude "explain this project"
claude -p "summarize this log" --output-format json
cat logs.txt | claude -p "find the root cause"
claude -c
claude -r "auth-refactor" "finish this PR"
claude update
claude install stable
claude auth status --text
```

常用启动参数：

```bash
claude --add-dir ../apps ../lib
claude --model sonnet
claude --permission-mode plan
claude -p "check this diff" --max-turns 5
```

使用判断：

- 持续探索和代码修改：用 `claude`。
- 脚本、CI、日志摘要：用 `claude -p`。
- 接着昨天的上下文继续：用 `claude -c` 或 `claude -r`。
- 多目录项目：用 `--add-dir`。

## Slash commands

会话控制：

- `/help`：查看当前环境可用命令。
- `/status`：查看版本、模型、账号和连接状态。
- `/usage`、`/cost`、`/stats`：查看使用统计。
- `/clear`：清空上下文开始新对话。
- `/compact [instructions]`：压缩上下文但继续当前会话。
- `/rewind`：通过 checkpoint 恢复代码、对话或从某点总结上下文。
- `/resume [session]`：恢复指定会话。

配置与权限：

- `/config`：打开设置界面。
- `/model [model]`：切换模型。
- `/effort [level|auto]`：调整推理强度。
- `/permissions`：管理 allow、ask、deny 权限规则。
- `/mcp`：管理 MCP server 连接。
- `/hooks`：查看和管理 hook。
- `/agents`：管理 subagent。
- `/skills`：列出可用 skill。
- `/plugin`：管理插件。

开发工作流：

- `/plan [description]`：进入计划模式，先读代码和列方案。
- `/debug [description]`：排查问题和读取调试日志。
- `/review [PR]`：本地审查 PR。
- `/security-review`：审查当前分支安全风险。
- `/simplify [focus]`：检查最近改动并做小范围简化。
- `/batch <instruction>`：大型迁移或批量改动拆分并行处理。
- `/loop [interval] [prompt]`：重复执行提示。
- `/schedule [description]`：创建或管理 routines。

## 权限模式

权限模式决定 Claude Code 在编辑文件、运行命令或发起网络请求前是否询问。

| 模式 | 适合场景 |
| --- | --- |
| `default` | 新项目、敏感仓库、需要逐步确认的任务 |
| `plan` | 只读探索、需求澄清、架构方案 |
| `acceptEdits` | 你愿意自动接受编辑，但仍希望盯着 diff |
| `auto` | 长任务、低打断工作流，前提是边界和验证命令清楚 |
| `dontAsk` | CI 或脚本化环境，只允许预先批准的工具 |
| `bypassPermissions` | 仅隔离容器或虚拟机，不要在真实个人目录里使用 |

CLI 中常见模式可用 `Shift+Tab` 切换，也可以启动时指定：

```bash
claude --permission-mode plan
```

## 日常工作流

### 陌生代码库探索

```text
请先只读分析这个项目：
1. 说明它做什么、主要入口在哪里
2. 列出构建、测试、部署命令
3. 标出最需要保护的目录和配置
4. 给我一个 30 分钟上手路线
暂时不要修改文件。
```

### Bug 修复

```text
我遇到这个错误：[粘贴错误]
复现命令是：[命令]
请先分析 3 个可能根因，说明你要读哪些文件。
确认最可能根因后，做最小改动并运行复现命令验证。
```

### 重构与清理

先锁住行为，再改结构：

1. 明确测试、快照或输入输出样例。
2. 声明边界，例如「不改公共 API」或「只改某目录」。
3. 每轮只处理一个问题：重复、命名、边界或依赖。
4. 结束后用 `/simplify` 或 `/review` 再查一遍。

### PR 与审查

```text
请读取当前 git diff，输出：
1. 改动摘要
2. 用户影响
3. 风险和回滚方式
4. 已验证和未验证项
5. 适合作为 PR 描述的 Markdown
```

## MCP、Hooks、Subagents

MCP 适合把外部系统接入 Claude Code，例如 issue tracker、GitHub、数据库、监控、文档系统。远程服务优先 HTTP transport：

```bash
claude mcp add --transport http notion https://mcp.notion.com/mcp
```

Hooks 适合在生命周期事件中自动运行脚本，比如：

- `PreToolUse`：阻断 `rm -rf`、禁止读取 `.env`。
- `PostToolUse`：编辑后跑局部测试或格式化。
- `SessionStart`：注入项目说明或动态上下文。
- `PreCompact` / `PostCompact`：在上下文压缩前后做记录。

Subagents 适合把探索、规划、专项审查隔离到专用上下文。内置常见角色包括 Explore、Plan、general-purpose；自定义 subagent 可通过 `/agents` 管理。

## 七天练习

1. 安装或更新 Claude Code，确认 `claude auth status --text` 正常。
2. 在一个项目中启动 `claude`，让它输出项目总览和测试命令。
3. 用 `/plan` 分析一个小需求，要求列风险、文件清单和验证方式。
4. 用 `/permissions` 允许一条安全测试命令，拒绝读取 `.env*`。
5. 拿一个真实报错做 bug 修复，先分析再改，最后跑复现命令。
6. 练 `/compact` 和 `/rewind`，理解上下文恢复边界。
7. 用 `claude -p --output-format json` 给日志或 diff 生成结构化摘要。
8. 读 MCP 文档，至少配置或草拟一个可信工具接入方案。
9. 对一个本地 diff 跑 `/review` 或 `/security-review`。
10. 让 Claude Code 生成 PR 说明，包含变更、原因、验证和剩余风险。

## 官方资料

- [Claude Code CLI reference](https://code.claude.com/docs/en/cli-reference)
- [Claude Code commands reference](https://code.claude.com/docs/en/commands)
- [Permission modes](https://code.claude.com/docs/en/permission-modes)
- [Best practices for Claude Code](https://code.claude.com/docs/en/best-practices)
- [MCP](https://code.claude.com/docs/en/mcp)
- [Hooks reference](https://code.claude.com/docs/en/hooks)
- [Subagents](https://code.claude.com/docs/en/sub-agents)
- [Checkpointing](https://code.claude.com/docs/en/checkpointing)
