---
description: 代码审查最佳实践知识库 - 涵盖 20+ 编程语言和框架的最佳实践、安全编码标准和性能优化技巧
---

# 代码审查最佳实践知识库

这个知识库包含了各种编程语言和框架的最佳实践，供代码审查专家参考。

## 目录

- [通用编程原则](#通用编程原则)
- [Go](#go)
- [Python](#python)
- [JavaScript/TypeScript](#javascripttypescript)
- [Java](#java)
- [C/C++](#cc)
- [C#](#c)
- [PHP](#php)
- [Ruby](#ruby)
- [Rust](#rust)
- [Swift](#swift)
- [Kotlin](#kotlin)
- [SQL](#sql)
- [HTML/CSS](#htmlcss)
- [安全编码通用原则](#安全编码通用原则)
- [性能优化通用原则](#性能优化通用原则)
- [测试最佳实践](#测试最佳实践)

---

## 快速定位

### 按问题类型查找

| 问题类型 | 参考章节 |
|---------|---------|
| SQL注入 | [安全编码通用原则](#安全编码通用原则)、[Go](#go)、[Python](#python)、[Java](#java) |
| XSS | [JavaScript/TypeScript](#javascripttypescript)、[安全编码通用原则](#安全编码通用原则) |
| 命令注入 | [Python](#python)、[安全编码通用原则](#安全编码通用原则) |
| 硬编码密钥 | [Go](#go)、[Python](#python)、[安全编码通用原则](#安全编码通用原则) |
| 字符串拼接性能 | [Go](#go)、[Python](#python)、[性能优化通用原则](#性能优化通用原则) |
| 错误处理 | [Go](#go)、[Python](#python)、[Java](#java) |
| 并发问题 | [Go](#go)、[Java](#java) |
| 测试质量 | [测试最佳实践](#测试最佳实践)、各语言章节 |

### 按语言快速查找

| 语言 | 主要章节 | 关键主题 |
|-----|---------|---------|
| Go | [Go](#go) | 错误处理、并发安全、性能优化、SQL注入防护 |
| Python | [Python](#python) | PEP 8、类型注解、上下文管理器、列表推导式 |
| JavaScript/TypeScript | [JS/TS](#javascripttypescript) | async/await、XSS防护、DOM优化、防抖节流 |
| Java | [Java](#java) | 异常处理、并发、try-with-resources |
| C/C++ | [C/C++](#cc) | 内存管理、缓冲区安全、RAII |

---

## 通用编程原则

### SOLID 原则

1. **Single Responsibility Principle (单一职责原则)**
   - 一个类/函数应该只有一个引起它变化的原因
   - 每个函数只做一件事，并做好

2. **Open/Closed Principle (开闭原则)**
   - 软件实体应该对扩展开放，对修改关闭
   - 使用接口和抽象来实现

3. **Liskov Substitution Principle (里氏替换原则)**
   - 子类应该能够替换父类而不破坏程序
   - 保持接口的一致性

4. **Interface Segregation Principle (接口隔离原则)**
   - 客户端不应该依赖它不需要的接口
   - 使用小而专注的接口

5. **Dependency Inversion Principle (依赖倒置原则)**
   - 依赖抽象而不是具体实现
   - 使用依赖注入

### DRY 原则 (Don't Repeat Yourself)

```go
// ❌ 重复代码
func getUser(id int) *User {
    // 50 lines of validation
}

func updateUser(id int, user *User) error {
    // 50 lines of the same validation
}

// ✅ 提取公共逻辑
func validateUser(user *User) error {
    // validation logic
}

func getUser(id int) *User {
    validateUser(user)
}

func updateUser(id int, user *User) error {
    validateUser(user)
}
```

### KISS 原则 (Keep It Simple, Stupid)

- 保持代码简单易懂
- 避免过度设计
- 优先选择简单解决方案

### YAGNI 原则 (You Aren't Gonna Need It)

- 只实现当前需要的功能
- 不要为未来可能的需求编程
- 避免不必要的复杂性

---

## Go

### 代码质量

#### 命名规范

```go
// ✅ 好的命名
type UserService struct {}
func (s *UserService) GetUserByID(id string) (*User, error) {}
const MaxRetries = 3
var userCache = make(map[string]*User)

// ❌ 不好的命名
type US struct {}
func (s *US) get(u string) (*User, error) {}
const max = 3
var uc = make(map[string]*User)
```

**规则**：
- 包名：小写单词，不使用下划线
- 常量：驼峰命名或全大写（仅用于导出）
- 变量：驼峰命名
- 接口：通常以 -er 结尾（Reader, Writer）

#### 错误处理

```go
// ✅ 正确的错误处理
func ReadFile(path string) ([]byte, error) {
    data, err := os.ReadFile(path)
    if err != nil {
        return nil, fmt.Errorf("read file: %w", err)
    }
    return data, nil
}

// ❌ 不要忽略错误
data, _ := os.ReadFile(path)

// ❌ 不要在错误中包含敏感信息
if err != nil {
    return fmt.Errorf("failed for user %s with password %s: %v", user, pass, err)
}
```

**最佳实践**：
- 总是检查错误
- 使用 `fmt.Errorf` 包装错误，保留上下文
- 错误消息应该小写，不包含句号
- 不要在错误中暴露敏感信息

#### 并发安全

```go
// ✅ 使用 sync.Map
var cache sync.Map
cache.Store("key", value)
if v, ok := cache.Load("key"); ok {
    // use v
}

// ✅ 使用互斥锁保护共享数据
type SafeCounter struct {
    mu    sync.Mutex
    count int
}

func (c *SafeCounter) Increment() {
    c.mu.Lock()
    defer c.mu.Unlock()
    c.count++
}

// ❌ 不要在循环中 defer
for _, file := range files {
    defer file.Close()  // 错误：会累积到函数返回
}

// ✅ 正确做法
for _, file := range files {
    func() {
        defer file.Close()
        // 处理文件
    }()
}
```

#### 性能优化

```go
// ❌ 低效的字符串拼接
var result string
for i := 0; i < 1000; i++ {
    result += strconv.Itoa(i)
}

// ✅ 使用 strings.Builder
var builder strings.Builder
builder.Grow(1000) // 预分配容量
for i := 0; i < 1000; i++ {
    builder.WriteString(strconv.Itoa(i))
}
result := builder.String()

// ❌ 未预分配 slice 容量
numbers := []int{}
for i := 0; i < 1000; i++ {
    numbers = append(numbers, i)
}

// ✅ 预分配容量
numbers := make([]int, 0, 1000)
for i := 0; i < 1000; i++ {
    numbers = append(numbers, i)
}

// ✅ 使用 range 时只读，避免索引变量
for _, item := range items {
    process(item)
}
```

### 安全性

#### SQL 注入防护

```go
// ❌ 不安全
query := "SELECT * FROM users WHERE id = " + userID
rows, err := db.Query(query)

// ✅ 使用参数化查询
query := "SELECT * FROM users WHERE id = ?"
rows, err := db.Query(query, userID)

// ✅ 使用命名参数（如果支持）
query := "SELECT * FROM users WHERE id = :id"
rows, err := db.Query(query, sql.Named("id", userID))
```

#### 敏感信息保护

```go
// ❌ 硬编码密钥
const APIKey = "sk-1234567890abcdef"

// ✅ 使用环境变量
apiKey := os.Getenv("API_KEY")
if apiKey == "" {
    return errors.New("API_KEY environment variable not set")
}

// ✅ 记录时过滤敏感字段
type User struct {
    ID       string `json:"id"`
    Username string `json:"username"`
    Password string `json:"-"` // 不序列化
}

func (u *User) String() string {
    return fmt.Sprintf("User{ID=%s, Username=%s}", u.ID, u.Username)
}
```

#### 输入验证

```go
// ✅ 验证用户输入
func ValidateUsername(username string) error {
    if len(username) < 3 || len(username) > 20 {
        return errors.New("username must be 3-20 characters")
    }
    if !regexp.MustCompile(`^[a-zA-Z0-9_]+$`).MatchString(username) {
        return errors.New("username can only contain letters, numbers, and underscores")
    }
    return nil
}
```

### 测试

#### Table-Driven Tests

```go
// ✅ 使用 table-driven tests
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

#### 测试覆盖

```go
// ✅ 测试所有分支
func TestDivide(t *testing.T) {
    // 正常情况
    result, err := Divide(10, 2)
    if err != nil || result != 5 {
        t.Errorf("Divide(10, 2) = %d, %v; want 5, nil", result, err)
    }

    // 错误情况
    _, err = Divide(10, 0)
    if err == nil {
        t.Error("Divide(10, 0) expected error, got nil")
    }
}
```

---

## Python

### 代码质量

#### PEP 8 规范

```python
# ✅ 遵循 PEP 8
def calculate_total(amount: float, tax_rate: float) -> float:
    """Calculate total amount including tax."""
    return amount * (1 + tax_rate)

class UserService:
    """Service for managing users."""

    def get_user(self, user_id: str) -> Optional[User]:
        """Get user by ID."""
        pass

# ❌ 不符合 PEP 8
def CalculateTotal(Amount, TaxRate):  # 应该小写+下划线
    pass

class userService:  # 应该大驼峰
    pass
```

#### 类型注解

```python
# ✅ 使用类型注解
from typing import List, Optional, Dict

def find_users(age_min: int) -> List[User]:
    """Find users older than age_min."""
    return [u for u in users if u.age > age_min]

def get_user(user_id: str) -> Optional[User]:
    """Get user by ID, returns None if not found."""
    return users.get(user_id)

# ✅ 使用 TypedDict
class UserDict(TypedDict):
    id: str
    name: str
    age: int

def process_user(user: UserDict) -> None:
    pass
```

#### 上下文管理器

```python
# ✅ 使用上下文管理器管理资源
def read_file(filename: str) -> str:
    """Read file content safely."""
    with open(filename, 'r') as f:
        return f.read()

# ✅ 自定义上下文管理器
from contextlib import contextmanager

@contextmanager
def timer(name: str):
    """Context manager for timing code blocks."""
    start = time.time()
    yield
    print(f"{name} took {time.time() - start:.2f}s")

with timer("processing"):
    process_data()
```

### 安全性

#### SQL 注入防护

```python
# ❌ 不安全
cursor.execute(f"SELECT * FROM users WHERE id = {user_id}")

# ✅ 使用参数化查询
cursor.execute("SELECT * FROM users WHERE id = ?", (user_id,))

# ✅ 使用命名参数
cursor.execute("SELECT * FROM users WHERE id = :id", {"id": user_id})
```

#### 命令注入防护

```python
# ❌ 不安全
os.system(f"cat {filename}")

# ✅ 使用 subprocess
subprocess.run(['cat', filename], check=True)

# ✅ 如果需要 shell=True，验证输入
import shlex
if not re.match(r'^[a-zA-Z0-9_.-]+$', filename):
    raise ValueError("Invalid filename")
subprocess.run(f"cat {shlex.quote(filename)}", shell=True, check=True)
```

#### 密码处理

```python
# ❌ 不安全
password = "secret123"
hashed_password = hashlib.md5(password.encode()).hexdigest()

# ✅ 使用 bcrypt 或 argon2
import bcrypt

password = b"secret123"
salt = bcrypt.gensalt()
hashed_password = bcrypt.hashpw(password, salt)

# 验证
if bcrypt.checkpw(password, hashed_password):
    print("Password correct")
```

### 性能优化

#### 列表推导式

```python
# ❌ 低效
result = []
for x in range(100):
    result.append(x * 2)

# ✅ 使用列表推导式
result = [x * 2 for x in range(100)]

# ✅ 对于大数据使用生成器
result = (x * 2 for x in range(1000000))
```

#### 字符串拼接

```python
# ❌ 低效
result = ""
for s in strings:
    result += s

# ✅ 使用 join
result = "".join(strings)
```

#### 使用内置函数

```python
# ❌ 手动实现
total = 0
for num in numbers:
    total += num

# ✅ 使用内置函数
total = sum(numbers)

# ✅ 使用 map
squared = list(map(lambda x: x**2, numbers))

# ✅ 使用生成器表达式
squared_sum = sum(x**2 for x in numbers)
```

### 测试

#### pytest 最佳实践

```python
# ✅ 使用 fixtures
import pytest

@pytest.fixture
def user():
    """Create a test user."""
    return User(id="1", name="Alice", age=30)

def test_user_age(user):
    """Test user age."""
    assert user.age == 30

# ✅ 参数化测试
@pytest.mark.parametrize("input,expected", [
    (5, 10),
    (0, 0),
    (-5, -10),
])
def test_calculate(input, expected):
    """Test calculate function."""
    assert calculate(input) == expected

# ✅ 使用 mock
from unittest.mock import Mock, patch

def test_external_api():
    """Test external API call."""
    with patch('requests.get') as mock_get:
        mock_get.return_value.json.return_value = {"status": "ok"}
        result = call_external_api()
        assert result["status"] == "ok"
```

---

## JavaScript/TypeScript

### 代码质量

#### 现代 JavaScript

```javascript
// ✅ 使用 const/let
const API_URL = 'https://api.example.com';
let count = 0;

// ❌ 避免使用 var
var data = [];

// ✅ 使用模板字符串
const message = `Hello, ${name}! You have ${count} messages.`;

// ✅ 使用解构
const { name, age } = user;
const [first, second] = array;

// ✅ 使用箭头函数
const add = (a, b) => a + b;
const users = data.map(item => ({ id: item.id, name: item.name }));
```

#### 异步处理

```javascript
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

// ✅ 处理多个异步操作
const [user, posts] = await Promise.all([
    fetchUser(userId),
    fetchUserPosts(userId)
]);

// ❌ 避免 callback hell
function fetchData(callback) {
    fetch(url, (res) => {
        res.json((data) => {
            callback(data);
        });
    });
}
```

#### TypeScript 类型

```typescript
// ✅ 明确定义类型
interface User {
    id: string;
    name: string;
    email: string;
    age?: number; // 可选
}

function getUser(id: string): Promise<User> {
    return fetch(`/api/users/${id}`)
        .then(res => res.json());
}

// ✅ 使用联合类型
type Status = 'pending' | 'approved' | 'rejected';

function updateStatus(id: string, status: Status): void {
    // ...
}

// ✅ 使用泛型
function identity<T>(arg: T): T {
    return arg;
}
```

### 安全性

#### XSS 防护

```javascript
// ❌ 不安全
element.innerHTML = userInput;

// ✅ 使用 textContent
element.textContent = userInput;

// ✅ 使用 DOMPurify 清理 HTML
import DOMPurify from 'dompurify';
element.innerHTML = DOMPurify.sanitize(userInput);

// ✅ 使用模板引擎的自动转义
// React, Vue 等框架默认转义
<div>{userInput}</div>
```

#### CSRF 防护

```javascript
// ✅ 使用 CSRF token
fetch(url, {
    method: 'POST',
    headers: {
        'Content-Type': 'application/json',
        'X-CSRF-Token': getCsrfToken()
    },
    body: JSON.stringify(data)
});

// ✅ 使用 SameSite cookie
document.cookie = 'session=abc; SameSite=Strict';
```

### 性能优化

#### DOM 操作优化

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

// ✅ 批量更新
requestAnimationFrame(() => {
    // DOM 更新
});
```

#### 事件委托

```javascript
// ❌ 为每个元素添加事件监听
items.forEach(item => {
    item.addEventListener('click', handleClick);
});

// ✅ 使用事件委托
container.addEventListener('click', (e) => {
    if (e.target.matches('.item')) {
        handleClick(e);
    }
});
```

#### 防抖和节流

```javascript
// ✅ 防抖 - 延迟执行
function debounce(func, wait) {
    let timeout;
    return function executedFunction(...args) {
        const later = () => {
            clearTimeout(timeout);
            func(...args);
        };
        clearTimeout(timeout);
        timeout = setTimeout(later, wait);
    };
}

// ✅ 节流 - 限制执行频率
function throttle(func, limit) {
    let inThrottle;
    return function(...args) {
        if (!inThrottle) {
            func.apply(this, args);
            inThrottle = true;
            setTimeout(() => inThrottle = false, limit);
        }
    };
}
```

### 测试

#### Jest 最佳实践

```javascript
// ✅ 使用 describe/it 组织测试
describe('User', () => {
    it('should calculate age correctly', () => {
        const user = new User('Alice', 30);
        expect(user.age).toBe(30);
    });

    it('should validate name', () => {
        expect(() => new User('', 30)).toThrow('Name is required');
    });
});

// ✅ 使用 mock
jest.mock('./api');
import { fetchUser } from './api';

test('fetches user data', async () => {
    fetchUser.mockResolvedValue({ id: '1', name: 'Alice' });

    const data = await fetchUser('1');
    expect(data.name).toBe('Alice');
});

// ✅ 测试异步代码
test('async test', async () => {
    const result = await asyncFunction();
    expect(result).toBe('expected value');
});
```

---

## Java

### 代码质量

#### 命名规范

```java
// ✅ 好的命名
public class UserService {
    private static final int MAX_RETRIES = 3;
    private UserRepository userRepository;

    public User getUserById(String id) {
        return userRepository.findById(id);
    }
}

// ❌ 不好的命名
public class us {
    private static final int max = 3;
    private UserRepository ur;

    public User g(String i) {
        return ur.findById(i);
    }
}
```

#### 异常处理

```java
// ✅ 正确的异常处理
public void processData(String input) throws ProcessingException {
    try {
        // 处理逻辑
    } catch (IOException e) {
        throw new ProcessingException("Failed to process data", e);
    }
}

// ❌ 不要捕获太宽泛的异常
try {
    // code
} catch (Exception e) {
    // 太宽泛
}

// ❌ 不要吞掉异常
try {
    // code
} catch (Exception e) {
    // 什么都不做
}

// ✅ 使用 try-with-resources
try (FileInputStream fis = new FileInputStream("file.txt")) {
    // 使用 fis
} // 自动关闭
```

#### 并发

```java
// ✅ 使用线程安全集合
Map<String, String> map = new ConcurrentHashMap<>();
List<String> list = new CopyOnWriteArrayList<>();

// ✅ 使用锁
private final Lock lock = new ReentrantLock();

public void safeMethod() {
    lock.lock();
    try {
        // 临界区
    } finally {
        lock.unlock();
    }
}

// ❌ 不要在同步块中调用可能阻塞的方法
synchronized (this) {
    // 不要在这里调用外部方法或 I/O
}
```

### 安全性

#### SQL 注入防护

```java
// ❌ 不安全
String query = "SELECT * FROM users WHERE id = " + userId;
Statement stmt = connection.createStatement();
ResultSet rs = stmt.executeQuery(query);

// ✅ 使用 PreparedStatement
String query = "SELECT * FROM users WHERE id = ?";
PreparedStatement stmt = connection.prepareStatement(query);
stmt.setString(1, userId);
ResultSet rs = stmt.executeQuery();
```

#### 密码处理

```java
// ✅ 使用 BCrypt
import org.mindrot.jbcrypt.BCrypt;

String hashedPassword = BCrypt.hashpw(plainPassword, BCrypt.gensalt());

// 验证
if (BCrypt.checkpw(plainPassword, hashedPassword)) {
    // 密码正确
}
```

### 性能优化

#### 字符串处理

```java
// ❌ 低效的字符串拼接
String result = "";
for (int i = 0; i < 1000; i++) {
    result += i;
}

// ✅ 使用 StringBuilder
StringBuilder sb = new StringBuilder(1000); // 预分配容量
for (int i = 0; i < 1000; i++) {
    sb.append(i);
}
String result = sb.toString();
```

#### 对象创建

```java
// ✅ 复用对象
String s1 = "hello";
String s2 = "hello"; // 复用同一个对象

// ❌ 避免不必要的对象创建
String s3 = new String("hello"); // 创建新对象
```

---

## C/C++

### 代码质量

#### 内存管理

```c
// ✅ 正确的内存管理
char* buffer = malloc(size);
if (buffer == NULL) {
    return NULL;
}

// 使用 buffer
strcpy(buffer, data);

// 释放内存
free(buffer);
buffer = NULL; // 避免悬空指针

// ❌ 内存泄漏
char* buffer = malloc(size);
// 忘记 free(buffer)

// ✅ 使用 RAII (C++)
std::unique_ptr<char[]> buffer(new char[size]);
// 自动释放
```

#### 缓冲区安全

```c
// ❌ 不安全
char buffer[10];
strcpy(buffer, input); // 可能溢出

// ✅ 安全版本
char buffer[10];
strncpy(buffer, input, sizeof(buffer) - 1);
buffer[sizeof(buffer) - 1] = '\0';

// ✅ 使用 snprintf
snprintf(buffer, sizeof(buffer), "%s", input);
```

### 安全性

#### 整数溢出

```c
// ✅ 检查整数溢出
if (a > INT_MAX - b) {
    // 处理溢出
}
int result = a + b;

// ✅ 使用安全函数
#include <stdint.h>
if (!__builtin_add_overflow(a, b, &result)) {
    // 没有溢出
}
```

### 性能优化

#### 循环优化

```c
// ❌ 低效
for (int i = 0; i < strlen(str); i++) {
    // strlen 在每次迭代都调用
}

// ✅ 高效
int len = strlen(str);
for (int i = 0; i < len; i++) {
    // 只调用一次 strlen
}
```

---

## 安全编码通用原则

### 输入验证

- 验证所有外部输入（用户输入、文件、网络）
- 白名单验证优于黑名单
- 验证类型、长度、格式、范围

### 输出编码

- 对输出到不同上下文的数据进行编码
- HTML、JavaScript、URL、SQL 等
- 使用框架的自动转义功能

### 认证和授权

- 使用强哈希算法（bcrypt、argon2）
- 实现适当的密码策略
- 使用 HTTPS 传输敏感数据
- 实施最小权限原则

### 加密

- 不要自己实现加密算法
- 使用经过验证的加密库
- 正确管理密钥
- 使用标准的加密算法（AES、RSA）

---

## 性能优化通用原则

### 算法复杂度

- 优先选择 O(1) > O(log n) > O(n) > O(n log n) > O(n²)
- 避免嵌套循环
- 使用适当的数据结构

### 缓存

- 缓存重复计算的结果
- 使用内存缓存（Redis、Memcached）
- 实现缓存失效策略

### 数据库优化

- 使用索引
- 避免 N+1 查询
- 使用连接而不是多次查询
- 分页获取大量数据

### 并发和异步

- 使用异步 I/O
- 并行处理独立任务
- 使用线程池
- 避免锁竞争

---

## 测试最佳实践

### 单元测试

- 测试单个功能点
- 使用 mock 隔离依赖
- 测试边界条件
- 测试错误情况

### 集成测试

- 测试组件间交互
- 使用测试数据库
- 测试完整流程

### 测试覆盖率

- 目标：80%+ 覆盖率
- 覆盖关键路径
- 不仅追求覆盖率，更要关注测试质量

### 测试命名

```go
// ✅ 清晰的测试命名
func TestUserService_GetUserByID_ReturnsUser_WhenUserExists(t *testing.T) {}
func TestUserService_GetUserByID_ReturnsError_WhenUserNotFound(t *testing.T) {}

// ✅ 使用 table-driven tests
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
    // ...
}
```

---

这个知识库持续更新中。如需特定语言或框架的更多最佳实践，请随时添加！