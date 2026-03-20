---
description: 智能代码审查工具 - 支持多种审查模式和全面的质量检查
---

# 代码审查命令 (`/cr`)

## 概述

`/cr` (code review) 是一个智能代码审查工具，支持 4 种审查模式，检查 4 个维度的代码质量，支持 20+ 编程语言，并提供详细的审查报告和可操作的修复建议。

## 使用说明

### 基本语法

```bash
/cr [选项] [参数...]
```

### 四种审查模式

#### 模式 1: 审查未提交的更改（默认）
```bash
/cr
```

审查当前工作区和暂存区的所有未提交更改。

#### 模式 2: 审查指定文件或目录
```bash
/cr <path> [path2...]
```

审查指定的文件或目录。

**示例**:
- `/cr src/main.go` - 审查单个文件
- `/cr src/ tests/` - 审查多个目录
- `/cr *.go` - 审查匹配的文件

#### 模式 3: 审查指定 Commit
```bash
/cr --commit <hash>
/cr @<hash>
/cr @HEAD~1
```

审查指定的 commit。

**示例**:
- `/cr --commit abc123` - 审查指定 commit
- `/cr @HEAD` - 审查最新 commit
- `/cr @HEAD~3` - 审查倒数第 3 个 commit

#### 模式 4: 审查 Pull Request
```bash
/cr --pr <PR-number>
/cr --pr <URL>
```

审查指定的 Pull Request。

**示例**:
- `/cr --pr 123` - 审查当前仓库的 PR #123
- `/cr --pr https://github.com/user/repo/pull/123` - 审查指定 URL 的 PR

**要求**: 需要安装 GitHub CLI (`gh`)

## 审查流程

### 1. 环境检查

```bash
# 检查 Git 仓库
git rev-parse --git-dir 2>/dev/null

# 如果失败，显示错误：
# ❌ 当前目录不是 Git 仓库
# 💡 提示：请先初始化 Git 仓库（git init）或切换到 Git 仓库目录
```

### 2. 检测审查模式

根据用户输入自动检测审查模式：

```bash
# 无参数 → 模式 1：未提交更改
if [ $# -eq 0 ]; then
    mode="uncommitted"
fi

# --commit 或 @ → 模式 3：commit 审查
if [[ "$1" =~ ^--commit$ ]] || [[ "$1" =~ ^@ ]]; then
    mode="commit"
fi

# --pr → 模式 4：PR 审查
if [[ "$1" =~ ^--pr$ ]]; then
    mode="pr"
fi

# 其他 → 模式 2：文件/目录审查
mode="files"
```

### 3. 收集代码信息

#### 模式 1: 未提交更改

```bash
# 1. 检查是否有更改
if ! git status --porcelain | grep -q .; then
    echo "✅ 工作区干净，没有需要审查的更改"
    exit 0
fi

# 2. 获取更改统计
git diff --stat

# 3. 获取更改的文件列表
git status --short

# 4. 获取详细 diff
git diff
git diff --cached
```

#### 模式 2: 指定文件/目录

```bash
# 1. 检查文件是否存在
for path in "$@"; do
    if [ ! -e "$path" ]; then
        echo "❌ 文件或目录不存在: $path"
        exit 1
    fi
done

# 2. 读取文件内容
cat "$@"
```

#### 模式 3: 指定 Commit

```bash
# 1. 验证 commit hash
commit_hash="${1#@}"  # 移除 @ 前缀
commit_hash="${commit_hash#--commit }"

if ! git rev-parse --verify "$commit_hash" >/dev/null 2>&1; then
    echo "❌ 无效的 commit hash: $commit_hash"
    exit 1
fi

# 2. 获取 commit 信息
git show --stat "$commit_hash"

# 3. 获取详细 diff
git show "$commit_hash"
```

#### 模式 4: Pull Request

```bash
# 1. 检查 GitHub CLI
if ! command -v gh &> /dev/null; then
    echo "❌ 需要安装 GitHub CLI (gh)"
    echo "💡 安装: https://cli.github.com/"
    exit 1
fi

# 2. 获取 PR 信息
pr_number="${1#--pr }"

# 从 URL 提取 PR 号码
if [[ "$pr_number" =~ github\.com/.*/pull/([0-9]+) ]]; then
    pr_number="${BASH_REMATCH[1]}"
fi

# 3. 获取 PR 详情
gh pr view "$pr_number" --json title,body,files

# 4. 获取 PR diff
gh pr diff "$pr_number"
```

