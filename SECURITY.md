# 安全政策 (Security Policy)

## 支持的版本

目前 actively supported 的版本：

| 版本 | 支持状态 |
| ---- | -------- |
| 0.2.x | ✅ 支持 |
| 0.1.x | ⚠️ 仅重要安全更新 |

## 报告安全漏洞

如果你发现了安全漏洞，**请不要公开报告**。请按照以下步骤私密报告：

### 报告流程

1. **发送邮件**
   - 收件人: security@example.com（替换为实际邮箱）
   - 主题: [Security] ai-plugins 安全漏洞报告
   - 内容:
     - 漏洞描述
     - 影响范围
     - 复现步骤
     - 可能的修复建议

2. **或使用私有漏洞报告**
   - 在 GitHub 上创建私有 Security Advisory
   - 访问: https://github.com/chenquan/ai-plugins/security/advisories

### 你应该期望什么？

1. **确认收到** (24 小时内)
   - 我们会确认收到你的报告
   - 评估严重程度

2. **评估和修复** (7 天内)
   - 我们会调查问题
   - 制定修复方案
   - 开发补丁

3. **发布补丁**
   - 在确认安全后发布修复版本
   - 给予合理的时间让用户更新

4. **公开披露**
   - 在修复版本发布后公开漏洞详情
   - 感谢你的贡献

## 安全最佳实践

### 作为用户

1. **保持更新**
   - 定期更新到最新版本
   - 关注安全公告

2. **审查代码**
   - 本插件是开源的，你可以审查所有代码
   - 不要运行来源不明的插件

3. **保护敏感信息**
   - 不要在代码中硬编码密钥
   - 使用环境变量管理敏感配置

### 作为开发者

1. **代码审查**
   - 所有代码变更需要经过审查
   - 特别关注安全性相关修改

2. **依赖管理**
   - 定期更新依赖项
   - 检查依赖项的安全公告

3. **测试**
   - 测试错误处理
   - 验证输入清理
   - 检查权限控制

## 已知安全问题

### 当前无已知问题

目前没有已知的未修复安全漏洞。

### 历史问题

详见 GitHub Security Advisories:
https://github.com/chenquan/ai-plugins/security/advisories

## 安全功能

### 输入验证

- 所有命令输入都经过验证
- 文件路径检查防止路径遍历
- 防止命令注入

### 敏感信息保护

- 不会在日志中记录敏感信息
- 检测硬编码密钥并警告
- 支持环境变量配置

### 权限控制

- 插件在用户权限下运行
- 不需要额外的系统权限
- 所有操作都是显式的

## 安全更新

### 更新通知

当发布安全更新时：

1. **GitHub Release**
   - 标记为 Security Update
   - 包含修复详情

2. **Change Log**
   - 在 CHANGELOG.md 中记录
   - 标记安全修复

3. **公告**
   - 在 README 中添加警告
   - 通知所有已知用户

### 更新优先级

- **严重**: 立即更新 (48 小时内)
- **高危**: 尽快更新 (1 周内)
- **中危**: 计划更新 (下次正常更新)

## 安全相关资源

### 学习资源

- [OWASP Top 10](https://owasp.org/www-project-top-ten/)
- [CWE Top 25](https://cwe.mitre.org/top25/)
- [GitHub Security Best Practices](https://docs.github.com/en/code-security)

### 工具

- [npm audit](https://docs.npmjs.com/cli/audit) - 依赖安全检查
- [Snyk](https://snyk.io/) - 漏洞扫描
- [CodeQL](https://codeql.github.com/) - 代码分析

## 联系方式

**安全问题**: security@example.com（请替换为实际邮箱）
**一般问题**: https://github.com/chenquan/ai-plugins/issues

## 致谢

感谢所有通过负责任的披露程序报告安全问题的研究人员！

---

**最后更新**: 2026-03-21
