---
title: Clawdbot连接iMessage教程
date: 2026-01-28
category: Resources
tags: [Clawdbot, iMessage, Tutorial, macOS]
status: completed
---
# 如何让 Clawdbot 连接 iMessage：详尽指南

本文将指导你如何完成 Clawdbot 的 iMessage 渠道配置，包括安装必要组件、系统权限授权以及常见问题解决。

## 第一阶段：环境准备

### 1. 安装 imsg 支持
Clawdbot 依赖 `imsg` CLI 工具来与 macOS 的 iMessage 数据库进行通信。
在终端执行：
```bash
brew tap steipete/tap
brew install imsg
```

### 2. 确认 imsg 路径
安装完成后，找出 `imsg` 的执行路径（通常在 `/opt/homebrew/bin/imsg`）：
```bash
which imsg
```

---

## 第二阶段：Clawdbot 配置更新

在你的 Clawdbot 工作区，你需要更新对应的配置文件（通常是 `~/.clawdbot/clawdbot.json`）。

### 1. 启用 iMessage 频道
在该 JSON 文件中，确保以下部分已正确配置：
```json
{
  "plugins": {
    "entries": {
      "imessage": { "enabled": true }
    }
  },
  "channels": {
    "imessage": {
      "enabled": true,
      "cliPath": "/opt/homebrew/bin/imsg" 
    }
  }
}
```
*注：`cliPath` 必须与 `which imsg` 返回的路径一致。*

### 2. 重启 Gateway
每当修改配置文件后，都需要重启服务以生效：
```bash
clawdbot gateway restart
```

---

## 第三阶段：关键一步——授予系统权限 (TCC)

这是最容易失败的一步。由于 macOS 的隐私机制，即使配置正确，Clawdbot 也可能因为无法读取 `chat.db` 而报错。

### 1. 确定 Node 运行路径
由于 Clawdbot 是通过 Node.js 运行的，你需要授权的是 Node 程序本身。
在终端通过以下命令找到真实的 Node 路径：
```bash
# 这通常会返回一个 Cellar 路径或 /opt/homebrew/bin/node
readlink -f $(which node)
```

### 2. 授予“完全磁盘访问权限 (Full Disk Access)”
1. 打开 **系统设置 (System Settings)** -> **隐私与安全性 (Privacy & Security)** -> **完全磁盘访问 (Full Disk Access)**。
2. 点击底部的 **`+`** 号。
3. 按下快捷键 `Command + Shift + G`。
4. 粘贴你在上一步查到的路径（例如：`/opt/homebrew/opt/node/bin/node`），然后点击 **打开**。
5. 确保该路径旁边的开关已 **开启**。

---

## 第四阶段：验证连接

### 1. 运行 Probe 测试
你可以通过 Clawdbot 的状态指令查看 iMessage 频道是否在线：
```bash
clawdbot status
```

### 2. 手动 RPC 测试
如果你想直接测试 `imsg` 权限是否生效，可以在终端运行：
```bash
echo '{"jsonrpc":"2.0","method":"chats","id":1}' | /opt/homebrew/bin/imsg rpc
```
如果没有报错并返回了你的聊天列表，说明一切就绪！

---

## 常见问题处理

- **Probe failed Error: imsg rpc exited (code 1)**: 99% 的情况是因为“完全磁盘访问权限”未正确授予。请重新检查授权步骤，并确保你授权的是 `node` 真实路径而不是软链接。
- **权限已给但仍报错**: 尝试在权限列表中删除该条目并重新添加，然后彻底重启你的终端和 Clawdbot。