### 4. 语言检测

```bash
detect_language() {
    local file=$1
    local ext="${file##*.}"
    local filename="${file##*/}"

    # 根据扩展名判断
    case "$ext" in
        go) echo "go" ;;
        java) echo "java" ;;
        kt|kts) echo "kotlin" ;;
        py) echo "python" ;;
        js|mjs|cjs) echo "javascript" ;;
        ts|tsx) echo "typescript" ;;
        c) echo "c" ;;
        cpp|cc|cxx) echo "cpp" ;;
        h|hh|hpp) echo "cpp" ;;
        cs) echo "csharp" ;;
        php) echo "php" ;;
        rb) echo "ruby" ;;
        rs) echo "rust" ;;
        swift) echo "swift" ;;
        dart) echo "dart" ;;
        scala) echo "scala" ;;
        sh|bash|zsh) echo "shell" ;;
        lua) echo "lua" ;;
        pl|pm) echo "perl" ;;
        hs) echo "haskell" ;;
        r|R) echo "r" ;;
        sql) echo "sql" ;;
        html|htm) echo "html" ;;
        css) echo "css" ;;
        scss|sass) echo "scss" ;;
        less) echo "less" ;;
        styl) echo "stylus" ;;
        vue) echo "vue" ;;
        jsx) echo "javascript" ;;
        json) echo "json" ;;
        xml|yaml|yml) echo "config" ;;
        *) echo "unknown" ;;
    esac
}
```

### 5. 执行审查分析

#### 5.1 代码质量检查

**检查项目**:
- 代码风格（命名规范、格式、复杂度）
- 最佳实践（语言特定的惯用法、设计模式）
- 代码异味（过长函数、重复代码、过度耦合）

**语言特定规则**:

##### Go
```go
// ❌ 不好的命名
var x1 string
func GetData() {}

// ✅ 好的命名
var userName string
func GetUserByID(id string) (*User, error) {}
```

##### Python
```python
# ❌ 不好的命名
def calc(x):
    return x * 2

# ✅ 好的命名
def calculate_total(amount: float) -> float:
    """Calculate total with tax."""
    return amount * 2
```

##### JavaScript
```javascript
// ❌ 使用 var
var data = [];

// ✅ 使用 const/let
const data = [];
let count = 0;
```

#### 5.2 安全性检查

**检查项目**:
- SQL 注入
- XSS (跨站脚本攻击)
- 命令注入
- 敏感信息泄露（硬编码密钥）
- 不安全的加密
- 不安全的依赖

**检测模式**:

##### SQL 注入
```go
// ❌ 不安全
query := "SELECT * FROM users WHERE id = " + userID

// ✅ 安全
query := "SELECT * FROM users WHERE id = ?"
args := []interface{}{userID}
```

```python
# ❌ 不安全
cursor.execute(f"SELECT * FROM users WHERE id = {user_id}")

# ✅ 安全
cursor.execute("SELECT * FROM users WHERE id = ?", (user_id,))
```

```javascript
// ❌ 不安全
const query = `SELECT * FROM users WHERE id = ${userId}`;

// ✅ 安全
const query = 'SELECT * FROM users WHERE id = ?';
```

##### XSS
```javascript
// ❌ 不安全
element.innerHTML = userInput;

// ✅ 安全
element.textContent = userInput;
// 或使用 DOMPurify.sanitize(userInput)
```

##### 命令注入
```python
# ❌ 不安全
os.system(f"cat {filename}")

# ✅ 安全
subprocess.run(['cat', filename], check=True)
```

##### 硬编码密钥
```go
// ❌ 不安全
apiKey := "sk-1234567890abcdef"

// ✅ 安全
apiKey := os.Getenv("API_KEY")
```

#### 5.3 性能检查

**检查项目**:
- N+1 查询问题
- 不必要的循环嵌套
- 缺少缓存机会
- 缺少批处理
- 内存泄漏风险

**语言特定规则**:

##### Go
```go
// ❌ 在循环中使用 defer
for _, file := range files {
    defer file.Close()  // 错误：defer 会累积到函数返回
}

// ✅ 正确
for _, file := range files {
    file.Close()  // 立即关闭
}
```

