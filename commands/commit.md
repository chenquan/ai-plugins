---
description: 自动提交 git 更改并推送到远程仓库
---

# Git 自动提交命令

## 使用说明

当用户执行 `/commit` 命令时，按照以下流程自动化 Git 提交流程：

### 1. 检查 Git 仓库

首先检查当前目录是否是 Git 仓库：

```bash
git rev-parse --git-dir
```

如果返回错误（非 Git 仓库），提示用户：
```
❌ 当前目录不是 Git 仓库
💡 提示：请先初始化 Git 仓库（git init）或切换到 Git 仓库目录
```

### 2. 查看 Git 状态

检查是否有待提交的更改：

```bash
git status
```

如果工作区是干净的（没有更改），提示用户：
```
✅ 工作区干净，没有需要提交的更改
```

然后退出流程。

### 3. 获取更改详情

如果有更改，运行以下命令获取详细信息：

```bash
# 获取更改统计
git diff --stat

# 获取更改的文件列表
git status --short
```

### 4. 分析更改内容

分析以下信息来生成智能提交信息：

#### 4.1 确定更改类型

根据文件路径和扩展名推断提交类型：

**通用文件类型**：

| 文件模式 | 提交类型 | 判断逻辑 |
|---------|---------|---------|
| `tests/`, `__tests__/`, `test/`, `spec/`, `*.test.*`, `*.spec.*`, `*_test.*`, `*_spec.*` | `test:` | 测试文件 |
| `README.md`, `CHANGELOG.md`, `CONTRIBUTING.md`, `docs/`, `*.md` (排除特殊文件) | `docs:` | 文档文件 |
| `*.css`, `*.scss`, `*.sass`, `*.less`, `*.styl` (仅格式化) | `style:` | 样式文件 |

**编程语言源代码文件**：

| 语言 | 文件扩展名 | 主要类型 |
|------|-----------|---------|
| Go | `*.go` | `feat:` / `fix:` / `refactor:` |
| Java | `*.java` | `feat:` / `fix:` / `refactor:` |
| Kotlin | `*.kt`, `*.kts` | `feat:` / `fix:` / `refactor:` |
| C | `*.c`, `*.h` | `feat:` / `fix:` / `refactor:` |
| C++ | `*.cpp`, `*.cc`, `*.cxx`, `*.hpp`, `*.hh`, `*.hxx` | `feat:` / `fix:` / `refactor:` |
| C# | `*.cs` | `feat:` / `fix:` / `refactor:` |
| PHP | `*.php` | `feat:` / `fix:` / `refactor:` |
| Python | `*.py` | `feat:` / `fix:` / `refactor:` |
| Ruby | `*.rb` | `feat:` / `fix:` / `refactor:` |
| Rust | `*.rs` | `feat:` / `fix:` / `refactor:` |
| Swift | `*.swift` | `feat:` / `fix:` / `refactor:` |
| JavaScript | `*.js`, `*.mjs`, `*.cjs` | `feat:` / `fix:` / `refactor:` |
| TypeScript | `*.ts`, `*.tsx` | `feat:` / `fix:` / `refactor:` |
| Dart | `*.dart` | `feat:` / `fix:` / `refactor:` |
| Scala | `*.scala` | `feat:` / `fix:` / `refactor:` |
| R | `*.r`, `*.R` | `feat:` / `fix:` / `refactor:` |
| Shell | `*.sh`, `*.bash`, `*.zsh` | `chore:` / `feat:` / `fix:` |
| Lua | `*.lua` | `feat:` / `fix:` / `refactor:` |
| Perl | `*.pl`, `*.pm` | `feat:` / `fix:` / `refactor:` |
| Haskell | `*.hs` | `feat:` / `fix:` / `refactor:` |

**测试文件模式**（按语言）：

