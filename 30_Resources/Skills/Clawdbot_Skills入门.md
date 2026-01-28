---
title: Clawdbot Skills 配置与安装指南
date: 2026-01-28
category: Resources
tags: [Clawdbot, Skills, Automation, Homebrew]
status: in-progress
---
# Clawdbot Skills 配置与安装指南 🛠️

基于对 `https://docs.molt.bot/tools/skills` 的调研以及本地系统的实操，以下是为你配置的技能列表及其状态。

## 1. Skills 核心概念
Clawdbot 通过 **AgentSkills** 兼容的文件夹来学习工具使用。
- **Bundled Skills**: 内置技能。
- **Managed Skills**: 位于 `~/.clawdbot/skills`。
- **Workspace Skills**: 位于当前工作区的 `/skills`（优先级最高）。

## 2. 自动化安装进度 (通过 Homebrew)
为了让 Clawdbot 变得更强大，我正在后台为你安装以下关键工具：

| 工具 | 用途 | 状态 |
| :--- | :--- | :--- |
| `gh` | GitHub 集成 | ⏳ 安装中 |
| `jq` | JSON 数据处理 | ⏳ 安装中 |
| `trash` | 安全删除文件 (替代 rm) | ⏳ 安装中 |
| `ffmpeg` | 视频/音频处理 (video-frames 依赖) | ⏳ 安装中 |
| `yt-dlp` | 视频下载 (summarize 依赖) | ⏳ 安装中 |

## 3. 待办事项与进阶 Skills
- [ ] **Apple Reminders**: 计划安装 `remindctl` 以启用苹果提醒集成。
- [ ] **Obsidian CLI**: 寻找稳健的命令行工具以增强知识库自动化。
- [ ] **ClawdHub**: 待 npm 兼容性问题解决后，将用于从 [clawdhub.com](https://clawdhub.com) 同步更多第三方技能。

---
*注：本文件由 aiagent02 自动生成，记录系统技能扩展进程。*