```go
// ❌ 低效的字符串拼接
var result string
for _, s := range strings {
    result += s
}

// ✅ 高效
var builder strings.Builder
for _, s := range strings {
    builder.WriteString(s)
}
result := builder.String()
```

```go
// ❌ 未预分配容量
numbers := []int{}
for i := 0; i < 1000; i++ {
    numbers = append(numbers, i)
}

// ✅ 预分配容量
numbers := make([]int, 0, 1000)
for i := 0; i < 1000; i++ {
    numbers = append(numbers, i)
}
```

##### Python
```python
# ❌ 低效的字符串拼接
result = ""
for s in strings:
    result += s

# ✅ 高效
result = "".join(strings)
```

```python
# ❌ 不使用列表推导式
result = []
for x in range(100):
    result.append(x * 2)

# ✅ 使用列表推导式
result = [x * 2 for x in range(100)]
```

##### JavaScript
```javascript
// ❌ 在循环中操作 DOM
for (let item of items) {
    document.body.appendChild(createElement(item));
}

// ✅ 使用 DocumentFragment
const fragment = document.createDocumentFragment();
for (let item of items) {
    fragment.appendChild(createElement(item));
}
document.body.appendChild(fragment);
```

#### 5.4 测试覆盖检查

**检查项目**:
- 是否为新功能添加测试
- 测试覆盖率
- 测试质量（可读性、独立性、断言质量）
- 测试类型（单元测试、集成测试、E2E 测试）

**语言特定规则**:

##### Go
```go
// ✅ Table-driven tests
func TestCalculate(t *testing.T) {
    tests := []struct {
        name     string
        input    int
        expected int
    }{
        {"positive", 5, 10},
        {"zero", 0, 0},
        {"negative", -5, -10},
    }

    for _, tt := range tests {
        t.Run(tt.name, func(t *testing.T) {
            result := Calculate(tt.input)
            if result != tt.expected {
                t.Errorf("Calculate(%d) = %d; want %d", tt.input, result, tt.expected)
            }
        })
    }
}
```

##### Python
```python
# ✅ 使用 pytest fixtures
@pytest.fixture
def user():
    return User(name="Alice", age=30)

def test_user_age(user):
    assert user.age == 30
```

##### JavaScript
```javascript
// ✅ 使用 Jest describe/it
describe('User', () => {
    it('should calculate age correctly', () => {
        const user = new User('Alice', 30);
        expect(user.age).toBe(30);
    });

    it('should validate name', () => {
        expect(() => new User('', 30)).toThrow('Name is required');
    });
});
```

## 输出格式

### 详细报告模板