| 语言 | 测试文件模式 | 示例 |
|------|-------------|------|
| Go | `*_test.go`, `tests/` | `user_test.go`, `integration_test.go` |
| Java | `*Test.java`, `*Tests.java`, `src/test/` | `UserServiceTest.java` |
| Kotlin | `*Test.kt`, `*Spec.kt` | `UserTest.kt`, `UserSpec.kt` |
| Python | `test_*.py`, `*_test.py`, `tests/`, `conftest.py` | `test_user.py`, `user_test.py` |
| JavaScript/TypeScript | `*.test.js`, `*.spec.js`, `*.test.ts`, `*.spec.ts`, `__tests__/` | `user.test.js`, `user.spec.ts` |
| Rust | `*test*.rs`, `tests/` | `integration_test.rs` |
| PHP | `*Test.php`, `*Spec.php`, `tests/` | `UserTest.php` |
| Ruby | `*_test.rb`, `*_spec.rb`, `spec/`, `test/` | `user_test.rb`, `user_spec.rb` |
| C/C++ | `*test*.c`, `*test*.cpp`, `test/` | `test_main.c` |
| C# | `*Test.cs`, `*Tests.cs`, `Tests/` | `UserServiceTest.cs` |
| Swift | `*Test*.swift`, `Tests/` | `UserTests.swift` |
| Dart | `*_test.dart`, `test/` | `user_test.dart` |

**配置和依赖文件**（按语言/框架）：

| 语言/框架 | 配置文件模式 |
|----------|-------------|
| Node.js | `package.json`, `package-lock.json`, `yarn.lock`, `pnpm-lock.yaml`, `npm-shrinkwrap.json` |
| TypeScript | `tsconfig.json`, `*.d.ts` |
| Go | `go.mod`, `go.sum`, `Gopkg.toml`, `Gopkg.lock` |
| Java/Maven | `pom.xml`, `pom.properties` |
| Java/Gradle | `build.gradle`, `build.gradle.kts`, `settings.gradle`, `gradle.properties` |
| Python | `requirements.txt`, `setup.py`, `Pipfile`, `pyproject.toml`, `poetry.lock`, `environment.yml` |
| Ruby | `Gemfile`, `Gemfile.lock`, `*.gemspec` |
| Rust | `Cargo.toml`, `Cargo.lock` |
| PHP | `composer.json`, `composer.lock` |
| C#/.NET | `*.csproj`, `*.sln`, `packages.config`, `project.json` |
| Swift | `Package.swift`, `Podfile`, `Podfile.lock`, `Cartfile` |
| Dart | `pubspec.yaml`, `pubspec.lock` |
| Scala | `build.sbt`, `project/*.scala` |
| 通用配置 | `*.config.js`, `*.config.ts`, `*.config.json`, `*.config.yaml`, `*.config.yml`, `.eslintrc*`, `.prettierrc*`, `.babelrc*`, `tslint.json` |
| CI/CD | `.github/workflows/*`, `.gitlab-ci.yml`, `Jenkinsfile`, `azure-pipelines.yml`, `circleci-config.yml` |
| Docker | `Dockerfile`, `docker-compose.yml`, `docker-compose.yaml`, `.dockerignore` |
| Kubernetes | `*.yaml`, `*.yml` (在 k8s/ 目录) |
| Terraform | `*.tf` |
| Ansible | `*.yml`, `*.yaml` (在 ansible/ 或 playbooks/ 目录) |

**模板和视图文件**：

| 框架 | 文件模式 |
|------|---------|
| HTML | `*.html`, `*.htm` |
| Vue | `*.vue` |
| React/JSX | `*.jsx`, `*.tsx` |
| Angular | `*.component.html`, `*.component.ts`, `*.component.css` |
| Svelte | `*.svelte` |
| Pug/Jade | `*.pug` |
| EJS | `*.ejs` |
| Handlebars | `*.hbs`, `*.handlebars` |
| Blade (Laravel) | `*.blade.php` |
| Twig | `*.twig` |
| Jinja2 | `*.j2`, `*.jinja2` |
| ERB (Ruby) | `*.erb` |

**数据库相关**：

| 类型 | 文件模式 |
|------|---------|
| SQL | `*.sql` |
| Migration | `migrations/*`, `*_migration.sql`, `*_migration.rb` |
| Schema | `schema.prisma`, `schema.sql` |

**样式文件**：

| 类型 | 文件模式 |
|------|---------|
| CSS | `*.css` |
| Sass/SCSS | `*.scss`, `*.sass` |
| Less | `*.less` |
| Stylus | `*.styl` |
| Styled-components | 通常在 `*.js`, `*.ts`, `*.tsx` 文件中 |

**更改类型判断规则**：

1. **测试文件** (`test:`) - 优先级最高
   - 匹配任何测试文件模式
   - 包含测试代码的修改

2. **文档文件** (`docs:`) - 第二优先级
   - README、CHANGELOG、文档目录
   - 仅包含注释或文档字符串的代码更改

