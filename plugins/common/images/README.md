# Screenshots & Images

本目录用于存放插件的截图和演示图片。

## 需要的截图

### 1. screenshot-1.png
**描述**: 使用 `/commit` 命令智能提交代码的演示
**建议内容**:
- Claude Code 界面
- 执行 `/commit` 命令
- 显示生成的提交信息
- 用户确认和执行过程

**尺寸**: 1200x800 或类似比例

### 2. screenshot-2.png
**描述**: 使用 `/cr` 命令进行代码审查的演示
**建议内容**:
- Claude Code 界面
- 执行 `/cr` 命令
- 显示代码审查报告
- 问题列表和修复建议

**尺寸**: 1200x800 或类似比例

### 3. screenshot-3.png
**描述**: 代码审查专家提供深度分析和改进建议
**建议内容**:
- 调用 code-review-expert 代理
- 显示专家级分析结果
- 改进路线图和培训建议

**尺寸**: 1200x800 或类似比例

## 截图指南

### 准备工作
1. 确保在良好的环境下截图
2. 使用清晰的字体大小
3. 避免显示敏感信息
4. 保持界面简洁

### 工具推荐
- **macOS**: Cmd + Shift + 4（截图）
- **Windows**: Win + Shift + S（截图）
- **Linux**: gnome-screenshot 或类似工具

### 后期处理
- 调整大小到合适尺寸（推荐宽度 1200px）
- 添加必要的标注或箭头
- 导出为 PNG 格式
- 保持文件大小合理（< 500KB）

## 命名规范

- 使用小写字母和连字符
- 格式: `screenshot-{number}.png` 或 `{feature}-{action}.png`
- 示例:
  - `screenshot-1.png`
  - `code-review-analysis.png`
  - `commit-command-demo.png`

## 添加新截图

1. 将截图文件放入本目录
2. 更新 `marketplace.json` 中的 `screenshots` 部分
3. 更新 README.md 如果需要展示截图

## 注意事项

⚠️ **隐私保护**:
- 不要泄露个人邮箱、API 密钥等敏感信息
- 使用测试代码而非真实项目代码
- 模糊或遮挡任何敏感数据

⚠️ **版权**:
- 确保截图中的代码可以公开
- 避免显示专有信息

---

**需要帮助？** 请创建 Issue 或联系维护者。