```
🔍 代码审查报告
═══════════════════════════════════════════════════════════════

📊 审查概览
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
审查模式: Git Diff (未提交的更改)
审查范围: 5 个文件，+328 行，-127 行
编程语言: Go (100%)
审查时间: 2026-03-20 23:15:30

📁 更改的文件:
  M src/main.go (+156, -45)
  M src/auth.go (+89, -23)
  M src/database.go (+72, -59)
  A src/utils.go (+11, -0)
  D src/legacy.go (+0, -127)

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🎯 检查结果总览
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
✅ 代码质量: 发现 3 个问题
⚠️  安全性: 发现 1 个严重问题
🐌 性能问题: 发现 2 个优化机会
📝 测试覆盖: 缺少测试

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📋 详细问题列表
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

🔴 严重问题 (2 个)
─────────────────────────────────────────────────────────────────

1. SQL 注入漏洞 (src/database.go:45)
   严重性: 🔴 严重
   类别: 安全性

   当前代码:
   ```go
   query := "SELECT * FROM users WHERE id = " + userID
   rows, err := db.Query(query)
   ```

   问题: 直接拼接用户输入到 SQL 查询，存在 SQL 注入风险

   修复建议:
   ```go
   query := "SELECT * FROM users WHERE id = ?"
   rows, err := db.Query(query, userID)
   ```

   参考: https://owasp.org/www-community/attacks/SQL_Injection


2. 硬编码敏感信息 (src/auth.go:12)
   严重性: 🔴 严重
   类别: 安全性

   当前代码:
   ```go
   const apiKey = "sk-1234567890abcdef"
   ```

   问题: API 密钥硬编码在代码中，存在泄露风险

   修复建议:
   ```go
   apiKey := os.Getenv("API_KEY")
   if apiKey == "" {
       return errors.New("API_KEY environment variable not set")
   }
   ```

   参考: https://cwe.mitre.org/data/definitions/798.html

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🟡 中等问题 (3 个)
─────────────────────────────────────────────────────────────────

3. 函数过长 (src/main.go:78-156)
   严重性: 🟡 中等
   类别: 代码质量

   问题: 函数 `handleRequest` 有 78 行，建议拆分为更小的函数

   修复建议:
   ```go
   func handleRequest(w http.ResponseWriter, r *http.Request) {
       // 提取为单独的函数
       if err := validateRequest(r); err != nil {
           // ...
       }
       data := extractData(r)
       response := processResponse(data)
       sendResponse(w, response)
   }
   ```

4. 循环中的 defer (src/database.go:89)
   严重性: 🟡 中等
   类别: 性能

   当前代码:
   ```go
   for _, file := range files {
       defer file.Close()
       // 处理文件
   }
   ```

   问题: defer 会累积到函数返回，可能导致资源未及时释放

   修复建议:
   ```go
   for _, file := range files {
       func(f *os.File) {
           defer f.Close()
           // 处理文件
       }(file)
   }
   ```


5. 缺少错误处理 (src/utils.go:23)
   严重性: 🟡 中等
   类别: 代码质量

   当前代码:
   ```go
   data, _ := ioutil.ReadFile(filename)
   ```

   问题: 忽略错误可能导致程序崩溃

   修复建议:
   ```go
   data, err := ioutil.ReadFile(filename)
   if err != nil {
       return fmt.Errorf("failed to read file: %w", err)
   }
   ```

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🟢 轻微问题 (1 个)
─────────────────────────────────────────────────────────────────

6. 命名不规范 (src/main.go:34)
   严重性: 🟢 轻微
   类别: 代码质量

   当前代码:
   ```go
   var x1, x2 string
   ```

   修复建议:
   ```go
   var userName, userEmail string
   ```

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
✨ 改进建议
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

🎯 优先修复项:
1. ⚠️ 修复 SQL 注入漏洞（src/database.go:45）
2. ⚠️ 移除硬编码密钥（src/auth.go:12）

📈 性能优化:
1. 优化循环中的 defer（src/database.go:89）
2. 考虑使用缓存减少重复查询

📝 代码质量:
1. 拆分长函数（src/main.go:78-156）
2. 改善命名规范
3. 添加更多注释

🧪 测试覆盖:
1. 当前测试覆盖率: 15%
2. 建议为核心功能添加单元测试
3. 添加集成测试覆盖关键路径

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📊 统计信息
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
审查文件数: 5
代码行数: +328, -127
发现问题: 6 个
  - 🔴 严重: 2 个
  - 🟡 中等: 3 个
  - 🟢 轻微: 1 个

估计修复时间:
  - 严重问题: 1-2 小时
  - 中等问题: 2-3 小时
  - 轻微问题: 30 分钟
  - 总计: 4-6 小时

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
💡 下一步行动
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
1. 立即修复严重的安全性问题
2. 改善代码质量和可维护性
3. 优化性能瓶颈
4. 提高测试覆盖率
5. 考虑使用静态分析工具（如 go vet, pylint, ESLint）

═══════════════════════════════════════════════════════════════
```

## 支持的编程语言

### 主要支持的语言 (20+)