3. **配置文件** (`chore:`) - 第三优先级
   - 锁文件、配置文件、依赖文件
   - 构建脚本、CI/CD 配置

4. **纯样式/格式更改** (`style:`)
   - 仅包含空白字符、格式化的更改
   - CSS、SCSS 等样式文件的格式化

5. **修复问题** (`fix:`)
   - 代码中包含 "fix", "bug", "issue", "error", "exception" 等关键词
   - 删除不必要的代码
   - 修改错误处理逻辑

6. **新功能** (`feat:`)
   - 添加新文件、新函数、新类
   - 扩展功能实现的代码

7. **重构** (`refactor:`)
   - 代码结构重组
   - 变量/函数重命名
   - 提取公共逻辑

8. **其他** (`chore:`)
   - 无法明确分类的更改

#### 4.2 检测更改内容

使用 `git diff` 分析具体更改：

```bash
# 获取暂存区的更改
git diff --cached

# 获取工作区的更改
git diff
```

#### 4.3 生成提交信息

根据分析结果生成符合 Conventional Commits 规范的提交信息：

**格式**: `<type>: <description>`

**提交类型优先级**（从高到低）：
1. `test:` - 测试相关
2. `docs:` - 文档相关
3. `fix:` - 修复问题
4. `feat:` - 新功能
5. `refactor:` - 重构
6. `style:` - 样式/格式
7. `chore:` - 构建/工具

**提交信息生成规则**：

- **测试文件**: `test: [action] [component/feature]`
  - 通用示例: `test: add unit tests for user authentication`
  - Go: `test: add table-driven tests for user service`
  - Java: `test: add JUnit tests for order controller`
  - Python: `test: add pytest tests for data pipeline`
  - JavaScript/TypeScript: `test: add Jest tests for auth middleware`
  - Rust: `test: add unit tests for user module`

- **文档文件**: `docs: [action] [what]`
  - 通用示例: `docs: update installation instructions`
  - 示例: `docs: add API documentation for endpoints`
  - 示例: `docs: fix typo in README`
  - Go: `docs: add godoc comments for package`
  - Java: `docs: update Javadoc for public API`
  - Python: `docs: update docstrings for module`

- **配置/依赖**: `chore: [action] [what]`
  - Node.js: `chore: update dependencies to latest versions`
  - Go: `chore: update Go modules to v1.20`
  - Java: `chore: upgrade Maven dependencies`
  - Python: `chore: update pip requirements`
  - Rust: `chore: update Cargo dependencies`
  - 通用示例: `chore: configure ESLint rules`
  - 通用示例: `chore: add TypeScript configuration`

- **修复问题**: `fix: [what]`
  - Go: `fix: handle nil pointer dereference in user service`
  - Java: `fix: resolve NullPointerException in order processing`
  - Python: `fix: handle KeyError in data parsing`
  - JavaScript: `fix: resolve async/await promise rejection`
  - C/C++: `fix: resolve segmentation fault in buffer handling`
  - PHP: `fix: resolve undefined array key warning`
  - Rust: `fix: handle panic in unwrap() call`
  - 通用示例: `fix: correct API endpoint URL`

- **新功能**: `feat: [what]`
  - Go: `feat: add HTTP middleware for authentication`
  - Java: `feat: implement Spring Security configuration`
  - Python: `feat: add Django REST API endpoints`
  - JavaScript/TypeScript: `feat: add Express API routes`
  - C#: `feat: implement ASP.NET Core controllers`
  - PHP: `feat: add Laravel authentication system`
  - Rust: `feat: implement async database connection pool`
  - C++: `feat: add memory-efficient data structures`
  - Swift: `feat: implement SwiftUI user interface`
  - Kotlin: `feat: add Android service component`

- **重构**: `refactor: [what]`
  - Go: `refactor: extract interface from concrete implementation`
  - Java: `refactor: apply dependency injection pattern`
  - Python: `refactor: simplify class inheritance hierarchy`
  - JavaScript: `refactor: convert callbacks to async/await`
  - C++: `refactor: modernize to C++20 features`
  - Ruby: `refactor: extract service objects from controllers`
  - Rust: `refactor: reduce code duplication with macros`
  - 通用示例: `refactor: simplify data validation logic`
  - 通用示例: `refactor: extract common utilities to separate module`

