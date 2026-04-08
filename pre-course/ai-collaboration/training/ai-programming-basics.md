# AI编程基础培训 - 从工作区搭建到功能开发实战

> **培训时长**：60分钟  
> **目标受众**：有编程基础，首次使用AI编程工具的开发者  
> **培训目标**：掌握工作区搭建规范，理解AI辅助开发的完整流程

---

## 目录

- [第一部分：工作区搭建篇（15-20分钟）](#第一部分工作区搭建篇)
- [第二部分：AI编程实战篇（15-20分钟）](#第二部分ai编程实战篇)
- [附录：常用资源与FAQ](#附录常用资源与faq)

---

# 第一部分：工作区搭建篇

## 1. 工作区目录结构设计

### 1.1 推荐的目录结构

一个规范的工作区目录结构是高效协作的基础：

```
workspace/
├── projects/                    # 项目目录
│   ├── ProjectA/               # 具体项目A
│   │   ├── frontend/           # 前端代码
│   │   ├── backend/            # 后端代码
│   │   ├── docs/               # 项目文档
│   │   │   ├── prd/           # 产品需求文档
│   │   │   ├── api/           # API文档
│   │   │   └── design/        # 设计稿
│   │   ├── db/                 # 数据库脚本
│   │   └── tests/              # 测试代码
│   └── ProjectB/               # 具体项目B
│
├── libs/                        # 公共库
│   ├── CommonUtils/            # 通用工具库
│   └── UiComponents/           # UI组件库
│
├── tools/                       # 开发工具
│   └── scripts/                # 脚本工具
│
└── .trae/                       # AI工具配置（Trae IDE）
    ├── documents/              # AI生成的文档
    ├── specs/                  # 规格说明
    └── rules/                  # 项目规则
```

### 1.2 目录命名规范

| 类型 | 规范 | 示例 |
|------|------|------|
| 项目目录 | 驼峰格式 | `SpDataView` |
| 前端目录 | `frontend` 或 `web` | `frontend/` |
| 后端目录 | `backend` 或 `api` | `backend/` |
| 文档目录 | `docs` | `docs/` |
| 配置文件 | `.` 开头 | `.env`, `.gitignore` |

---

## 2. AI工具分工与角色

### 2.1 AI编程工具的角色定位

> **AI编程工具不是替代开发者，而是作为"智能助手"协助开发，完成重复性的工作，核心代码，比如高性能、高并发等架构核心代码建议人工编写，AI助手只能够给出模型内最佳实践代码，并不一定适合当前功能代码所需。理解AI的角色定位至关重要：**

```
┌─────────────────────────────────────────────────────────┐
│                    开发者（主导）                          │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐      │
│  │ 需求分析    │  │ 架构设计    │  │ 代码审查    │      │
│  │ 决策判断    │  │ 质量把控    │  │ 最终确认    │      │
│  └─────────────┘  └─────────────┘  └─────────────┘      │
└─────────────────────────────────────────────────────────┘
                          ▲
                          │ 协作
                          ▼
┌─────────────────────────────────────────────────────────┐
│                    AI工具（辅助）                          │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐      │
│  │ 代码生成    │  │ 文档编写    │  │ 问题诊断    │      │
│  │ 重构建议    │  │ 测试用例    │  │ 知识检索    │      │
│  └─────────────┘  └─────────────┘  └─────────────┘      │
└─────────────────────────────────────────────────────────┘
```

### 2.2 AI工具能力边界

| AI擅长 | AI不擅长 |
|--------|----------|
| 生成样板代码 | 理解复杂业务逻辑 |
| 编写文档和注释 | 做架构决策 |
| 重构和优化代码 | 处理模糊需求 |
| 生成测试用例 | 理解隐含约束 |
| 解释代码功能 | 保证代码100%正确 |

### 2.3 常用AI编程工具对比

| 工具 | 角色定位 | 主要职责 |
|------|----------|----------|
| **Claude Code** | 架构师 | 生成框架代码、Git提交审查和提交 |
| **Trae** | 开发工程师 | 详细功能代码生成、UI原型设计、前端交互生成 |
| **Code Buddy** | 测试工程师 | 代码审查、扫漏、单元测试、自动测试 |

**工具协作流程**：

```
┌─────────────────────────────────────────────────────────┐
│                    AI工具协作流程                         │
└─────────────────────────────────────────────────────────┘

  ┌──────────────┐     ┌──────────────┐     ┌──────────────┐
  │ Claude Code  │────▶│    Trae      │────▶│  Code Buddy  │
  │   架构师     │     │  开发工程师   │     │  测试工程师  │
  └──────────────┘     └──────────────┘     └──────────────┘
        │                    │                    │
        ▼                    ▼                    ▼
  ┌──────────────┐     ┌──────────────┐     ┌──────────────┐
  │ 生成框架代码  │     │ 功能代码生成  │     │   代码审查   │
  │ 项目结构设计  │     │ UI原型设计   │     │   扫漏检查   │
  │ 技术选型建议  │     │ 前端交互生成  │     │   单元测试   │
  └──────────────┘     └──────────────┘     └──────────────┘
        │                                         │
        │                                         ▼
        │                                  ┌──────────────┐
        └─────────────────────────────────▶│ Claude Code  │
                                          │ Git提交审查  │
                                          │   代码提交   │
                                          └──────────────┘
```

---

## 3. Git提交规范

### 3.1 Commit Message 规范

采用 [Conventional Commits](https://www.conventionalcommits.org/) 规范：

```
<type>(<scope>): <subject>

<body>

<footer>
```

**Type类型说明**：

| 类型 | 说明 | 示例 |
|------|------|------|
| `feat` | 新功能 | `feat(dashboard): 添加逾期看板模块` |
| `fix` | Bug修复 | `fix(api): 修复日期查询条件错误` |
| `docs` | 文档更新 | `docs: 更新API文档` |
| `style` | 代码格式 | `style: 格式化代码` |
| `refactor` | 重构 | `refactor(utils): 重构日期工具函数` |
| `test` | 测试 | `test: 添加单元测试` |
| `chore` | 构建/工具 | `chore: 更新依赖版本` |

### 3.2 分支管理规范

```
main (生产分支)
  └── develop (开发分支)
        ├── feature/xxx (功能分支)
        ├── bugfix/xxx (修复分支)
        └── release/x.x.x (发布分支)
```

**分支命名规范**：
- 功能分支：`feature/模块名-功能描述`
- 修复分支：`bugfix/问题描述`
- 发布分支：`release/版本号`

### 3.3 Git代码提交流程

> **Claude Code 作为Git提交审查和提交角色，负责整个提交流程的执行。**

**完整提交流程**：

```
┌─────────────────────────────────────────────────────────┐
│                 Git代码提交流程                          │
└─────────────────────────────────────────────────────────┘

  ┌──────────────────┐
  │ 1. Claude Code   │
  │ 生成提交代码和步骤 │
  └────────┬─────────┘
           │
           ▼
  ┌──────────────────┐
  │ 2. 钩子自动触发   │
  │ - 代码检查        │
  │ - 生成提交信息    │
  └────────┬─────────┘
           │
           ▼
  ┌──────────────────┐
  │ 3. Claude Code   │
  │ 执行 git add     │
  │ 执行 git commit  │
  └────────┬─────────┘
           │
           ▼
  ┌──────────────────┐
  │ 4. 钩子自动审查   │
  │ - 扫漏检查        │
  │ - 敏感信息审查    │
  └────────┬─────────┘
           │
           ▼
     ┌─────┴─────┐
     │  审查结果  │
     └─────┬─────┘
           │
     ┌─────┴─────┐
     │           │
     ▼           ▼
┌─────────┐  ┌─────────┐
│ 不通过  │  │  通过   │
└────┬────┘  └────┬────┘
     │            │
     ▼            ▼
┌─────────┐  ┌─────────┐
│拒绝提交 │  │自动提交 │
│修复建议 │  │git push │
└─────────┘  └─────────┘
```

**Claude Code 提交命令示例**：

```bash
# 1. 查看当前修改状态
git status

# 2. 生成提交信息（AI自动分析变更内容）
# Claude Code 会根据变更内容生成符合规范的提交信息

# 3. 执行提交
git add .
git commit -m "feat(dashboard): 添加逾期看板模块

- 实现总览表格组件
- 添加筛选功能
- 完成API对接"

# 4. 钩子自动执行检查
# - pre-commit: 代码检查、类型检查、单元测试
# - commit-msg: 提交信息格式校验

# 5. 如果通过，自动推送到远程仓库
git push origin feature/dashboard-domestic
```

**钩子检查内容**：

| 钩子 | 检查项 | 不通过处理 |
|------|--------|-----------|
| pre-commit | ESLint代码检查 | 返回错误信息，提示修复 |
| pre-commit | TypeScript类型检查 | 返回类型错误，提示修复 |
| pre-commit | 单元测试执行 | 返回失败用例，提示修复 |
| pre-commit | 敏感信息扫描 | 拒绝提交，列出敏感文件 |
| commit-msg | 提交信息格式 | 返回格式错误，提示正确格式 |

**提交失败处理流程**：

```
提交失败
    │
    ▼
┌─────────────────────────────────────────┐
│ Claude Code 分析错误信息                 │
│                                         │
│ 错误类型：                               │
│ - 代码风格问题 → 自动修复或提示修复位置   │
│ - 类型错误 → 提示具体类型不匹配          │
│ - 测试失败 → 列出失败用例和原因          │
│ - 敏感信息 → 列出敏感文件路径            │
└─────────────────────────────────────────┘
    │
    ▼
修复后重新提交
```

---

## 4. Git钩子配置

### 4.1 使用 Husky 配置Git钩子

```bash
# 安装 husky
npm install husky --save-dev

# 初始化 husky
npx husky install

# 添加 pre-commit 钩子
npx husky add .husky/pre-commit "npm run lint"

# 添加 commit-msg 钩子
npx husky add .husky/commit-msg "npx commitlint --edit $1"
```

### 4.2 常用钩子配置

**.husky/pre-commit**（提交前检查）：
```bash
#!/usr/bin/env sh
. "$(dirname -- "$0")/_/husky.sh"

# 运行代码检查
npm run lint

# 运行类型检查
npm run typecheck

# 运行测试
npm run test
```

**.husky/commit-msg**（提交信息校验）：
```bash
#!/usr/bin/env sh
. "$(dirname -- "$0")/_/husky.sh"

npx --no -- commitlint --edit "$1"
```

### 4.3 commitlint 配置

**commitlint.config.js**：
```javascript
module.exports = {
  extends: ['@commitlint/config-conventional'],
  rules: {
    'type-enum': [
      2,
      'always',
      ['feat', 'fix', 'docs', 'style', 'refactor', 'test', 'chore', 'revert']
    ],
    'subject-case': [0]
  }
};
```

---

## 5. 多语言代码规范

### 5.1 前端规范（Vue/TypeScript）

**目录结构**：
```
frontend/
├── src/
│   ├── api/           # API接口
│   ├── components/    # 公共组件
│   ├── views/         # 页面视图
│   ├── store/         # 状态管理
│   ├── router/        # 路由配置
│   ├── utils/         # 工具函数
│   └── types/         # 类型定义
```

**命名规范**：
- 组件文件：PascalCase（如 `UserList.vue`）
- 工具函数：camelCase（如 `formatDate.ts`）
- 常量：UPPER_SNAKE_CASE（如 `API_BASE_URL`）

**ESLint配置要点**：
```json
{
  "rules": {
    "vue/multi-word-component-names": "error",
    "@typescript-eslint/no-explicit-any": "warn",
    "no-console": "warn"
  }
}
```

### 5.2 后端规范（PHP/Laravel）

**目录结构**：
```
backend/
├── app/
│   ├── Http/Controllers/   # 控制器
│   ├── Models/             # 模型
│   └── Services/           # 服务层
├── routes/                 # 路由
└── config/                 # 配置
```

**命名规范**：
- 控制器：PascalCase + Controller（如 `OrderController`）
- 模型：PascalCase 单数（如 `Order`）
- 方法：camelCase（如 `getOrderList`）

**PHP最佳实践规范**：

> 以下为行业推荐规范，可根据项目实际情况调整。

| 规范项 | 推荐标准 | 说明 |
|--------|----------|------|
| 代码风格 | PSR-12 | PHP官方代码风格规范 |
| 自动加载 | PSR-4 | 命名空间与目录结构对应 |
| 类型声明 | 严格类型 | 使用 `declare(strict_types=1)` |
| 返回类型 | 明确声明 | 所有方法必须有返回类型声明 |
| 参数类型 | 明确声明 | 所有参数必须有类型声明 |

**PHP代码风格配置**：

```php
// phpcs.xml - PHP CodeSniffer 配置
<?xml version="1.0"?>
<ruleset name="PHP Standards">
    <description>PHP代码规范检查</description>
    
    <!-- 使用PSR-12标准 -->
    <rule ref="PSR12"/>
    
    <!-- 检查目录 -->
    <file>app</file>
    <file>routes</file>
    
    <!-- 排除目录 -->
    <exclude-pattern>vendor/*</exclude-pattern>
    <exclude-pattern>storage/*</exclude-pattern>
    
    <!-- 显示进度 -->
    <arg value="p"/>
    
    <!-- 使用颜色输出 -->
    <arg name="colors"/>
</ruleset>
```

**PHPStan静态分析配置**：

```yaml
# phpstan.neon - 静态分析配置
parameters:
    level: 8
    paths:
        - app
        - routes
    excludePaths:
        - vendor
    reportUnmatchedIgnoredErrors: false
```

**Laravel最佳实践**：

```php
// ✅ 推荐：使用依赖注入
public function store(Request $request, OrderService $orderService)
{
    return $orderService->create($request->validated());
}

// ❌ 不推荐：直接使用Facade
public function store(Request $request)
{
    return Order::create($request->all());
}

// ✅ 推荐：使用表单请求验证
class StoreOrderRequest extends FormRequest
{
    public function rules(): array
    {
        return [
            'customer_id' => ['required', 'exists:customers,id'],
            'amount' => ['required', 'numeric', 'min:0'],
        ];
    }
}

// ✅ 推荐：使用资源类返回数据
class OrderResource extends JsonResource
{
    public function toArray($request): array
    {
        return [
            'id' => $this->id,
            'customer' => new CustomerResource($this->whenLoaded('customer')),
            'amount' => $this->amount,
            'created_at' => $this->created_at->toIso8601String(),
        ];
    }
}

// ✅ 推荐：服务层处理业务逻辑
class OrderService
{
    public function create(array $data): Order
    {
        return DB::transaction(function () use ($data) {
            $order = Order::create($data);
            // 其他业务逻辑...
            return $order;
        });
    }
}
```

**PHP代码质量工具**：

```bash
# 安装开发依赖
composer require --dev \
    squizlabs/php_codesniffer \
    phpstan/phpstan \
    laravel/pint

# 运行代码风格检查
./vendor/bin/phpcs

# 运行静态分析
./vendor/bin/phpstan analyse

# 运行代码格式化
./vendor/bin/pint
```

### 5.3 后端规范（Java/Spring Cloud）

**目录结构**：
```
src/main/java/com/company/project/
├── controller/     # 控制器
├── service/        # 服务层
├── repository/     # 数据访问层
├── entity/         # 实体类
├── dto/            # 数据传输对象
└── config/         # 配置类
```

**命名规范**：
- 类名：PascalCase
- 方法名：camelCase
- 常量：UPPER_SNAKE_CASE

**阿里巴巴Java开发规范**：

> 参考《阿里巴巴Java开发手册》，以下为核心规范要点，可根据项目实际情况调整。

**命名规范**：

| 类型 | 规范 | 示例 |
|------|------|------|
| 类名 | UpperCamelCase | `UserOrderService` |
| 方法名 | lowerCamelCase | `getUserById()` |
| 常量 | UPPER_SNAKE_CASE | `MAX_RETRY_COUNT` |
| 包名 | 全小写 | `com.company.project.service` |
| 抽象类 | Abstract或Base开头 | `AbstractOrderProcessor` |
| 异常类 | Exception结尾 | `OrderNotFoundException` |
| 测试类 | Test结尾 | `OrderServiceTest` |
| 接口实现类 | Impl结尾 | `OrderServiceImpl` |

**代码规范要点**：

```java
// ✅ 推荐：常量定义
public class Constants {
    public static final int MAX_RETRY_COUNT = 3;
    public static final String DEFAULT_CHARSET = "UTF-8";
}

// ❌ 不推荐：魔法值
if (status == 1) { ... }  // 不清楚1代表什么

// ✅ 推荐：使用枚举
public enum OrderStatus {
    PENDING(0, "待处理"),
    PROCESSING(1, "处理中"),
    COMPLETED(2, "已完成");
    
    private final int code;
    private final String desc;
    
    OrderStatus(int code, String desc) {
        this.code = code;
        this.desc = desc;
    }
}

// ✅ 推荐：集合判空
if (CollectionUtils.isEmpty(orderList)) {
    return Collections.emptyList();
}

// ❌ 不推荐：直接判空
if (orderList == null || orderList.size() == 0) {
    return new ArrayList<>();
}

// ✅ 推荐：使用StringBuilder拼接字符串
StringBuilder sb = new StringBuilder();
sb.append("order_").append(orderId);

// ❌ 不推荐：字符串直接拼接
String orderNo = "order_" + orderId;  // 循环中性能差

// ✅ 推荐：避免返回null
public List<Order> getOrders() {
    if (condition) {
        return Collections.emptyList();
    }
    return orderList;
}

// ❌ 不推荐：返回null
public List<Order> getOrders() {
    if (condition) {
        return null;  // 调用方需要判空
    }
    return orderList;
}
```

**OOP规约**：

```java
// ✅ 推荐：避免通过对象引用访问静态变量
User user = new User();
User.STATIC_FIELD;  // 正确

// ❌ 不推荐：通过对象引用访问
user.STATIC_FIELD;  // 不推荐

// ✅ 推荐：覆写方法必须加@Override注解
@Override
public String toString() {
    return "User{id=" + id + "}";
}

// ✅ 推荐：可变参数放在参数列表最后
public void process(String name, Integer... ids) { }

// ❌ 不推荐：可变参数不在最后
public void process(Integer... ids, String name) { }  // 编译错误
```

**异常处理规范**：

```java
// ✅ 推荐：捕获具体异常
try {
    orderService.process(order);
} catch (OrderNotFoundException e) {
    log.error("订单不存在: {}", order.getId(), e);
    throw new BusinessException("订单不存在");
} catch (Exception e) {
    log.error("处理订单异常", e);
    throw new SystemException("系统异常");
}

// ❌ 不推荐：直接捕获Exception
try {
    orderService.process(order);
} catch (Exception e) {
    e.printStackTrace();  // 不推荐
}

// ✅ 推荐：finally块中释放资源
InputStream is = null;
try {
    is = new FileInputStream(file);
    // 处理逻辑
} finally {
    if (is != null) {
        is.close();
    }
}

// ✅ 推荐：使用try-with-resources
try (InputStream is = new FileInputStream(file)) {
    // 处理逻辑
}  // 自动关闭资源
```

**日志规约**：

```java
// ✅ 推荐：使用占位符
log.info("订单创建成功, orderId: {}, userId: {}", orderId, userId);

// ❌ 不推荐：字符串拼接
log.info("订单创建成功, orderId: " + orderId + ", userId: " + userId);

// ✅ 推荐：异常日志包含堆栈
log.error("处理订单异常, orderId: {}", orderId, e);

// ❌ 不推荐：异常信息丢失
log.error("处理订单异常: " + e.getMessage());  // 丢失堆栈信息
```

**Java代码质量工具配置**：

```xml
<!-- pom.xml 添加阿里代码规范插件 -->
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-pmd-plugin</artifactId>
    <version>3.21.0</version>
    <configuration>
        <rulesets>
            <ruleset>rulesets/java/ali-comment.xml</ruleset>
            <ruleset>rulesets/java/ali-concurrent.xml</ruleset>
            <ruleset>rulesets/java/ali-constant.xml</ruleset>
            <ruleset>rulesets/java/ali-exception.xml</ruleset>
            <ruleset>rulesets/java/ali-flowcontrol.xml</ruleset>
            <ruleset>rulesets/java/ali-naming.xml</ruleset>
            <ruleset>rulesets/java/ali-oop.xml</ruleset>
            <ruleset>rulesets/java/ali-orm.xml</ruleset>
            <ruleset>rulesets/java/ali-other.xml</ruleset>
            <ruleset>rulesets/java/ali-set.xml</ruleset>
        </rulesets>
    </configuration>
</plugin>
```

**Java审查命令**：

```bash
# 使用Maven运行阿里规范检查
mvn pmd:check

# 使用Checkstyle检查
mvn checkstyle:check

# 运行SpotBugs静态分析
mvn spotbugs:check

# 运行单元测试
mvn test

# 生成测试覆盖率报告
mvn jacoco:report
```

### 5.4 代码规范审查

> **Code Buddy 作为测试工程师，负责代码规范审查、扫漏和单元测试。**

**审查流程**：

```
┌─────────────────────────────────────────────────────────┐
│                 代码规范审查流程                          │
└─────────────────────────────────────────────────────────┘

  ┌──────────────────┐
  │ 代码提交/合并请求 │
  └────────┬─────────┘
           │
           ▼
  ┌──────────────────┐
  │ Code Buddy 启动  │
  │   自动审查       │
  └────────┬─────────┘
           │
           ▼
  ┌──────────────────────────────────────────┐
  │              审查检查项                   │
  ├──────────────────────────────────────────┤
  │ □ 代码风格检查（ESLint/PHPCS/PMD）        │
  │ □ 类型检查（TypeScript/PHPStan/SpotBugs）│
  │ □ 最佳实践审查（阿里规范/PSR-12）          │
  │ □ 安全漏洞扫描                            │
  │ □ 敏感信息检测                            │
  │ □ 单元测试覆盖率                          │
  │ □ 代码复杂度分析                          │
  │ □ 重复代码检测                            │
  └────────┬─────────────────────────────────┘
           │
           ▼
     ┌─────┴─────┐
     │  审查结果  │
     └─────┬─────┘
           │
     ┌─────┴─────┐
     │           │
     ▼           ▼
┌─────────┐  ┌─────────┐
│ 不通过  │  │  通过   │
└────┬────┘  └────┬────┘
     │            │
     ▼            ▼
┌─────────┐  ┌─────────┐
│生成报告 │  │允许合并 │
│修复建议 │  │更新状态 │
└─────────┘  └─────────┘
```

**审查检查项详解**：

| 检查项 | 前端工具 | PHP工具 | Java工具 | 说明 |
|--------|----------|---------|----------|------|
| 代码风格 | ESLint | PHPCS | PMD/Checkstyle | 检查代码格式是否符合规范 |
| 类型检查 | TypeScript | PHPStan | SpotBugs | 静态类型分析，发现潜在错误 |
| 安全漏洞 | npm-audit | SonarQube | SonarQube | 检测SQL注入、XSS等安全问题 |
| 敏感信息 | detect-secrets | detect-secrets | detect-secrets | 检测密钥、密码等敏感数据 |
| 单元测试 | Vitest | PHPUnit | JUnit/Jacoco | 检查测试覆盖率和测试结果 |
| 代码复杂度 | ESLint | PHPMD | PMD | 检测圈复杂度、认知复杂度 |
| 重复代码 | jscpd | PHPCPD | PMD-CPD | 检测重复代码块 |
| 最佳实践 | ESLint规则 | PHPStan规则 | 阿里PMD规则 | 检查是否符合最佳实践 |

**Code Buddy 审查命令示例**：

```bash
# 前端项目审查
npm run lint              # 代码风格检查
npm run typecheck         # TypeScript类型检查
npm run test:coverage     # 单元测试覆盖率
npm run security:audit    # 安全漏洞扫描

# 后端项目审查（PHP）
./vendor/bin/phpcs        # 代码风格检查
./vendor/bin/phpstan analyse  # 静态分析
./vendor/bin/phpunit --coverage-html  # 测试覆盖率

# 后端项目审查
mvn pmd:check             # 阿里规范检查
mvn checkstyle:check      # 代码风格检查
mvn spotbugs:check        # 静态分析
mvn test                  # 单元测试
mvn jacoco:report         # 测试覆盖率报告

# 敏感信息检测
detect-secrets scan       # 扫描敏感信息
git secrets --scan        # Git历史扫描
```

**审查报告示例**：

**前端项目审查报告**：

```markdown
# 前端代码审查报告

## 基本信息
- 项目：SpDataView
- 分支：feature/dashboard-domestic
- 审查时间：2026-04-07 10:30:00
- 审查人：Code Buddy

## 审查结果

### ❌ 不通过项（2项）

| 文件 | 问题 | 严重程度 | 修复建议 |
|------|------|----------|----------|
| src/api/order.ts | 缺少返回类型声明 | 高 | 添加返回类型 `Promise<Order[]>` |
| src/utils/format.ts | 存在硬编码密码 | 严重 | 使用环境变量替代 |

### ⚠️ 警告项（3项）

| 文件 | 问题 | 说明 |
|------|------|------|
| src/views/Dashboard.vue | 组件过大 | 建议拆分为子组件 |
| src/api/user.ts | any类型使用 | 建议定义具体类型 |
| src/components/Table.vue | 缺少注释 | 建议添加组件说明 |

### ✅ 通过项

- ESLint代码风格检查：通过
- TypeScript类型检查：通过
- 单元测试覆盖率：85%
- 无安全漏洞

## 修复建议

1. **src/api/order.ts:15** - 添加返回类型声明
   ```typescript
   // 修改前
   export function getOrders(params) { ... }
   
   // 修改后
   export function getOrders(params: OrderParams): Promise<Order[]> { ... }
   ```

2. **src/utils/format.ts:23** - 移除硬编码密码
   ```typescript
   // 修改前
   const password = 'admin123';
   
   // 修改后
   const password = process.env.DEFAULT_PASSWORD;
   ```
```

**PHP后端项目审查报告**：

```markdown
# PHP后端代码审查报告

## 基本信息
- 项目：SpDataView
- 分支：feature/dashboard-domestic
- 审查时间：2026-04-07 10:30:00
- 审查人：Code Buddy

## 审查结果

### ❌ 不通过项（2项）

| 文件 | 问题 | 严重程度 | 修复建议 |
|------|------|----------|----------|
| app/Http/Controllers/OrderController.php | 缺少返回类型声明 | 高 | 添加返回类型声明 |
| app/Services/PaymentService.php | 存在硬编码API密钥 | 严重 | 使用环境变量替代 |

### ⚠️ 警告项（3项）

| 文件 | 问题 | 说明 |
|------|------|------|
| app/Models/Order.php | 方法过长 | 建议拆分方法 |
| app/Http/Controllers/UserController.php | 缺少注释 | 建议添加方法注释 |
| routes/api.php | 路由未命名 | 建议添加路由名称 |

### ✅ 通过项

- PSR-12代码风格检查：通过
- PHPStan静态分析：通过
- 单元测试覆盖率：82%
- 无安全漏洞

## 修复建议

1. **app/Http/Controllers/OrderController.php:25** - 添加返回类型声明
   ```php
   // 修改前
   public function index(Request $request) { ... }
   
   // 修改后
   public function index(Request $request): JsonResponse { ... }
   ```

2. **app/Services/PaymentService.php:15** - 移除硬编码API密钥
   ```php
   // 修改前
   private $apiKey = 'sk_live_xxxxx';
   
   // 修改后
   private $apiKey;
   
   public function __construct() {
       $this->apiKey = config('services.payment.api_key');
   }
   ```
```

**Java后端项目审查报告**：

```markdown
# Java后端代码审查报告

## 基本信息
- 项目：OrderService
- 分支：feature/order-process
- 审查时间：2026-04-07 10:30:00
- 审查人：Code Buddy

## 审查结果

### ❌ 不通过项（3项）

| 文件 | 问题 | 严重程度 | 修复建议 |
|------|------|----------|----------|
| OrderController.java | 魔法值使用 | 高 | 使用常量或枚举替代 |
| OrderService.java | 异常直接打印堆栈 | 严重 | 使用日志框架记录异常 |
| PaymentUtil.java | 硬编码密钥 | 严重 | 使用配置中心或环境变量 |

### ⚠️ 警告项（4项）

| 文件 | 问题 | 说明 |
|------|------|------|
| OrderServiceImpl.java | 方法圈复杂度过高 | 建议拆分方法，当前复杂度15 |
| UserRepository.java | 缺少类注释 | 建议添加类说明和作者信息 |
| OrderProcessor.java | 集合判空不规范 | 建议使用CollectionUtils.isEmpty |
| Constants.java | 常量命名不规范 | 建议使用UPPER_SNAKE_CASE |

### ✅ 通过项

- 阿里PMD规范检查：通过
- SpotBugs静态分析：通过
- Checkstyle代码风格：通过
- 单元测试覆盖率：88%
- 无安全漏洞

## 修复建议

1. **OrderController.java:45** - 移除魔法值
   ```java
   // 修改前
   if (status == 1) {
       processOrder();
   }
   
   // 修改后
   if (status == OrderStatus.PENDING.getCode()) {
       processOrder();
   }
   ```

2. **OrderService.java:78** - 使用日志框架
   ```java
   // 修改前
   try {
       process(order);
   } catch (Exception e) {
       e.printStackTrace();
   }
   
   // 修改后
   try {
       process(order);
   } catch (Exception e) {
       log.error("处理订单异常, orderId: {}", order.getId(), e);
       throw new OrderProcessException("订单处理失败", e);
   }
   ```

3. **PaymentUtil.java:12** - 移除硬编码密钥
   ```java
   // 修改前
   private static final String API_KEY = "sk_live_xxxxx";
   
   // 修改后
   @Value("${payment.api.key}")
   private String apiKey;
   ```

4. **OrderServiceImpl.java:120** - 降低圈复杂度
   ```java
   // 修改前：一个方法处理所有逻辑，圈复杂度15
   
   // 修改后：拆分为多个方法
   public void processOrder(Order order) {
       validateOrder(order);
       calculateAmount(order);
       saveOrder(order);
       notifyCustomer(order);
   }
   ```
```

**审查配置文件**：

```yaml
# .codebuddy/review-config.yaml
review:
  # 代码风格检查
  lint:
    enabled: true
    auto_fix: true
    
  # 类型检查
  typecheck:
    enabled: true
    strict: true
    
  # 安全扫描
  security:
    enabled: true
    tools:
      - npm-audit
      - snyk
      
  # 敏感信息检测
  secrets:
    enabled: true
    patterns:
      - password
      - api_key
      - secret
      - token
      
  # 单元测试
  test:
    enabled: true
    coverage_threshold: 80
    
  # 代码复杂度
  complexity:
    enabled: true
    max_cyclomatic: 10
    
  # 重复代码
  duplication:
    enabled: true
    threshold: 50
```

---

## 6. 项目上下文与团队上下文

### 6.1 项目上下文（Project Context）

项目上下文帮助AI理解当前项目的结构、规范和技术栈。

**创建 `.codebuddy/project-rules.md`**：

```markdown
# 项目规范

## 技术栈
- 前端：Vue 3 + TypeScript + Vite
- 后端：PHP 8 + Laravel
- 数据库：MySQL 8.0

## 代码规范
- 遵循 PSR-12（PHP）
- 遵循 ESLint + Prettier（TypeScript）

## 命名规范
- 组件：PascalCase
- 变量/函数：camelCase
- 常量：UPPER_SNAKE_CASE

## API规范
- RESTful风格
- 统一响应格式：{ code, data, message }
```

### 6.2 团队上下文（Team Context）

团队上下文定义团队协作规范和共享知识。

**创建 `AGENTS.md`**（放在工作区根目录）：

```markdown
# 团队协作规范

## 开发流程
1. 从 develop 分支创建 feature 分支
2. 开发完成后提交 PR
3. Code Review 通过后合并

## 代码审查要点
- 代码风格是否符合规范
- 是否有足够的注释
- 是否有对应的测试用例

## 文档要求
- API变更需更新文档
- 新功能需更新README
```

### 6.3 上下文的作用

```
┌─────────────────────────────────────────────────────────┐
│                    AI理解上下文                           │
│                                                         │
│  项目上下文 ──→ 知道项目用什么技术、遵循什么规范           │
│  团队上下文 ──→ 知道团队如何协作、有什么约定               │
│  代码上下文 ──→ 知道当前文件的依赖、相关代码               │
│                                                         │
│  结果：生成的代码更符合项目规范，减少修改次数              │
└─────────────────────────────────────────────────────────┘
```

---

## 7. AI敏感信息屏蔽

### 7.1 什么是敏感信息

在使用AI编程工具时，以下信息属于敏感信息，**绝不能**暴露给AI或提交到代码仓库：

| 类型 | 示例 | 风险等级 |
|------|------|----------|
| API密钥 | `OPENAI_API_KEY`, `AWS_ACCESS_KEY` | 🔴 高 |
| 数据库凭证 | `DB_PASSWORD`, `DB_HOST`, `DB_USERNAME` | 🔴 高 |
| 第三方服务密钥 | `STRIPE_SECRET_KEY`, `MAILGUN_API_KEY` | 🔴 高 |
| JWT密钥 | `JWT_SECRET`, `APP_KEY` | 🔴 高 |
| 内网地址 | `192.168.x.x`, `10.x.x.x` | 🟡 中 |
| 测试账号密码 | `admin/admin123` | 🟡 中 |
| 个人信息 | 邮箱、手机号、身份证 | 🟡 中 |

### 7.2 为什么需要屏蔽敏感信息

```
┌─────────────────────────────────────────────────────────┐
│                    敏感信息泄露风险                        │
├─────────────────────────────────────────────────────────┤
│                                                         │
│  AI工具 ──→ 可能将敏感信息发送到云端服务器                  │
│                                                         │
│  代码仓库 ──→ 敏感信息被公开，任何人可见                    │
│                                                         │
│  日志文件 ──→ 敏感信息被记录，可能被泄露                    │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

### 7.3 配置 .gitignore

**必须忽略的文件**：

```gitignore
# 环境变量文件
.env
.env.local
.env.*.local
*.env

# 配置文件中的敏感信息
config/secrets.yml
config/database.yml

# 密钥文件
*.pem
*.key
*.p12

# 日志文件
logs/
*.log

# IDE配置（可能包含敏感信息）
.idea/
.vscode/
*.swp

# 系统文件
.DS_Store
Thumbs.db
```

### 7.4 配置AI工具忽略规则

**Trae IDE 配置**（`.trae/rules/project_rules.md`）：

```markdown
# 敏感信息处理规范

## 禁止操作
- ❌ 不要在代码中硬编码密钥、密码
- ❌ 不要将 .env 文件提交到代码仓库
- ❌ 不要在日志中输出敏感信息
- ❌ 不要将敏感信息发送给AI

## 正确做法
- ✅ 使用环境变量存储敏感信息
- ✅ 使用 .env.example 提供配置模板
- ✅ 在提交前检查是否包含敏感信息
```

**.env.example 模板**：

```env
# 数据库配置
DB_HOST=your_db_host
DB_DATABASE=your_database
DB_USERNAME=your_username
DB_PASSWORD=your_password

# API密钥
API_KEY=your_api_key

# JWT密钥
JWT_SECRET=your_jwt_secret
```

### 7.5 敏感信息检测工具

**git-secrets**（防止提交敏感信息）：

```bash
# 安装
brew install git-secrets

# 在仓库中设置
git secrets --install
git secrets --register-aws

# 扫描历史记录
git secrets --scan-history
```

**detect-secrets**（Python工具）：

```bash
# 安装
pip install detect-secrets

# 扫描项目
detect-secrets scan > .secrets.baseline

# 审计结果
detect-secrets audit .secrets.baseline
```

### 7.6 最佳实践清单

- [ ] 所有敏感信息存储在 `.env` 文件中
- [ ] `.env` 文件已添加到 `.gitignore`
- [ ] 提供 `.env.example` 作为配置模板
- [ ] 代码中不硬编码任何密钥或密码
- [ ] 日志输出不包含敏感信息
- [ ] 提交前使用 `git diff` 检查变更
- [ ] 定期轮换密钥和密码
- [ ] 使用密钥管理服务（如 AWS Secrets Manager、Vault）

---

## 8. 项目初始化与项目间映射

### 8.1 为什么需要项目间映射

在多项目工作区中，AI工具需要理解项目之间的关系才能正确工作：

```
┌─────────────────────────────────────────────────────────┐
│                    项目间关系示例                          │
├─────────────────────────────────────────────────────────┤
│                                                         │
│  前端项目 ──调用──▶ 后端API项目                           │
│     │                    │                              │
│     └──共享──▶ 公共组件库 ◀──共享──┘                      │
│                                                         │
│  AI需要知道：                                            │
│  - 哪些项目是关联的                                       │
│  - API接口定义在哪里                                      │
│  - 共享的类型/组件在哪里                                   │
│  - 数据库表结构在哪里                                     │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

### 8.2 项目初始化配置文件

在工作区根目录创建 `workspace-config.yaml`：

```yaml
# 工作区配置文件
workspace:
  name: "codebuddy_workspace"
  version: "1.0.0"
  description: "多项目协作工作区"

# 项目列表
projects:
  - name: "SpDataView"
    path: "projects/SpDataView"
    type: "fullstack"
    tech_stack:
      frontend: "vue3+vbenadmin"
      backend: "php"
    description: "逾期回款数据看板"
    
  - name: "grlogin"
    path: "projects/grlogin"
    type: "fullstack"
    tech_stack:
      frontend: "vue3"
      backend: "laravel"
    description: "用户认证系统"

# 项目间映射关系
mappings:
  # API映射：前端调用哪个后端
  api:
    - frontend: "projects/SpDataView/frontend"
      backend: "projects/SpDataView/backend"
      api_docs: "projects/SpDataView/docs/api"
      
  # 类型共享映射
  types:
    - source: "projects/SpDataView/frontend/src/types"
      shared_with: []
      
  # 数据库映射
  database:
    - project: "projects/SpDataView"
      schema: "projects/SpDataView/db/sql"
      models: "projects/SpDataView/backend/models"

# 共享资源
shared:
  libs:
    - path: "libs/CommonUtils"
      type: "npm"
  docs:
    - path: "shared/docs"
```

### 8.3 AI工具启动配置

**Trae IDE 配置**（`.trae/workspace-rules.md`）：

```markdown
# 工作区规则

## 项目关系说明

### SpDataView 项目
- 前端：Vue 3 + VbenAdmin
- 后端：PHP 原生
- 数据库：MySQL
- API文档：docs/api/

### grlogin 项目
- 前端：Vue 3 + TypeScript
- 后端：Laravel (PHP 8)
- 数据库：MySQL

## 跨项目开发规范

### 前端调用后端API
1. 先查看 API 文档：`docs/api/`
2. 确认请求参数和响应格式
3. 在 `src/api/` 目录创建接口文件

### 数据库变更
1. 先编写 SQL 脚本：`db/sql/`
2. 更新数据模型
3. 更新 API 文档

## AI工具启动时自动加载

当AI工具启动时，会自动读取以下文件：
1. `.trae/workspace-rules.md` - 工作区规则
2. `.trae/rules/project_rules.md` - 当前项目规则
3. `AGENTS.md` - 团队协作规范
4. `workspace-config.yaml` - 项目映射配置
```

### 8.4 项目初始化脚本

创建 `scripts/init-project.sh`：

```bash
#!/bin/bash

# 项目初始化脚本
# 用法: ./init-project.sh <project_name> <project_type>

PROJECT_NAME=$1
PROJECT_TYPE=$2

if [ -z "$PROJECT_NAME" ]; then
    echo "Usage: ./init-project.sh <project_name> <project_type>"
    echo "project_type: frontend | backend | fullstack"
    exit 1
fi

PROJECT_PATH="projects/$PROJECT_NAME"

# 创建项目目录结构
mkdir -p "$PROJECT_PATH"/{frontend,backend,docs,db,tests}

# 创建项目规则文件
mkdir -p "$PROJECT_PATH/.trae/rules"
cat > "$PROJECT_PATH/.trae/rules/project_rules.md" << EOF
# $PROJECT_NAME 项目规则

## 技术栈
- 前端：Vue 3 + TypeScript
- 后端：PHP / Laravel
- 数据库：MySQL

## 代码规范
- 遵循项目既有的代码风格
- 新增功能需有对应测试

## API规范
- RESTful风格
- 统一响应格式
EOF

# 创建 AGENTS.md
cat > "$PROJECT_PATH/AGENTS.md" << EOF
# $PROJECT_NAME 项目上下文

## 项目简介
[请补充项目描述]

## 开发流程
1. 从 develop 分支创建 feature 分支
2. 开发完成后提交 PR
3. Code Review 通过后合并

## 重要文档路径
- 产品文档：docs/
- API文档：docs/api/
- 数据库脚本：db/
EOF

# 创建 .env.example
cat > "$PROJECT_PATH/.env.example" << EOF
# 数据库配置
DB_HOST=localhost
DB_DATABASE=
DB_USERNAME=
DB_PASSWORD=

# API配置
API_BASE_URL=
EOF

# 创建 .gitignore
cat > "$PROJECT_PATH/.gitignore" << EOF
.env
.env.local
node_modules/
vendor/
*.log
.idea/
.vscode/
EOF

echo "项目 $PROJECT_NAME 初始化完成！"
echo "路径: $PROJECT_PATH"
```

### 8.5 自动更新项目映射

创建 `scripts/update-mappings.sh`：

```bash
#!/bin/bash

# 自动扫描项目并更新映射配置

WORKSPACE_ROOT=$(pwd)
CONFIG_FILE="$WORKSPACE_ROOT/workspace-config.yaml"

echo "扫描工作区项目..."

# 扫描 projects 目录
PROJECTS=()
for dir in projects/*/; do
    if [ -d "$dir" ]; then
        project_name=$(basename "$dir")
        PROJECTS+=("$project_name")
        echo "发现项目: $project_name"
    fi
done

# 更新配置文件中的项目列表
echo ""
echo "项目列表已更新: ${PROJECTS[*]}"
echo "请手动编辑 $CONFIG_FILE 完善项目详情"
```

### 8.6 AI启动时的项目识别流程

```
┌─────────────────────────────────────────────────────────┐
│                 AI工具启动流程                            │
└─────────────────────────────────────────────────────────┘

     ┌──────────────┐
     │  AI工具启动   │
     └──────┬───────┘
            │
            ▼
     ┌──────────────┐
     │ 读取工作区配置 │
     │ workspace-   │
     │ config.yaml  │
     └──────┬───────┘
            │
            ▼
     ┌──────────────┐
     │ 读取工作区规则 │
     │ workspace-   │
     │ rules.md     │
     └──────┬───────┘
            │
            ▼
     ┌──────────────┐
     │ 读取团队规范  │
     │ AGENTS.md    │
     └──────┬───────┘
            │
            ▼
     ┌──────────────┐
     │ 识别当前项目  │
     │ (根据工作目录)│
     └──────┬───────┘
            │
            ▼
     ┌──────────────┐
     │ 加载项目规则  │
     │ project_     │
     │ rules.md     │
     └──────┬───────┘
            │
            ▼
     ┌──────────────┐
     │ 建立项目映射  │
     │ - API关系    │
     │ - 类型共享   │
     │ - 数据库关联 │
     └──────┬───────┘
            │
            ▼
     ┌──────────────┐
     │ AI准备就绪   │
     │ 可开始开发   │
     └──────────────┘
```

### 8.7 项目映射最佳实践

| 场景 | 配置方式 | 示例 |
|------|----------|------|
| 前后端分离 | API映射 | 前端项目 → 后端API项目 |
| 微服务调用 | 服务映射 | 服务A → 服务B的接口 |
| 共享组件 | 类型映射 | 组件库 → 多个前端项目 |
| 数据库共享 | 数据库映射 | 多服务 → 同一数据库 |

### 8.8 检查清单

- [ ] `workspace-config.yaml` 已创建并配置
- [ ] `.trae/workspace-rules.md` 已创建
- [ ] 各项目的 `project_rules.md` 已创建
- [ ] 项目间的API映射已配置
- [ ] 共享资源路径已配置
- [ ] 初始化脚本可正常执行

---

## 9. 工作区搭建检查清单

完成工作区搭建后，请确认以下事项：

**基础配置**
- [ ] 目录结构符合规范
- [ ] Git仓库已初始化
- [ ] Git钩子已配置（pre-commit, commit-msg）
- [ ] 代码规范配置文件已创建（ESLint, Prettier等）
- [ ] 项目上下文文件已创建（project-rules.md）
- [ ] 团队上下文文件已创建（AGENTS.md）
- [ ] README.md 已编写
- [ ] .gitignore 已配置

**敏感信息保护**
- [ ] .env 文件已添加到 .gitignore
- [ ] .env.example 模板已创建
- [ ] 代码中无硬编码密钥/密码
- [ ] 敏感信息检测工具已配置

**项目映射配置**
- [ ] workspace-config.yaml 已创建
- [ ] 工作区规则文件已创建（workspace-rules.md）
- [ ] 项目间API映射已配置
- [ ] 共享资源路径已配置
- [ ] 项目初始化脚本已准备

---

# 第二部分：AI编程实战篇

## 实战案例：逾期回款数据看板开发

本节将以一个真实项目为例，演示从需求到提测的完整AI辅助开发流程。

### 项目概述

**项目名称**：vbendata - 逾期回款数据看板前端应用  
**项目路径**：`e:\workspace\codebuddy_workspace\projects\vbendata`

**项目名称**：SpDataView - 逾期回款数据看板（后台）  
**项目路径**：`e:\workspace\codebuddy_workspace\projects\SpDataView`

### 技术架构

> **重要说明**：本项目由两个独立项目组成，前端和后端必须分别独立运行。

---

#### 📦 前端项目：vbendata

| 属性 | 说明 |
|------|------|
| **项目名称** | vbendata |
| **项目类型** | 独立前端项目 |
| **项目路径** | `projects/vbendata` |
| **主要用途** | 逾期回款数据看板前端应用 |

**技术栈**：

| 类别 | 技术/框架 |
|------|-----------|
| 核心框架 | Vue 3 |
| UI框架 | VbenAdmin |
| 开发语言 | TypeScript |
| 构建工具 | Vite |
| 状态管理 | Pinia |
| HTTP客户端 | Axios |

**启动命令**：
```bash
cd projects/vbendata
npm install
npm run dev
```

---

#### 📦 后端项目：spdataview/backend

| 属性 | 说明 |
|------|------|
| **项目名称** | spdataview/backend |
| **项目类型** | 独立后端API项目 |
| **项目路径** | `projects/SpDataView/backend` |
| **主要用途** | 逾期回款数据看板API服务 |

**技术栈**：

| 类别 | 技术/框架 |
|------|-----------|
| 核心语言 | PHP 8 |
| 框架类型 | 原生PHP（非Laravel） |
| 数据库连接 | PDO |
| API风格 | RESTful |

**启动命令**：
```bash
cd projects/SpDataView/backend
php -S localhost:8000
```

---

#### 🔗 项目关联关系

```
┌─────────────────────────────────────────────────────────┐
│                    项目关联关系                          │
└─────────────────────────────────────────────────────────┘

  ┌─────────────────────┐         ┌─────────────────────┐
  │   前端项目          │         │   后端项目          │
  │   vbendata          │         │ spdataview/backend  │
  │                     │         │                     │
  │   Vue 3 + VbenAdmin │  HTTP   │   PHP 8 原生        │
  │   TypeScript + Vite │ ──────▶ │   RESTful API       │
  │                     │  REST   │   PDO + MySQL       │
  │   端口: 5173        │         │   端口: 8000        │
  └─────────────────────┘         └─────────────────────┘
           │                               │
           │                               │
           ▼                               ▼
    前端独立运行                      后端独立运行
    npm run dev                      php -S localhost:8000
```

### 历史开发背景

> 本项目采用前后端分离架构，前端基于VbenAdmin框架进行二次开发（独立项目），后端使用原生PHP实现API服务。

| 阶段 | 说明 |
|------|------|
| 初始架构 | 前后端分离设计，前端Vue3 + VbenAdmin，后端PHP原生 |
| 前端项目 | vbendata - 独立项目，基于VbenAdmin框架的数据看板前端应用 |
| 后端项目 | spdataview/backend - PHP原生API服务 |
| 数据层 | MySQL数据库，包含数仓同步的业务数据表 |

### 目录结构

```
projects/
├── vbendata/                        # 前端项目（独立）
│   ├── src/
│   │   ├── views/                  # 页面视图
│   │   ├── api/                    # API接口定义
│   │   ├── components/             # 公共组件
│   │   └── types/                  # TypeScript类型定义
│   └── package.json
│
└── SpDataView/                      # 后端与数据层项目
    ├── backend/                     # 后端项目
    │   ├── api/                    # API接口文件
    │   │   └── domestic/           # 国内业务接口
    │   ├── config/                 # 配置文件
    │   └── models/                 # 数据模型
    │
    ├── db/                          # 数据库脚本
    │   └── sql/                    # SQL建表语句
    │
    └── docs/                        # 项目文档
        ├── prd/                    # 产品需求文档
        ├── api/                    # API文档
        └── apiPrompt/              # API提示词文档
```

---

## 步骤1：需求整理 → 产品文档

### 1.1 原始需求（PRD）

从业务方获取的原始需求文档通常比较简略：

```markdown
# 需求简介
逾期回款数据看板与付款跟进情况

## 需求背景
1. 逾期回款周报需要导出并重复手工处理...
2. 财务临时需要数据，都是运营临时手工整理...
...
```

### 1.2 使用AI整理产品文档

**提示词示例**：
```
请根据以下原始需求，生成一份完整的产品需求文档（PRD），
包含：
1. 需求背景和目标
2. 用户角色定义
3. 功能模块详细设计
4. 页面交互说明
5. 接口需求
6. 非功能性需求

原始需求：
[粘贴原始需求内容]
```

### 1.3 多轮校正

AI生成的初版文档可能需要多轮校正：

```
第一轮：补充用户角色和权限说明
第二轮：细化页面交互细节
第三轮：补充非功能性需求（性能、安全等）
```

### 1.4 输出产物

**产品文档路径**：`docs/product-doc-v1.0.md`

---

## 步骤2：产品文档 → 原型设计

### 2.1 生成原型HTML

**提示词示例**：
```
请根据产品文档，生成一个HTML原型页面，
要求：
1. 使用纯HTML/CSS/JavaScript
2. 包含完整的页面布局
3. 模拟数据展示
4. 可交互的表格和弹窗

产品文档内容：
[粘贴产品文档相关部分]
```

### 2.2 原型校正要点

- 表格字段是否完整
- 交互逻辑是否正确
- 布局是否合理
- 数据展示是否清晰

### 2.3 输出产物

**原型文件路径**：`docs/prototype/domestic-overview.html`

---

## 步骤3：原型 → 前端交互代码

### 3.1 ⚠️ 重要：框架源码准备

**这是最容易出错的环节！**

如果使用VbenAdmin等框架，**必须先下载框架源码**，让AI阅读理解框架结构：

```
错误做法：
直接让AI生成代码 → AI产生幻觉 → 生成错误代码 → 浪费大量时间

正确做法：
1. 下载框架源码到本地
2. 让AI阅读框架核心文件
3. 理解框架的组件、API、规范
4. 再生成符合框架规范的代码
```

**提示词示例**：
```
请先阅读以下VbenAdmin框架的核心文件，理解框架的：
1. 组件使用方式
2. API调用规范
3. 状态管理模式
4. 路由配置方式

框架源码路径：[框架路径]

阅读完成后，请告诉我你理解了哪些关键点。
```

### 3.2 生成前端代码

**提示词示例**：
```
基于已理解的VbenAdmin框架，请根据原型设计生成前端代码：

要求：
1. 使用Vue 3 + TypeScript
2. 遵循框架的组件规范
3. 创建对应的API接口文件
4. 创建Mock数据

原型设计：[原型文件路径]
```

### 3.3 输出产物

```
frontend/src/views/dashboard/domestic/
├── index.vue                    # 页面入口
├── components/
│   ├── FilterArea.vue          # 筛选区域
│   ├── OverviewTable.vue       # 总览表格
│   ├── CustomerOverdueTable.vue # 客户逾期表格
│   └── OrderDetailModal.vue    # 订单明细弹窗
└── data.ts                      # Mock数据
```

---

## 步骤4：前端交互 → 后端API提示词

### 4.1 生成API提示词文档

**提示词示例**：
```
请根据前端交互需求，生成后端API的提示词文档，
包含：
1. 接口名称和路径
2. 输入参数
3. 实现逻辑
4. 输出参数格式

前端API文件：[路径]
数据库表结构：[路径]
```

### 4.2 输出产物

**API提示词路径**：`docs/apiPrompt/260401_domestic_v1.0.md`

**示例内容**：
```markdown
# 接口1：domestic_overdue_overview

## 输入参数
- stat_date: 日期，格式YYYY-MM-DD
- department_id: 部门ID

## 实现逻辑
1. 从 dws_trade_order_unpaid_in_period_by_employee_1d_inc 表查询
2. 按部门分组统计
3. 计算占比
4. 返回汇总数据

## 输出参数
{
  "code": 0,
  "data": [...],
  "message": "success"
}
```

---

## 步骤5：API提示词 → 后端接口实现

### 5.1 生成后端代码

**提示词示例**：
```
请根据API提示词文档，生成PHP后端接口代码：

要求：
1. 使用PDO连接数据库
2. 参数校验
3. SQL注入防护
4. 统一响应格式

API提示词：[文档路径]
数据库配置：[配置文件路径]
```

### 5.2 输出产物

```
backend/api/domestic/
├── overview.php                  # 总览接口
├── customer-detail.php          # 客户明细
├── last-week-payment.php        # 上周回款
└── ...
```

---

## 步骤6：前后端联调

### 6.1 联调策略：先调通一个接口

**推荐做法**：先调通第一个接口，形成模板，其他接口参照执行。

```
步骤：
1. 选择最简单的接口（如：overview）
2. 启动后端服务
3. 启动前端开发服务器
4. 检查网络请求
5. 验证数据格式
6. 确认功能正常
```

### 6.2 常见问题排查

| 问题 | 可能原因 | 解决方案 |
|------|----------|----------|
| 接口404 | 路由配置错误 | 检查路由文件 |
| CORS错误 | 跨域配置缺失 | 配置后端CORS头 |
| 数据格式不匹配 | 前后端字段不一致 | 统一字段命名 |
| 空数据返回 | SQL查询条件错误 | 检查SQL语句 |

### 6.3 批量联调

第一个接口调通后，其他接口可参照：

```
提示词：
请参照已调通的 overview 接口，帮我联调其他接口：
1. 检查API路径是否正确
2. 检查请求参数格式
3. 检查响应数据结构
4. 修复发现的问题

已调通接口：backend/api/domestic/overview.php
待调接口：backend/api/domestic/customer-detail.php
```

---

## 步骤7：单元测试

### 7.1 生成测试用例

**提示词示例**：
```
请为以下代码生成单元测试：

要求：
1. 使用 Vitest 框架
2. 覆盖正常流程
3. 覆盖边界情况
4. 覆盖异常情况

待测试代码：[文件路径]
```

### 7.2 执行测试

```bash
# 运行所有测试
npm run test

# 运行特定测试文件
npm run test -- filename.test.ts

# 生成覆盖率报告
npm run test:coverage
```

### 7.3 开发自测清单

- [ ] 功能是否按需求实现
- [ ] 边界情况是否处理
- [ ] 错误提示是否友好
- [ ] 性能是否达标
- [ ] 兼容性是否满足

---

## 步骤8：代码审查与提交

### 8.1 使用AI审查代码

**提示词示例**：
```
请审查以下代码，检查：
1. 代码规范
2. 潜在Bug
3. 性能问题
4. 安全隐患
5. 可维护性

代码文件：[文件路径]
```

### 8.2 整理提交列表

```
提示词：
请帮我整理本次开发的提交列表，按模块分组：

修改的文件：
[列出所有修改的文件]
```

### 8.3 提交代码

```bash
# 查看修改状态
git status

# 添加文件
git add .

# 提交（遵循commit规范）
git commit -m "feat(dashboard): 完成国内逾期看板开发"

# 推送
git push origin feature/dashboard-domestic
```

---

## 步骤9：提测文档

### 9.1 提测文档模板

```markdown
# 提测文档

## 版本信息
- 版本号：v1.0.0
- 提测日期：2026-04-07
- 开发人员：[姓名]

## 功能清单
| 功能模块 | 功能描述 | 测试要点 |
|----------|----------|----------|
| 国内逾期看板 | 展示逾期数据 | 数据准确性、筛选功能 |

## 测试环境
- 测试地址：http://test.example.com
- 测试账号：test / test123

## 数据准备
- 已导入测试数据
- 特殊场景数据已准备

## 已知问题
| 问题描述 | 影响范围 | 计划修复时间 |
|----------|----------|--------------|
| 暂无 | - | - |

## 测试建议
1. 重点测试数据统计准确性
2. 测试各筛选条件组合
3. 测试大数据量下的性能
```

### 9.2 提交流程

```
1. 完成开发自测
2. 编写提测文档
3. 部署到测试环境
4. 发送提测邮件/通知
5. 配合测试人员验证
```

---

## AI编程完整流程图

```
┌─────────────────────────────────────────────────────────────────────┐
│                    AI辅助开发完整流程                                  │
└─────────────────────────────────────────────────────────────────────┘

  ┌──────────┐    ┌──────────┐    ┌──────────┐    ┌──────────┐
  │ 原始需求 │───▶│ 产品文档 │───▶│ 原型设计 │───▶│ 前端代码 │
  │  (PRD)   │    │  (PRD)   │    │  (HTML)  │    │  (Vue)   │
  └──────────┘    └──────────┘    └──────────┘    └──────────┘
       │               │               │               │
       │          多轮校正         框架源码准备      Mock数据
       │                                               │
       ▼                                               ▼
  ┌──────────┐    ┌──────────┐    ┌──────────┐    ┌──────────┐
  │ 提测文档 │◀───│ 代码提交 │◀───│ 单元测试 │◀───│ 前后端   │
  │          │    │          │    │          │    │ 联调     │
  └──────────┘    └──────────┘    └──────────┘    └──────────┘
                        │
                        ▼
                  ┌──────────┐
                  │ 后端API  │
                  │ 提示词   │
                  └──────────┘
                        │
                        ▼
                  ┌──────────┐
                  │ 后端接口 │
                  │ 实现     │
                  └──────────┘
```

---

## 关键经验总结

### ✅ 成功要点

1. **框架源码必须先准备**：避免AI幻觉，节省大量调试时间
2. **先调通一个接口**：形成模板，批量复用
3. **多轮校正**：AI生成的内容需要人工审核和修正
4. **上下文很重要**：项目上下文和团队上下文能显著提高代码质量

### ❌ 常见错误

1. 直接让AI生成代码，不提供框架源码
2. 期望AI一次生成完美代码
3. 忽略代码审查环节
4. 不写测试直接提测

### 💡 效率提升技巧

1. 使用模板化的提示词
2. 建立项目知识库（上下文文件）
3. 善用AI的代码解释功能
4. 让AI帮助写文档和注释

---

# 附录：常用资源与FAQ

## 常用命令速查

### Git命令

```bash
# 初始化仓库
git init

# 查看状态
git status

# 添加所有文件
git add .

# 提交
git commit -m "feat: 功能描述"

# 推送
git push origin branch-name

# 拉取
git pull origin branch-name

# 创建分支
git checkout -b feature/xxx
```

### NPM命令

```bash
# 安装依赖
npm install

# 运行开发服务器
npm run dev

# 构建生产版本
npm run build

# 运行测试
npm run test

# 代码检查
npm run lint
```

---

## 常见问题FAQ

### Q1: AI生成的代码不符合项目规范怎么办？

**A**: 确保项目上下文文件（project-rules.md）已创建，并在对话开始时让AI阅读。如果仍有问题，可以在提示词中明确指出规范要求。

### Q2: AI生成的代码有Bug怎么办？

**A**: AI生成的代码需要人工审查。可以：
1. 让AI解释代码逻辑
2. 运行单元测试
3. 进行代码审查
4. 发现问题后让AI修复

### Q3: 如何让AI理解复杂的业务逻辑？

**A**: 
1. 分步骤描述，不要一次性给太多信息
2. 提供具体的示例
3. 使用图表或流程图辅助说明
4. 多轮对话逐步细化

### Q4: AI编程工具会取代程序员吗？

**A**: 不会。AI是辅助工具，程序员仍然需要：
1. 理解业务需求
2. 做架构决策
3. 审查AI生成的代码
4. 保证代码质量

---

## 进阶学习资源

- [Conventional Commits规范](https://www.conventionalcommits.org/)
- [Vue 3官方文档](https://vuejs.org/)
- [TypeScript官方文档](https://www.typescriptlang.org/)
- [VbenAdmin文档](https://doc.vben.pro/)

---

*文档版本：v1.0*  
*创建日期：2026-04-07*  
*适用对象：有编程基础，首次使用AI编程工具的开发者*