| 语言 | 代码质量 | 安全性 | 性能 | 测试 | 检测工具 |
|------|---------|--------|------|------|---------|
| **Go** | ✅ | ✅ | ✅ | ✅ | gofmt, go vet, golint, staticcheck |
| **Java** | ✅ | ✅ | ✅ | ✅ | Checkstyle, PMD, SpotBugs, SonarQube |
| **Kotlin** | ✅ | ✅ | ✅ | ✅ | detekt, ktlint |
| **Python** | ✅ | ✅ | ✅ | ✅ | Pylint, Flake8, MyPy, Bandit |
| **JavaScript** | ✅ | ✅ | ✅ | ✅ | ESLint, JSHint, JSCS |
| **TypeScript** | ✅ | ✅ | ✅ | ✅ | TSLint, ESLint |
| **C** | ✅ | ✅ | ✅ | ✅ | Clang Static Analyzer, cppcheck |
| **C++** | ✅ | ✅ | ✅ | ✅ | Clang-Tidy, cppcheck, SonarQube |
| **C#** | ✅ | ✅ | ✅ | ✅ | StyleCop, FxCop, ReSharper |
| **PHP** | ✅ | ✅ | ✅ | ✅ | PHPStan, Psalm, PHP_CodeSniffer |
| **Ruby** | ✅ | ✅ | ✅ | ✅ | RuboCop, Reek |
| **Rust** | ✅ | ✅ | ✅ | ✅ | Clippy, Rustfmt |
| **Swift** | ✅ | ✅ | ✅ | ✅ | SwiftLint |
| **Dart** | ✅ | ✅ | ✅ | ✅ | dart analyze |
| **Scala** | ✅ | ✅ | ✅ | ✅ | Scalastyle, Scapegoat |
| **Shell** | ✅ | ✅ | ✅ | ✅ | ShellCheck |
| **Lua** | ✅ | ✅ | ✅ | ✅ | Luacheck |
| **Perl** | ✅ | ✅ | ✅ | ✅ | Perl::Critic, perlcritic |
| **Haskell** | ✅ | ✅ | ✅ | ✅ | HLint |

### Web 框架

- **React** (JSX, TypeScript)
- **Vue** (.vue files)
- **Angular** (TypeScript)
- **Svelte** (.svelte files)

### 模板引擎

- **HTML**, **Pug/Jade**, **EJS**, **Handlebars**
- **Blade** (Laravel), **Twig** (PHP), **Jinja2** (Python)

### 配置文件

- **JSON**, **YAML**, **XML**, **TOML**, **INI**
- **Dockerfile**, **docker-compose.yml**
- **Kubernetes** manifests
- **Terraform** (.tf)

## 语言特定规则

### Go

#### 代码质量
```go
// ✅ 使用有意义的命名
type UserService struct {
    db *sql.DB
}

// ✅ 错误处理
func GetUser(id string) (*User, error) {
    // ...
    if err != nil {
        return nil, fmt.Errorf("failed to get user: %w", err)
    }
    return user, nil
}

// ✅ 使用 defer 确保资源释放
func ProcessFile(filename string) error {
    f, err := os.Open(filename)
    if err != nil {
        return err
    }
    defer f.Close()
    // 处理文件
}
```

#### 安全性
```go
// ❌ SQL 注入风险
db.Query("SELECT * FROM users WHERE id = " + id)

// ✅ 使用参数化查询
db.Query("SELECT * FROM users WHERE id = ?", id)

// ❌ 硬编码密钥
const secret = "my-secret-key"

// ✅ 使用环境变量
secret := os.Getenv("SECRET_KEY")
```

#### 性能
```go
// ❌ 循环中 defer
for _, f := range files {
    defer f.Close()
}

// ✅ 使用匿名函数
for _, f := range files {
    func() {
        defer f.Close()
        // 处理文件
    }()
}

// ❌ 字符串拼接
var s string
for i := 0; i < 100; i++ {
    s += strconv.Itoa(i)
}

// ✅ 使用 strings.Builder
var b strings.Builder
for i := 0; i < 100; i++ {
    b.WriteString(strconv.Itoa(i))
}
s := b.String()

// ❌ 未预分配
nums := []int{}
for i := 0; i < 1000; i++ {
    nums = append(nums, i)
}

// ✅ 预分配容量
nums := make([]int, 0, 1000)
for i := 0; i < 1000; i++ {
    nums = append(nums, i)
}
```

### Python

#### 代码质量
```python
# ✅ 遵循 PEP 8
def calculate_total(amount: float, tax_rate: float) -> float:
    """Calculate total amount including tax."""
    return amount * (1 + tax_rate)

# ✅ 使用类型注解
from typing import List, Optional

def find_user(user_id: str) -> Optional[User]:
    # ...
    return user

# ✅ 使用上下文管理器
with open('file.txt', 'r') as f:
    content = f.read()
```

