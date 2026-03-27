# Plugin Schema Specification

This document defines the expected schema for all plugins in this marketplace.

## Directory Structure

Each plugin must follow this structure:

```
plugin-name/
├── .claude-plugin/           # Required: Metadata directory
│   └── plugin.json          # Required: Plugin manifest
├── commands/                 # Optional: Command definitions
│   ├── command1.md
│   └── command2.md
├── agents/                   # Optional: Agent definitions
│   ├── agent1.md
│   └── agent2.md
├── skills/                   # Optional: Agent Skills
│   ├── skill-name/
│   │   └── SKILL.md
│   └── another-skill/
│       ├── SKILL.md
│       └── scripts/
├── hooks/                    # Optional: Hook configurations
│   ├── hooks.json           # Main hook config
│   └── additional-hooks.json
├── .mcp.json                # Optional: MCP server definitions
├── scripts/                 # Optional: Hook and utility scripts
│   ├── script1.sh
│   └── script2.py
├── LICENSE                  # Optional: License file
├── CHANGELOG.md             # Optional: Version history
└── README.md                # Optional: Documentation
```

## Required Files

### `.claude-plugin/plugin.json`

The plugin manifest file is **required** for all plugins. It must contain valid JSON with the following structure:

#### Required Fields

- **`name`** (string): Plugin identifier (kebab-case recommended)
- **`version`** (string): Semantic version (e.g., "1.2.0")
- **`description`** (string): Brief description of the plugin

#### Optional Fields

- **`author`** (object): Author information
  - `name` (string, required): Author's name
  - `email` (string, optional): Author's email
  - `url` (string, optional): Author's website or GitHub profile
- **`homepage`** (string): Plugin documentation URL
- **`repository`** (string): Git repository URL
- **`license`** (string): License identifier (e.g., "MIT", "Apache-2.0")
- **`keywords`** (array of strings): Searchable keywords
- **`category`** (string): Plugin category for organization
- **`commands`** (string or array of strings): Custom command locations
- **`agents`** (string): Custom agent directory location
- **`hooks`** (string): Custom hook configuration file location
- **`mcpServers`** (string): Custom MCP server configuration file location

## Available Plugins

### Commit Plugin
智能提交 git 更改 - 自动生成符合 Conventional Commits 规范的提交信息

**Install:**
```bash
/plugin install commit
```

**Usage:**
```bash
/commit
```

### Code Review Plugin
智能代码审查工具 - 支持 4 种审查模式，检查代码质量、安全性、性能和测试覆盖

**Install:**
```bash
/plugin install code-review
```

**Usage:**
```bash
/cr              # 审查未提交的更改
/cr src/main.go  # 审查指定文件
/cr --pr 123     # 审查 Pull Request
```

### Code Review Expert Plugin
专家级代码审查顾问 - 提供深度分析、长期规划和团队培训

**Install:**
```bash
/plugin install code-review-expert
```

**Usage:**
```
请使用 code-review-expert 代理帮我审查代码
```

## Best Practices

1. **Use semantic versioning** for the `version` field
2. **Include descriptive keywords** for better discoverability
3. **Provide author information** for attribution and support
4. **Link to repository** for open-source contributions
5. **Document your plugin** with a README.md
6. **Track changes** with CHANGELOG.md
7. **Use clear names** that describe the plugin's purpose
8. **Keep descriptions concise** but informative

## License

MIT License - See LICENSE file for details