- **样式/格式**: `style: [what]`
  - 通用示例: `style: format code with Prettier`
  - Go: `style: run gofmt on package`
  - Python: `style: apply Black formatter`
  - Java: `style: apply Google Java Style Guide`
  - Rust: `style: run rustfmt on crate`
  - C#: `style: apply dotnet format`
  - 通用示例: `style: standardize indentation`
  - 通用示例: `style: apply consistent naming conventions`

**描述生成规则**：
- 使用原形小写动词开头（add, update, fix, remove, improve 等）
- 简洁明了，不超过 50 字符
- 描述"是什么"而不是"为什么"
- 使用中文或英文（根据项目语言）

### 5. 显示预览

向用户显示完整的预览信息：

```
🔍 检测到以下更改：
<git status --short 的输出>

📊 更改统计：
<git diff --stat 的输出>

💡 建议的提交信息：
<生成的提交信息>

📝 完整命令预览：
git add -A
git commit -m "<生成的提交信息>"
git push

⚠️ 即将执行上述命令，这将：
• 添加所有更改到暂存区
• 创建新的提交
• 推送到远程仓库

是否确认执行？
• 回复 "确认" 或 "yes" 继续
• 回复新的提交信息来修改
• 回复 "cancel" 或 "取消" 中止
```

### 6. 等待用户确认

等待用户回复：

- **确认执行**: 用户回复 "确认"、"yes"、"y" 或类似确认词
  - 继续执行提交和推送

- **修改提交信息**: 用户回复新的提交信息
  - 使用用户提供的提交信息
  - 再次显示预览
  - 继续等待确认

- **取消操作**: 用户回复 "cancel"、"取消"、"no" 等
  - 中止流程
  - 显示: `❌ 操作已取消`

### 7. 执行提交和推送

确认后，按以下顺序执行命令：

```bash
# 1. 添加所有更改
git add -A

# 2. 创建提交（使用最终确认的提交信息）
git commit -m "<最终提交信息>"

# 3. 推送到远程仓库
git push
```

### 8. 显示执行结果

根据执行结果显示相应信息：

**成功**：
```
✅ 提交成功！

📝 提交信息: <提交信息>
🌿 提交哈希: <commit hash>
📤 推送成功
```

**部分成功**（提交成功但推送失败）：
```
⚠️ 提交成功，但推送失败

📝 提交信息: <提交信息>
🌿 提交哈希: <commit hash>

❌ 推送错误: <错误信息>

💡 提示：你可以稍后手动执行 git push
```

**失败**（提交失败）：
```
❌ 提交失败

错误信息: <错误信息>

💡 常见解决方案：
• 检查是否有 merge conflict 需要解决
• 确认 Git 配置正确（git config user.name 和 user.email）
• 查看详细的错误信息以确定问题
```

### 9. 错误处理

针对常见错误提供友好的提示：

#### 9.1 非 Git 仓库
```
❌ 当前目录不是 Git 仓库

💡 解决方案：
• 初始化 Git 仓库: git init
• 或切换到已有 Git 仓库的目录
```

#### 9.2 未配置 Git 用户信息
```
❌ Git 用户信息未配置

💡 请先配置 Git 用户信息：
git config user.name "Your Name"
git config user.email "your.email@example.com"

或使用全局配置：
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"
```

#### 9.3 Merge Conflict
```
❌ 存在未解决的 merge conflict

💡 请先解决冲突：
1. 查看冲突文件: git status
2. 解决冲突标记（<<<<<<<, =======, >>>>>>>）
3. 标记冲突已解决: git add <文件>
4. 然后重新执行 /commit
```

#### 9.4 网络错误（推送失败）
```
❌ 推送到远程仓库失败

可能的原因：
• 网络连接问题
• 远程仓库不存在或无权限
• 远程仓库 URL 配置错误

💡 请检查：
• 网络连接是否正常
• 远程仓库配置: git remote -v
• 是否有推送权限
```

#### 9.5 远程分支不存在
```
❌ 远程分支不存在

💡 解决方案：
• 首次推送使用: git push -u origin <分支名>
• 或设置上游分支: git push --set-upstream origin <分支名>
```

### 10. 特殊情况处理

#### 10.1 工作区干净
```
✅ 工作区干净，没有需要提交的更改

💡 提示：
• 如果想修改最后一次提交，可以使用: git commit --amend
• 如果想查看最近的提交，使用: git log
```

#### 10.2 仅提交不推送

