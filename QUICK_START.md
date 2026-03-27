# AI Plugins Marketplace - 快速开始

## 📦 可用功能

本插件集合包含以下功能：

### 1. 斜杠命令

#### `/commit` - 智能提交
自动生成符合 Conventional Commits 规范的提交信息

**使用:**
```bash
/commit
```

#### `/cr` - 智能代码审查
支持 4 种审查模式的全面代码审查工具

**使用:**
```bash
/cr              # 审查未提交的更改
/cr src/main.go  # 审查指定文件
/cr --pr 123     # 审查 Pull Request
```

### 2. 子代理

#### `code-review-expert` - 专家级代码审查顾问
提供深度分析、长期规划和团队培训

**使用:**
```
请使用 code-review-expert 代理帮我审查代码
```

## 🚀 安装

### 通过市场安装（推荐）

```bash
/plugin marketplace add chenquan/ai-plugins
```

### 本地安装（开发模式）

```bash
git clone https://github.com/chenquan/ai-plugins.git ~/claude-marketplace
```

## 📚 更多信息

- [完整文档](README.md)
- [插件架构](PLUGIN_SCHEMA.md)
- [贡献指南](CONTRIBUTING.md)