#### 安全性
```python
# ❌ SQL 注入风险
cursor.execute(f"SELECT * FROM users WHERE id = {user_id}")

# ✅ 使用参数化查询
cursor.execute("SELECT * FROM users WHERE id = ?", (user_id,))

# ❌ 命令注入风险
os.system(f"cat {filename}")

# ✅ 使用 subprocess
subprocess.run(['cat', filename], check=True)
```

#### 性能
```python
# ❌ 低效的字符串拼接
result = ""
for s in strings:
    result += s

# ✅ 使用 join
result = "".join(strings)

# ❌ 不使用列表推导式
result = []
for x in range(100):
    result.append(x * 2)

# ✅ 使用列表推导式
result = [x * 2 for x in range(100)]

# ❌ 频繁调用 len()
for i in range(len(data)):
    process(data[i])

# ✅ 直接迭代
for item in data:
    process(item)
```

### JavaScript/TypeScript

#### 代码质量
```javascript
// ✅ 使用 const/let
const API_URL = 'https://api.example.com';
let count = 0;

// ✅ 使用 async/await
async function fetchData() {
    try {
        const response = await fetch(API_URL);
        const data = await response.json();
        return data;
    } catch (error) {
        console.error('Failed to fetch:', error);
        throw error;
    }
}

// ✅ 使用模板字符串
const message = `Hello, ${name}!`;
```

#### 安全性
```javascript
// ❌ XSS 风险
element.innerHTML = userInput;

// ✅ 使用 textContent 或 DOMPurify
element.textContent = userInput;
// 或
element.innerHTML = DOMPurify.sanitize(userInput);

// ❌ eval 危险
const result = eval(userInput);

// ✅ 使用 JSON.parse
const result = JSON.parse(userInput);
```

#### 性能
```javascript
// ❌ 频繁操作 DOM
for (let item of items) {
    document.body.appendChild(createElement(item));
}

// ✅ 使用 DocumentFragment
const fragment = document.createDocumentFragment();
for (let item of items) {
    fragment.appendChild(createElement(item));
}
document.body.appendChild(fragment);

// ❌ 在循环中创建函数
for (let i = 0; i < 10; i++) {
    setTimeout(function() { console.log(i); }, 100);
}

// ✅ 使用 let 或箭头函数
for (let i = 0; i < 10; i++) {
    setTimeout(() => console.log(i), 100);
}
```

## 错误处理

### 1. 非 Git 仓库

```
❌ 当前目录不是 Git 仓库

💡 解决方案：
• 初始化 Git 仓库: git init
• 或切换到已有 Git 仓库的目录
```

### 2. 文件不存在

```
❌ 文件或目录不存在: /path/to/file

💡 提示：
• 检查文件路径是否正确
• 使用相对路径或绝对路径
```

### 3. 无效的 Commit Hash

```
❌ 无效的 commit hash: abc123

💡 解决方案：
• 检查 commit hash 是否正确
• 使用 git log 查看可用的 commits
• 使用 @HEAD, @HEAD~1 等简写
```

### 4. GitHub CLI 未安装

```
❌ 需要安装 GitHub CLI (gh) 才能审查 Pull Request

💡 安装方法：
macOS: brew install gh
Linux:  https://cli.github.com/
Windows: https://cli.github.com/

安装后需要登录: gh auth login
```

### 5. PR 不存在

```
❌ Pull Request 不存在: 123

💡 提示：
• 检查 PR 号码是否正确
• 使用 gh pr list 查看可用的 PRs
• 确保在正确的 Git 仓库中
```

### 6. 工作区干净

```
✅ 工作区干净，没有需要审查的更改

💡 提示：
• 查看最近的提交: git log
• 审查指定的 commit: /cr @HEAD
• 审查指定的文件: /cr <path>
```

## 最佳实践

### 1. 审查时机

- ✅ **提交前审查**: 使用 `/cr` 审查未提交的更改
- ✅ **Push 前审查**: 审查所有即将推送的代码
- ✅ **PR 前审查**: 使用 `/cr --pr` 审查即将合并的 PR
- ✅ **定期审查**: 审查最近的提交以保持代码质量

### 2. 审查频率

- **小型更改**: 每次提交前
- **中型更改**: 完成 feature 后
- **大型更改**: PR 创建时
- **团队项目**: 定期（每周）审查主分支

### 3. 问题处理优先级

1. **🔴 严重问题**: 立即修复
   - 安全漏洞（SQL 注入、XSS、命令注入）
   - 数据泄露风险
   - 关键 bug

