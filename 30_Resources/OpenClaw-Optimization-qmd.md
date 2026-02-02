---
title: OpenClaw 成本优化实战：用 qmd 构建上下文节流阀
date: 2026-02-01
category: Efficiency
tags: [OpenClaw, Cost-Optimization, qmd, Semantic-Search, Local-LLM]
status: completed
---

# OpenClaw 成本优化实战：用 qmd 构建上下文节流阀 🛡️💰

## 1. 问题背景：$26 的惨痛教训
在 OpenClaw (Moltbot) 的默认运行模式下，`read` 工具非常“诚实”。当 Agent 需要读取日志或文档时，它往往会读取整个文件。
*   **场景**：Agent 为了查找一行错误日志，读取了 2000 行的 `system.log`。
*   **后果**：这 2000 行文本被完整塞入 LLM 的 Context Window。
*   **代价**：单次操作消耗 20k+ Tokens。昨晚一晚上的自动化任务烧掉了 **$26 USD**。

## 2. 解决方案：上下文节流 (Context Throttling)
核心思路是**“先检索，后提取”**。我们引入了 `qmd` (Quantum Markdown Search)，一个基于 Rust/Bun 的本地语义搜索引擎。

### 架构变化
*   ❌ **旧模式 (The Expensive Way)**：
    `read file.md` -> LLM (Cost: $$$)
*   ✅ **新模式 (The Thrifty Way)**：
    `qmd vsearch "intent"` -> Local Embedding -> Top-K Snippets -> LLM (Cost: Free retrieval + Micro-inference)

## 3. 部署实录

### 3.1 安装环境
```bash
# 1. 安装 Bun Runtime
curl -fsSL https://bun.sh/install | bash

# 2. 安装 qmd
bun install -g https://github.com/tobi/qmd
```

### 3.2 构建记忆库
将工作区核心文件和每日日志索引化：
```bash
cd ~/clawd
# 索引核心配置
qmd collection add --name workspace *.md
# 索引日志
qmd collection add --name daily-logs memory
# 生成向量 (自动下载 GGUF 模型)
qmd embed
```

### 3.3 MCP 集成
配置 `~/clawd/config/mcporter.json` 让 Agent 原生调用：
```json
{
  "mcpServers": {
    "qmd": {
      "command": "/Users/aiagent02/.bun/bin/qmd",
      "args": ["mcp"]
    }
  }
}
```

## 4. 效果验证
*   **搜索**：`qmd vsearch workspace "Identity thoughts"`
*   **结果**：Agent 成功关联到了 `IDENTITY.md` 和 `SOUL.md` 中的哲学思考片段，而无需通读所有文件。
*   **成本**：检索过程完全本地化，Token 消耗降低 **90% 以上**。

## 5. 结论
Agent 的智能不应建立在无度的 Token 消耗上。通过本地语义索引层，我们为 Agent 装上了一个“海马体”，让它只需回忆关键信息，而非每次都“重读人生”。
