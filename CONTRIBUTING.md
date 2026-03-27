# 贡献指南 (Contributing Guide)

感谢你有意为 ai-plugins 项目做出贡献！本指南将帮助你了解如何参与项目开发。

## 如何贡献

### 报告问题 (Bug Reports)

如果你发现了 bug，请：

1. 检查 [Issues](https://github.com/chenquan/ai-plugins/issues) 确认问题未被报告
2. 创建新的 Issue，包含：
   - 清晰的标题
   - 详细的问题描述
   - 复现步骤
   - 预期行为 vs 实际行为
   - 环境信息（操作系统、Claude Code 版本等）
   - 相关日志或截图

**Issue 模板**：

```markdown
### 问题描述
简要描述问题

### 复现步骤
1. 步骤一
2. 步骤二
3. ...

### 预期行为
应该发生什么

### 实际行为
实际发生了什么

### 环境信息
- 操作系统: [如 macOS 14.0]
- Claude Code 版本: [如 1.0.0]
- 插件版本: [如 0.2.0]

### 截图/日志
如果有相关的截图或日志，请附上
```

### 提出新功能 (Feature Requests)

如果你有好的想法：

1. 先在 [Discussions](https://github.com/chenquan/ai-plugins/discussions) 中讨论
2. 确认可行性后，创建 Feature Request Issue
3. 说明：
   - 功能描述和用例
   - 为什么这个功能重要
   - 可能的实现方案
   - 是否愿意自己实现

### 提交代码 (Pull Requests)

我们欢迎各种形式的贡献！

#### 开发流程

1. **Fork 仓库**
   ```bash
   # 在 GitHub 上 Fork 本仓库
   ```

2. **克隆你的 Fork**
   ```bash
   git clone https://github.com/YOUR_USERNAME/ai-plugins.git
   cd common
   ```

3. **创建特性分支**
   ```bash
   git checkout -b feature/your-feature-name
   # 或修复分支
   git checkout -b fix/issue-number
   ```

4. **进行开发**
   - 遵循代码规范
   - 添加必要的文档
   - 更新 CHANGELOG.md

5. **提交更改**
   ```bash
   # 使用 Conventional Commits 规范
   git commit -m "feat: add new code review agent"
   # 或
   git commit -m "fix: resolve SQL injection detection issue"
   ```

6. **推送到你的 Fork**
   ```bash
   git push origin feature/your-feature-name
   ```

7. **创建 Pull Request**
   - 在 GitHub 上创建 PR
   - 填写 PR 模板
   - 等待代码审查

#### 提交信息规范

我们遵循 [Conventional Commits](https://www.conventionalcommits.org/) 规范：

**格式**：`<type>: <description>`

**类型**：
- `feat`: 新功能
- `fix`: Bug 修复
- `docs`: 文档更新
- `style`: 代码格式（不影响功能）
- `refactor`: 重构
- `perf`: 性能优化
- `test`: 测试相关
- `chore`: 构建/工具配置

**示例**：
```bash
feat: add performance optimization agent
fix: resolve issue with file detection in /cr command
docs: update installation instructions
refactor: simplify command parsing logic
```

#### 添加新命令

在 `commands/` 目录创建新的 Markdown 文件：

```markdown
---
description: 命令描述
---

# 命令名称

详细的命令实现逻辑...
```

#### 添加新代理

在 `agents/` 目录创建新的 Markdown 文件：

```markdown
---
description: 代理描述
model: sonnet
---

# 代理名称

代理的角色定义、工作流程等...
```

#### 添加新技能

在 `skills/` 目录创建新的子目录和 `SKILL.md`：

```
skills/
└── your-skill/
    └── SKILL.md
```

### 代码审查

所有 PR 都需要经过代码审查：

1. **自我审查**
   - 代码符合项目规范
   - 文档完整
   - 没有调试代码

2. **响应反馈**
   - 及时回应审查意见
   - 修改被指出的问题
   - 保持礼貌和专业

3. **审查标准**
   - 功能正确性
   - 代码质量
   - 文档完整性
   - 向后兼容性

### 测试

虽然本项目主要是配置文件，但仍需测试：

1. **手动测试**
   - 在 Claude Code 中测试新功能
   - 验证所有示例可正常工作
   - 检查文档链接

2. **跨平台测试**（如适用）
   - macOS
   - Linux
   - Windows

### 文档

确保文档完整：

1. **README.md**
   - 更新功能列表
   - 添加使用示例

2. **CHANGELOG.md**
   - 添加版本变更记录

3. **代码注释**
   - 复杂逻辑添加注释
   - Markdown 文件保持清晰

## 开发环境设置

### 必需工具
- Git
- 文本编辑器（推荐 VS Code）
- Claude Code（用于测试）

### 可选工具
- GitHub CLI (`gh`) - 用于 PR 和 Issue 管理

### 项目结构

```
ai-plugins/
├── commands/           # 斜杠命令
├── agents/            # 子代理
├── skills/            # 技能包
├── templates/         # 模板文件
├── docs/             # 文档
├── mcp-servers/      # MCP 服务器配置
├── hooks/            # 钩子配置
├── images/           # 截图和图片
├── .github/          # GitHub 配置（可选）
├── plugin.json       # 插件清单
├── marketplace.json  # Marketplace 清单
├── README.md         # 项目说明
├── CHANGELOG.md      # 变更日志
└── LICENSE           # 许可证
```

## 发布流程

详见 [RELEASE.md](RELEASE.md)

## 社区规范

### 行为准则

- 尊重他人
- 建设性反馈
- 乐于助人
- 保持耐心

### 沟通渠道

- **GitHub Issues**: Bug 报告和功能请求
- **GitHub Discussions**: 一般讨论和问题
- **Pull Requests**: 代码贡献

## 获取帮助

如果需要帮助：

1. 查看 [文档](README.md)
2. 搜索 [Issues](https://github.com/chenquan/ai-plugins/issues)
3. 在 [Discussions](https://github.com/chenquan/ai-plugins/discussions) 提问
4. 联系维护者：chenquan

## 认可贡献者

所有贡献者将被列在项目中。感谢你的贡献！

## 许可证

通过贡献代码，你同意你的贡献将在 [MIT License](LICENSE) 下发布。

---

再次感谢你的贡献！🎉

**最后更新**: 2026-03-21
