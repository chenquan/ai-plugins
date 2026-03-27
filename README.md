# AI Plugins for Claude Code

[![Version](https://img.shields.io/badge/version-0.2.0-blue)](https://github.com/chenquan/ai-plugins/releases)
[![License](https://img.shields.io/badge/license-MIT-green)](LICENSE)
[![Claude Code](https://img.shields.io/badge/Claude%20Code-compatible-brightgreen)](https://claude.ai/code)

一套功能丰富的 Claude Code 插件集合，旨在提升开发效率和代码质量。

## 🚀 快速开始

### 安装插件

#### 方式 1: 通过 Claude Code Marketplace 安装（推荐）

在 Claude Code 中执行：

```
安装插件 common
```

或者使用命令行：

```bash
# 如果 Claude Code 支持 CLI 安装
claude plugin install common
```

#### 方式 2: 本地安装（开发模式）

```bash
# 克隆仓库
git clone https://github.com/chenquan/ai-plugins.git ~/claude-marketplace
cd ~/claude-marketplace

# Claude Code 会自动识别 .claude-plugin/marketplace.json
# 并加载 plugins/common/ 中定义的插件
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

## 📦 插件内容

本插件包含以下核心组件：

### 🎯 斜杠命令 (Slash Commands)
位于 `plugins/common/commands/` 目录，提供便捷的快捷指令：

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

### 🤖 子代理 (Subagents)
位于 `plugins/common/agents/` 目录，提供专门化的 AI 助手：

#### 可用代理

- **`code-review-expert`** - 专家级代码审查顾问
  - 提供深度代码分析和根本原因分析
  - 制定长期改进计划和路线图
  - 提供个性化团队培训建议
  - 建立持续改进机制和最佳实践库
  - 比基础 `/cr` 命令提供更全面的专家级指导

#### 使用示例

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

### 📚 Skills (技能)
位于 `plugins/common/skills/` 目录，提供渐进式加载的专业知识包：
- 按需加载，不影响主上下文
- 包含指令和可执行脚本
- 领域最佳实践

### 🔌 MCP 服务器
位于 `plugins/common/.claude-plugin/mcp.json`，通过标准协议连接外部工具：
- 集成第三方服务
- 访问外部数据源
- 扩展 Claude Code 能力

### ⚙️ 钩子 (Hooks)
位于 `plugins/common/.claude-plugin/hooks.json`，在工作流特定节点自动触发：
- 自动化代码格式化
- 实时代码检查
- 自定义工作流程

## 📁 目录结构

```
ai-plugins/
├── .claude-plugin/
│   └── marketplace.json         # 市场配置
├── plugins/                     # 所有插件
│   └── common/                  # 主插件目录
│       ├── .claude-plugin/
│       │   ├── plugin.json      # 插件元数据
│       │   ├── hooks.json       # 钩子配置
│       │   └── mcp.json         # MCP 服务器配置
│       ├── README.md            # 插件说明
│       ├── CLAUDE.md            # Claude Code 指令
│       ├── commands/            # 斜杠命令
│       │   ├── commit.md
│       │   └── cr.md
│       ├── agents/              # 子代理
│       │   ├── code-review-expert.md
│       │   └── code-review-knowledge-base.md
│       ├── skills/              # Skills
│       │   └── example-skill/
│       │       └── SKILL.md
│       ├── docs/                # 文档
│       │   ├── CODE_REVIEW_AGENT_GUIDE.md
│       │   ├── CR_COMMAND_USAGE.md
│       │   └── IMPLEMENTATION_SUMMARY.md
│       ├── templates/           # 模板
│       │   ├── improvement-report.md
│       │   ├── review-tracker.md
│       │   └── training-plan.md
│       ├── images/              # 图片资源
│       └── hooks/               # 钩子脚本
├── PLUGIN_SCHEMA.md             # 插件架构文档
├── QUICK_START.md               # 快速开始指南
├── README.md                    # 市场说明
├── CONTRIBUTING.md              # 贡献指南
└── SECURITY.md                  # 安全政策
```

## 🛠️ 开发指南

### 添加新的斜杠命令

在 `plugins/common/commands/` 目录下创建 `.md` 文件：

```markdown
---
description: 命令描述
---

你的命令内容...
```

### 创建新的子代理

在 `plugins/common/agents/` 目录下创建 `.md` 文件：

```markdown
---
description: 代理描述
model: sonnet
---

代理的指令和配置...
```

### 开发新的 Skill

在 `plugins/common/skills/` 目录下创建文件夹，并在其中创建 `SKILL.md`：

```markdown
---
description: Skill 描述
---

Skill 的详细内容...
```

### 配置 MCP 服务器

编辑 `plugins/common/.claude-plugin/mcp.json`：

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

编辑 `plugins/common/.claude-plugin/hooks.json`：

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

---

Built with ❤️ by [chenquan](https://github.com/chenquan)
