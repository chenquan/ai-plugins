# `/cr` 代码审查命令 - 使用指南

## 概述

`/cr` 命令是一个智能代码审查工具，已成功集成到 ai-plugins 中。本指南展示如何使用该命令以及预期效果。

## 快速开始

### 1. 基本用法

审查当前未提交的更改：
```
/cr
```

### 2. 审查指定文件

审查单个文件：
```
/cr src/main.go
```

审查多个文件或目录：
```
/cr src/ tests/
```

### 3. 审查 Commit

审查最新的 commit：
```
/cr @HEAD
```

审查指定的 commit：
```
/cr --commit abc123
```

审查倒数第 3 个 commit：
```
/cr @HEAD~3
```

### 4. 审查 Pull Request

审查 PR（需要 GitHub CLI）：
```
/cr --pr 123
```

审查指定 URL 的 PR：
```
/cr --pr https://github.com/user/repo/pull/123
```

## 测试示例

### 测试文件

项目中包含了一个测试文件 `test_review_examples.go`，其中包含了以下问题：

1. **SQL 注入漏洞** (第 23 行)
2. **未使用的变量** (第 30 行)
3. **循环中的 defer** (第 50 行)
4. **低效的字符串拼接** (第 62 行)
5. **硬编码的密钥** (第 68-72 行)
6. **忽略错误** (第 80 行)
7. **过长的函数** (第 86 行)

### 测试步骤

#### 1. 将测试文件添加到 Git 仓库

```bash
# 如果还没有初始化 Git 仓库
git init

# 添加测试文件
git add test_review_examples.go

# 创建一个 commit
git commit -m "test: add code review test examples"
```

#### 2. 执行代码审查

在 Claude Code 中运行：
```
/cr @HEAD
```

#### 3. 预期输出

应该看到类似以下的审查报告：

```
🔍 代码审查报告
═══════════════════════════════════════════════════════════════

📊 审查概览
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
审查模式: Commit (@HEAD)
审查范围: 1 个文件，+120 行，-0 行
编程语言: Go (100%)
审查时间: 2026-03-20 23:20:00

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🎯 检查结果总览
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
✅ 代码质量: 发现 3 个问题
⚠️  安全性: 发现 3 个严重问题
🐌 性能问题: 发现 2 个优化机会
📝 测试覆盖: 缺少测试

[详细问题列表...]
```

## 常见使用场景

### 场景 1: 提交前审查

```bash
# 1. 修改代码
vim src/main.go

# 2. 查看更改
git status

# 3. 执行代码审查
/cr

# 4. 根据审查结果修复问题
# ...

# 5. 提交代码
/commit
```

### 场景 2: PR 审查

```bash
# 1. 创建 PR
gh pr create --title "Add new feature"

# 2. 审查 PR
/cr --pr 123

# 3. 根据审查结果改进代码
# ...

# 4. 更新 PR
git push
```

### 场景 3: 代码质量检查

```bash
# 审查特定目录
/cr src/

# 审查多个文件
/cr main.go auth.go database.go

# 审查测试文件
/cr tests/
```

## 审查维度说明

### 1. 代码质量

检查项：
- 命名规范
- 代码格式
- 函数长度
- 代码复杂度
- 代码重复

### 2. 安全性

检查项：
- SQL 注入
- XSS 攻击
- 命令注入
- 硬编码密钥
- 不安全的加密

### 3. 性能

检查项：
- N+1 查询
- 循环优化
- 字符串处理
- 内存使用
- 缓存机会

### 4. 测试覆盖

检查项：
- 测试存在性
- 测试覆盖率
- 测试质量
- 断言完整性

## 支持的语言

当前支持 20+ 编程语言，包括：

- **Go** - 完整支持
- **Python** - 完整支持
- **JavaScript/TypeScript** - 完整支持
- **Java** - 完整支持
- **C/C++** - 完整支持
- **C#** - 完整支持
- **PHP** - 完整支持
- **Ruby** - 完整支持
- **Rust** - 完整支持
- **Swift** - 完整支持
- **Kotlin** - 完整支持
- **Dart** - 完整支持
- **Scala** - 完整支持
- **Shell** - 完整支持
- **Lua** - 完整支持
- **Perl** - 完整支持
- **Haskell** - 完整支持

## 错误处理

### 非 Git 仓库

```
❌ 当前目录不是 Git 仓库

💡 解决方案：
• 初始化 Git 仓库: git init
• 或切换到已有 Git 仓库的目录
```

### 工作区干净

```
✅ 工作区干净，没有需要审查的更改

💡 提示：
• 查看最近的提交: git log
• 审查指定的 commit: /cr @HEAD
• 审查指定的文件: /cr <path>
```

### GitHub CLI 未安装

```
❌ 需要安装 GitHub CLI (gh) 才能审查 Pull Request

💡 安装方法：
macOS: brew install gh
Linux:  https://cli.github.com/
Windows: https://cli.github.com/

安装后需要登录: gh auth login
```

## 最佳实践

### 1. 定期审查

- 提交前：`/cr`
- PR 创建时：`/cr --pr <number>`
- 定期检查：`/cr @HEAD`

### 2. 修复优先级

1. **🔴 严重问题** - 立即修复
   - 安全漏洞
   - 数据泄露

2. **🟡 中等问题** - 尽快修复
   - 性能问题
   - 代码质量

3. **🟢 轻微问题** - 逐步改进
   - 命名规范
   - 代码格式

### 3. 团队协作

- 建立统一的代码质量标准
- 在 CI/CD 中集成代码审查
- 定期分享审查经验

## 进阶功能

### 自定义配置

创建 `.review-config.json`:

```json
{
  "rules": {
    "max_line_length": 100,
    "max_function_length": 50,
    "require_tests": true
  },
  "ignore": [
    "vendor/*",
    "node_modules/*"
  ]
}
```

### 集成到工作流

#### Pre-commit Hook

```bash
#!/bin/bash
echo "Running code review..."
# 执行审查，发现问题则阻止提交
```

#### GitHub Actions

```yaml
name: Code Review
on: [pull_request]
jobs:
  review:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Run code review
        run: |
          # 运行审查命令
```

## 故障排除

### 问题 1: 审查速度慢

**解决方案**：
- 使用 `--parallel` 启用并行分析
- 使用 `--incremental` 仅分析变更
- 使用 `--cache` 启用缓存

### 问题 2: 误报率高

**解决方案**：
- 调整规则严格程度
- 使用自定义配置
- 在 `.review-ignore` 中忽略特定文件

### 问题 3: 缺少语言支持

**解决方案**：
- 目前支持 20+ 语言
- 可以通过配置添加自定义规则
- 考虑使用其他工具补充

## 总结

`/cr` 命令提供了：

✅ **全面的代码审查** - 4 个维度，20+ 语言
✅ **灵活的使用方式** - 4 种审查模式
✅ **详细的报告** - 清晰的问题描述和修复建议
✅ **易于集成** - 可集成到现有工作流

通过定期使用 `/cr` 进行代码审查，可以显著提高代码质量和团队生产力。

## 相关资源

- 命令文档：`commands/review.md`
- 测试示例：`test_review_examples.go`
- 插件主页：https://github.com/chenquan/ai-plugins
