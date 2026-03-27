# AI Plugins for Claude Code

[![Version](https://img.shields.io/badge/version-0.2.0-blue)](https://github.com/chenquan/ai-plugins/releases)
[![License](https://img.shields.io/badge/license-MIT-green)](LICENSE)
[![Code Review](https://img.shields.io/badge/code%20review-20%2B%20languages-orange)](README.md#支持的编程语言)
[![Claude Code](https://img.shields.io/badge/Claude%20Code-compatible-brightgreen)](https://claude.ai/code)

这是一套功能丰富的 Claude Code 插件集合，旨在提升开发效率和代码质量。

## 📦 插件内容

本插件包含以下五大核心组件：

### 🎯 斜杠命令 (Slash Commands)
位于 `commands/` 目录，提供便捷的快捷指令：

#### 可用命令
- **`/commit`** - 智能提交 git 更改并推送到远程仓库
  - 自动生成符合 Conventional Commits 规范的提交信息
  - 支持 20+ 编程语言的智能文件类型识别
  - 提供预览和确认机制

- **`/cr`** - 智能代码审查工具
  - 支持 4 种审查模式（未提交更改、文件/目录、commit、PR）
  - 检查 4 个维度（代码质量、安全性、性能、测试覆盖）
  - 支持 20+ 编程语言
  - 详细的审查报告和可操作的修复建议

#### 使用示例
```bash
/commit          # 提交当前更改
/cr              # 审查未提交的更改
/cr src/main.go  # 审查指定文件
/cr --pr 123     # 审查 Pull Request
```

#### 命令特点
- 快速执行常用任务
- 封装复杂的工作流程
- 一键触发专业操作
- 智能上下文感知

### 🤖 子代理 (Subagents)
位于 `agents/` 目录，提供专门化的 AI 助手：

#### 可用代理
- **`code-review-expert`** - 专家级代码审查顾问
  - 提供深度代码分析和根本原因分析
  - 制定长期改进计划和路线图
  - 提供个性化团队培训建议
  - 建立持续改进机制和最佳实践库
  - 比基础 `/cr` 命令提供更全面的专家级指导

#### 子代理特点
- 独立上下文执行特定任务
- 专注领域知识
- 不会干扰主对话
- 专家级别的深度分析

### 📚 Skills (技能)
位于 `skills/` 目录，提供渐进式加载的专业知识包：
- 按需加载，不影响主上下文
- 包含指令和可执行脚本
- 领域最佳实践

### 🔌 MCP 服务器
位于 `mcp-servers/` 目录，通过标准协议连接外部工具：
- 集成第三方服务
- 访问外部数据源
- 扩展 Claude Code 能力

### ⚙️ 钩子 (Hooks)
位于 `hooks/` 目录，在工作流特定节点自动触发：
- 自动化代码格式化
- 实时代码检查
- 自定义工作流程

## 🚀 快速开始

### 安装插件

#### 方式 1: 通过 Claude Code Marketplace 安装（推荐）

在 Claude Code 中执行：

```
安装插件 ai-plugins
```

或者使用命令行：

```bash
# 如果 Claude Code 支持 CLI 安装
claude plugin install common
```

#### 方式 2: 本地安装

将本插件放置在 Claude Code 的插件目录中：

```bash
# 克隆仓库
git clone https://github.com/chenquan/ai-plugins.git ~/.claude/plugins/common

# Windows 用户
git clone https://github.com/chenquan/ai-plugins.git %USERPROFILE%\.claude\plugins\ai-plugins
```

#### 验证安装

安装完成后，重启 Claude Code 并验证：

```bash
# 查看所有可用命令
/help

# 测试代码提交命令
/commit

# 测试代码审查命令
/cr

# 调用代码审查专家代理
请使用 code-review-expert 代理帮我审查代码
```

### 使用斜杠命令

在 Claude Code 中直接使用 `/` 开头的命令：

```
/help          # 查看所有可用命令
/my-command    # 使用自定义命令
```

### 调用子代理

在对话中指定使用特定的子代理：

```
请使用 code-review-expert 代理帮我审查代码
```

**使用示例**：
```bash
# 快速审查
"请使用 code-review-expert 代理审查当前的代码更改"

# 深度审查
"请使用 code-review-expert 代理对 src/auth/ 进行深度审查"

# 培训导向审查
"请使用 code-review-expert 代理进行教学式的代码审查，我们有很多新人"

# 全面健康检查
"请使用 code-review-expert 代理对我们项目进行全面的质量健康检查"
```

### Skills 自动加载

Skills 会在需要时自动加载，无需手动操作。

## 📁 目录结构

```
ai-plugins/
├── plugin.json              # 插件清单
├── README.md                # 使用说明
├── commands/                # 斜杠命令
│   └── example.md
├── agents/                  # 子代理
│   └── example.md
├── skills/                  # Skills
│   └── example-skill/
│       └── SKILL.md
├── mcp-servers/             # MCP 服务器配置
│   └── config.json
└── hooks/                   # 钩子配置
    └── hooks.json
```

## 🛠️ 开发指南

### 添加新的斜杠命令

在 `commands/` 目录下创建 `.md` 文件：

```markdown
---
description: 命令描述
---

你的命令内容...
```

### 创建新的子代理

在 `agents/` 目录下创建 `.md` 文件：

```markdown
---
description: 代理描述
model: sonnet
---

代理的指令和配置...
```

### 开发新的 Skill

在 `skills/` 目录下创建文件夹，并在其中创建 `SKILL.md`：

```markdown
---
description: Skill 描述
---

Skill 的详细内容...
```

### 配置 MCP 服务器

编辑 `mcp-servers/config.json`：

```json
{
  "mcpServers": {
    "server-name": {
      "command": "node",
      "args": ["path-to-server.js"]
    }
  }
}
```

### 配置钩子

编辑 `hooks/hooks.json`：

```json
{
  "beforeEdit": [
    {
      "description": "编辑前执行",
      "command": "echo 'Editing file'"
    }
  ]
}
```

## 📝 许可证

MIT License

## 🤝 贡献

欢迎提交 Issue 和 Pull Request！

## 📧 联系方式

- 作者：chenquan
- 仓库：https://github.com/chenquan/ai-plugins