如果用户想在确认时跳过推送，可以回复 "skip" 或 "仅提交"：

```
📝 仅提交，不推送

git add -A
git commit -m "<提交信息>"

是否确认？
```

## 实现要点

1. **安全第一**: 所有操作前必须显示预览，等待用户确认
2. **智能推断**: 基于文件类型和更改内容生成准确的提交信息
3. **多语言支持**: 支持检测 20+ 编程语言（Go、Java、Python、JavaScript、TypeScript、C/C++、C#、PHP、Ruby、Rust、Swift、Kotlin、Dart 等）
4. **友好交互**: 使用清晰的中文提示和 emoji 标记
5. **错误处理**: 针对常见错误提供明确的解决方案
6. **灵活性**: 允许用户修改提交信息，支持跳过推送
7. **规范遵循**: 严格遵守 Conventional Commits 规范

## 支持的编程语言和框架

### 编程语言（20+）
- **Go** - `*.go`, `*_test.go`, `go.mod`, `go.sum`
- **Java** - `*.java`, `*Test.java`, `pom.xml`, `build.gradle`
- **Kotlin** - `*.kt`, `*.kts`
- **Python** - `*.py`, `test_*.py`, `*_test.py`, `requirements.txt`, `setup.py`
- **JavaScript** - `*.js`, `*.mjs`, `*.cjs`, `*.test.js`, `package.json`
- **TypeScript** - `*.ts`, `*.tsx`, `*.test.ts`, `tsconfig.json`
- **C** - `*.c`, `*.h`, `*test*.c`
- **C++** - `*.cpp`, `*.cc`, `*.hpp`, `*.hh`, `*test*.cpp`
- **C#** - `*.cs`, `*Test.cs`, `*.csproj`
- **PHP** - `*.php`, `*Test.php`, `composer.json`
- **Ruby** - `*.rb`, `*_test.rb`, `*_spec.rb`, `Gemfile`
- **Rust** - `*.rs`, `*test*.rs`, `Cargo.toml`
- **Swift** - `*.swift`, `*Test*.swift`, `Package.swift`
- **Dart** - `*.dart`, `*_test.dart`, `pubspec.yaml`
- **Scala** - `*.scala`, `build.sbt`
- **Shell** - `*.sh`, `*.bash`, `*.zsh`
- **Lua** - `*.lua`
- **Perl** - `*.pl`, `*.pm`
- **Haskell** - `*.hs`

### Web 框架
- **React** - `*.jsx`, `*.tsx`
- **Vue** - `*.vue`
- **Angular** - `*.component.ts`, `*.component.html`
- **Svelte** - `*.svelte`

### 后端框架
- **Django** (Python)
- **Flask** (Python)
- **FastAPI** (Python)
- **Express** (Node.js)
- **Spring Boot** (Java)
- **Gin/Echo** (Go)
- **Laravel** (PHP)
- **Rails** (Ruby)
- **ASP.NET Core** (C#)

### 配置文件识别
- **Node.js**: `package.json`, `yarn.lock`, `pnpm-lock.yaml`
- **Go**: `go.mod`, `go.sum`
- **Python**: `requirements.txt`, `Pipfile`, `pyproject.toml`, `poetry.lock`
- **Java**: `pom.xml`, `build.gradle`
- **Ruby**: `Gemfile`, `Gemfile.lock`
- **Rust**: `Cargo.toml`, `Cargo.lock`
- **PHP**: `composer.json`, `composer.lock`
- **C#/.NET**: `*.csproj`, `*.sln`
- **Docker**: `Dockerfile`, `docker-compose.yml`
- **Kubernetes**: `*.yaml`, `*.yml`

## 执行流程总结

```
开始
  ↓
检查是否 Git 仓库 → 否 → 提示初始化 → 结束
  ↓ 是
检查是否有更改 → 否 → 提示工作区干净 → 结束
  ↓ 是
获取更改详情（git status, git diff --stat）
  ↓
分析文件类型和更改内容
  ↓
生成智能提交信息
  ↓
显示预览（文件、统计、提交信息、命令）
  ↓
等待用户确认
  ↓
用户确认？
  ├─ 修改提交信息 → 重新显示预览 → 等待确认
  ├─ 取消 → 提示已取消 → 结束
  └─ 确认 → 继续执行
      ↓
  执行 git add -A
      ↓
  执行 git commit
      ↓
  执行 git push
      ↓
  显示结果 → 结束
```