2. **🟡 中等问题**: 尽快修复
   - 性能问题
   - 代码质量问题
   - 错误处理缺失

3. **🟢 轻微问题**: 逐步改进
   - 命名规范
   - 代码格式
   - 文档注释

### 4. 团队协作

- **建立审查标准**: 团队统一代码风格和质量要求
- **定期审查**: 在 CI/CD 流程中集成代码审查
- **知识共享**: 通过审查学习最佳实践
- **持续改进**: 根据审查结果调整开发流程

### 5. 配置自定义规则

可以通过以下方式自定义审查规则：

#### 项目配置文件

创建 `.review-config.json`:

```json
{
  "rules": {
    "max_line_length": 100,
    "max_function_length": 50,
    "max_complexity": 10,
    "require_tests": true,
    "min_test_coverage": 80
  },
  "ignore": [
    "vendor/*",
    "node_modules/*",
    "*.min.js",
    "*.pb.go"
  ],
  "language_specific": {
    "go": {
      "enable_golint": true,
      "enable_staticcheck": true
    },
    "python": {
      "enable_pylint": true,
      "enable_mypy": true
    }
  }
}
```

#### 环境变量

```bash
export REVIEW_MAX_LINE_LENGTH=100
export REVIEW_MIN_TEST_COVERAGE=80
export REVIEW_IGNORE_VENDOR=true
```

## 集成到工作流

### Pre-commit Hook

创建 `.git/hooks/pre-commit`:

```bash
#!/bin/bash
echo "Running code review..."
# 调用代码审查命令
# 如果发现问题，阻止提交
```

### CI/CD 集成

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
          # 安装依赖
          # 运行代码审查
```

## 进阶功能

### 1. 增量审查

仅审查变更的部分，而不是整个文件：

```bash
# 审查相对于某个基准的变更
/cr --base origin/main
```

### 2. 历史对比

对比两次 commit 之间的变更：

```bash
/cr --compare HEAD~5 HEAD
```

### 3. 自动修复

某些问题可以自动修复：

```bash
# 自动修复可修复的问题
/cr --fix
```

### 4. 报告导出

将审查报告导出为不同格式：

```bash
# 导出为 JSON
/cr --format json --output report.json

# 导出为 HTML
/cr --format html --output report.html

# 导出为 Markdown
/cr --format markdown --output report.md
```

## 性能优化

### 1. 并行分析

多个文件并行分析以提高速度：

```bash
# 使用并行分析
/cr --parallel
```

### 2. 缓存

缓存分析结果以避免重复分析：

```bash
# 启用缓存
/cr --cache
```

### 3. 增量分析

仅分析变更的部分：

```bash
# 增量分析
/cr --incremental
```

## 故障排除

### 问题 1: 审查速度慢

**解决方案**:
- 使用 `--parallel` 启用并行分析
- 使用 `--incremental` 仅分析变更
- 使用 `--cache` 启用缓存
- 在配置文件中排除不必要的目录

### 问题 2: 误报率高

**解决方案**:
- 调整规则严格程度
- 使用 `--config` 自定义配置
- 在 `.review-ignore` 中忽略特定文件
- 报告误报以改进工具

### 问题 3: 内存占用高

**解决方案**:
- 限制并行分析数量: `--parallel 2`
- 分析特定目录而非整个项目
- 使用增量分析

### 问题 4: 缺少语言支持

**解决方案**:
- 目前支持 20+ 语言
- 可以通过配置添加自定义规则
- 考虑使用其他工具补充

## 总结

`/cr` 命令提供了一个全面的代码审查解决方案，帮助开发者：

✅ **提高代码质量**: 发现代码异味和最佳实践违规
✅ **增强安全性**: 检测安全漏洞和敏感信息泄露
✅ **优化性能**: 识别性能瓶颈和优化机会
✅ **完善测试**: 提高测试覆盖率和测试质量
✅ **多语言支持**: 支持 20+ 编程语言
✅ **灵活使用**: 4 种审查模式适应不同场景
✅ **详细报告**: 提供清晰的问题描述和修复建议
✅ **易于集成**: 可以集成到现有工作流中

通过定期使用 `/cr` 进行代码审查，可以显著提高代码质量和团队生产力。
