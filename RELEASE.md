# 发布指南 (Release Guide)

本文档说明如何将 ai-plugins 插件发布到 Claude Code Marketplace。

## 前置条件

1. **GitHub 仓库**: 确保代码已推送到 GitHub
2. **版本号**: 更新 `plugin.json` 和 `marketplace.json` 中的版本号
3. **变更日志**: 更新 `CHANGELOG.md`
4. **测试**: 确保所有功能正常工作

## 发布步骤

### 1. 准备发布

#### 1.1 更新版本号

更新以下文件中的版本号：
- `plugin.json`
- `marketplace.json`
- `README.md` 中的徽章版本

```bash
# 例如：从 0.2.0 更新到 0.3.0
```

#### 1.2 更新变更日志

在 `CHANGELOG.md` 中添加新版本的变更内容：

```markdown
## [0.3.0] - 2026-03-XX

### 新增 (Added)
- 新功能描述

### 修复 (Fixed)
- 修复的问题

### 改进 (Changed)
- 改进的内容
```

#### 1.3 创建 Git 标签

```bash
# 创建带注释的标签
git tag -a v0.3.0 -m "Release v0.3.0: 新功能描述"

# 推送标签到远程
git push origin v0.3.0
```

### 2. 发布到 GitHub

#### 2.1 创建 GitHub Release

1. 访问 GitHub 仓库的 Releases 页面
2. 点击 "Draft a new release"
3. 填写信息：
   - **Tag**: 选择刚创建的标签（如 `v0.3.0`）
   - **Release title**: `Release v0.3.0`
   - **Description**: 从 CHANGELOG.md 复制该版本的变更内容
4. 点击 "Publish release"

### 3. 提交到 Claude Code Marketplace

#### 3.1 准备 Marketplace 清单

确认 `marketplace.json` 文件完整且正确：

```json
{
  "name": "ai-plugins",
  "version": "0.3.0",
  "description": "...",
  "features": {...},
  "documentation": {...}
}
```

#### 3.2 提交插件

**方式 A: 通过 Claude Code CLI**

```bash
# 如果 Claude Code 提供了 CLI 工具
claude marketplace submit

# 或使用 npx
npx @claude-code/marketplace submit
```

**方式 B: 通过 Web 界面**

1. 访问 Claude Code Marketplace 网站
2. 登录你的账户
3. 点击 "Submit Plugin"
4. 填写表单：
   - 插件名称
   - 仓库 URL
   - 描述
   - 分类
   - 标签
5. 提交审核

#### 3.3 等待审核

- 插件提交后会进入审核队列
- 审核时间通常为 1-3 个工作日
- 审核通过后会收到通知

### 4. 验证发布

#### 4.1 测试安装

```bash
# 通过 marketplace 安装
claude plugin install ai-plugins

# 或者在 Claude Code 中
安装插件 ai-plugins
```

#### 4.2 功能测试

测试所有功能是否正常：
- `/commit` 命令
- `/cr` 命令
- `code-review-expert` 代理
- 其他功能

### 5. 发布后维护

#### 5.1 监控反馈

- 关注 GitHub Issues
- 查看 Marketplace 评分和评论
- 收集用户反馈

#### 5.2 问题修复

发现问题时：

```bash
# 创建修复分支
git checkout -b fix/issue-number

# 修复问题
# ...

# 提交修复
git commit -m "fix: resolve issue #XXX"

# 推送并创建 PR
git push origin fix/issue-number
```

#### 5.3 发布补丁版本

如果需要发布补丁版本（如 `v0.3.1`）：

```bash
# 更新版本号到 0.3.1
# 更新 CHANGELOG.md

# 创建标签
git tag -a v0.3.1 -m "Release v0.3.1: Patch release"

# 推送
git push origin v0.3.1
```

## 版本号规范

遵循 [Semantic Versioning](https://semver.org/)：

- **MAJOR.MINOR.PATCH** (如 0.2.0)
  - **MAJOR**: 不兼容的 API 变更
  - **MINOR**: 向后兼容的功能新增
  - **PATCH**: 向后兼容的问题修复

示例：
- `0.1.0` → `0.2.0`: 新增功能（代码审查专家）
- `0.2.0` → `0.2.1`: Bug 修复
- `0.2.1` → `1.0.0`: 重大更新或不兼容变更

## 发布检查清单

在发布前，请确认：

- [ ] 所有功能已测试
- [ ] 文档已更新
- [ ] CHANGELOG.md 已更新
- [ ] 版本号已更新
- [ ] Git 标签已创建
- [ ] GitHub Release 已创建
- [ ] marketplace.json 已更新
- [ ] 插件在 Claude Code 中测试通过
- [ ] README.md 中的示例可正常工作

## 回滚计划

如果发布后发现严重问题：

```bash
# 1. 立即在 GitHub 上标记该版本为 deprecated
# 2. 修复问题
# 3. 发布新版本（如 0.3.1）
# 4. 通知用户更新
```

## 常见问题

### Q: 插件审核被拒绝了怎么办？

A: 查看拒绝原因，修正问题后重新提交。常见原因：
- 描述不准确
- 功能不完整
- 文档不清晰
- 安全问题

### Q: 如何加快审核速度？

A:
- 确保提交信息完整
- 提供清晰的文档
- 包含使用示例
- 确保代码质量

### Q: 可以同时有多个版本吗？

A: Marketplace 通常只显示最新版本，但用户可以安装特定版本：
```bash
claude plugin install ai-plugins@0.2.0
```

## 联系方式

如有发布相关的问题：
- 创建 GitHub Issue
- 联系：chenquan
- 仓库：https://github.com/chenquan/ai-plugins

---

**最后更新**: 2026-03-21
