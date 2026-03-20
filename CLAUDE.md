# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a **Claude Code plugin collection** that extends Claude Code's capabilities through slash commands, subagents, skills, MCP servers, and hooks. The project contains no executable code - it's entirely configuration and documentation files that Claude Code loads and interprets.

**Key Architecture**: All functionality is defined through Markdown files with YAML frontmatter and JSON configuration files. Claude Code reads these files to provide enhanced capabilities.

## Core Components

### 1. Slash Commands (`commands/`)
Markdown files that define `/command` shortcuts. Each file must have:
- **Frontmatter**: `description` field (required)
- **Content**: Detailed instructions for Claude to execute when command is invoked

**Example structure**:
```markdown
---
description: Command description here
---

# Command Name

Instructions for Claude to execute...
```

**Current commands**:
- `/commit` - Smart git commit with Conventional Commits
- `/cr` - Intelligent code review supporting 4 modes and 20+ languages

### 2. Subagents (`agents/`)
Specialized AI assistants that run in isolated contexts. Each file must have:
- **Frontmatter**: `description` (required), `model` (optional: sonnet/opus/haiku)
- **Content**: Agent's role, capabilities, workflow, and expertise

**Example structure**:
```markdown
---
description: Agent description
model: sonnet
---

# Agent Name

Agent's role definition, workflow, and knowledge base...
```

**Current agents**:
- `code-review-expert` - Expert-level code review with deep analysis, long-term planning, and team training

### 3. Skills (`skills/`)
On-demand knowledge packages that load only when needed. Structure:
- Each skill in its own directory
- Must contain `SKILL.md` with `description` frontmatter
- Can include additional resources (scripts, references)

### 4. MCP Servers (`mcp-servers/`)
External tool integrations via Model Context Protocol. Configured in `config.json`:
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

### 5. Hooks (`hooks/`)
Automated workflows triggered at specific points. Configured in `hooks.json`:
```json
{
  "beforeEdit": [
    {
      "description": "Description",
      "command": "command-to-run"
    }
  ]
}
```

## Adding New Components

### New Slash Command
Create `commands/your-command.md`:
```markdown
---
description: What this command does
---

Detailed instructions for execution...
```

### New Subagent
Create `agents/your-agent.md`:
```markdown
---
description: Agent purpose
model: sonnet
---

Agent definition with:
- Core capabilities
- Workflow/process
- Knowledge base
- Output formats
```

### New Skill
Create directory `skills/your-skill/` with `SKILL.md`:
```markdown
---
description: When/how this skill loads
---

Skill content...
```

## File Naming Conventions

- **Commands**: Descriptive names (e.g., `commit.md`, `review.md` or `cr.md`)
- **Agents**: Descriptive names with `-` separator (e.g., `code-review-expert.md`)
- **Skills**: Directory with descriptive name, containing `SKILL.md`
- **Templates**: Descriptive names with `-` separator in `templates/` directory

## Frontmatter Requirements

All markdown files MUST have YAML frontmatter at the top:
```yaml
---
description: Required description
model: Optional model specification (agents only)
---
```

## Plugin Metadata

`plugin.json` contains:
- Basic info (name, version, description, author, repository, license)
- Categories for discoverability
- Features list (commands, agents, documentation)

Update version in `plugin.json` when making significant changes.

## Documentation

- **README.md** - User-facing overview and quick start
- **docs/** - Detailed guides (e.g., `CODE_REVIEW_AGENT_GUIDE.md`)
- **templates/** - Reusable templates for tracking, planning, reporting

## Key Patterns

### Command Implementation Pattern
Commands should:
1. Check prerequisites (e.g., git repository)
2. Gather context (file status, changes)
3. Analyze and generate intelligent output
4. Present preview and await confirmation (if destructive)
5. Execute with clear error handling

### Agent Implementation Pattern
Agents should:
1. Define clear role and capabilities
2. Establish workflow/steps
3. Provide multiple strategies (quick, deep, training, etc.)
4. Include knowledge base references
5. Generate structured output with action items

### Multi-language Support
Both `/commit` and `/cr` support 20+ programming languages. When adding similar functionality:
- Consider file extensions and naming conventions
- Handle language-specific patterns (test files, config files)
- Provide language-specific examples

## Important Notes

- **No build process**: This is a pure configuration/documentation project
- **No tests**: Validation happens through Claude Code's interpretation
- **No dependencies**: Plugin structure is self-contained
- **Git-based**: Version control tracks all changes
- **User-facing**: All content is written for Claude to present to users

## File Path References

When referencing other files in documentation:
- Commands: `commands/filename.md` or just `filename` if in context
- Agents: `agents/filename.md`
- Docs: `docs/filename.md`
- Templates: `templates/filename.md`

Example cross-reference format: "See [CODE_REVIEW_AGENT_GUIDE.md](docs/CODE_REVIEW_AGENT_GUIDE.md)"

## Template System

The `templates/` directory contains reusable markdown templates for:
- Issue tracking (`review-tracker.md`)
- Training plans (`training-plan.md`)
- Improvement reports (`improvement-report.md`)

These are used by agents and can be customized per project needs.
