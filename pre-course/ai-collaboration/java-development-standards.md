# Java 开发规范

> 适用项目：所有包含 Java 技术栈的项目（当前 Monorepo 预留扩展）
> 最后更新：2026-03-31

---

## 一、构建工具规范

### 使用 Maven Wrapper（强制）

所有 Java 项目必须使用 **Maven Wrapper（`mvnw`）**，禁止依赖全局安装的 `mvn`，确保所有开发者和 CI 环境使用完全一致的 Maven 版本。

```bash
# 初始化 Maven Wrapper（在 Java 项目根目录执行）
mvn wrapper:wrapper -Dmaven=3.9.6

# 之后统一使用
./mvnw clean install      # Linux/macOS
mvnw.cmd clean install    # Windows
```

**目录结构**：
```
projects/<java-project>/
├── .mvn/
│   └── wrapper/
│       └── maven-wrapper.properties   ← Maven 版本锁定
├── mvnw                               ← Linux/macOS 启动脚本
├── mvnw.cmd                           ← Windows 启动脚本
├── pom.xml                            ← 项目依赖配置
└── src/
    ├── main/java/
    └── test/java/
```

### Maven 版本要求

- **Maven**：3.8.0+（推荐 3.9.x）
- **JDK**：17+（Spring Boot 3.x 要求）
- **编译目标**：`<java.version>17</java.version>`

---

## 二、代码风格（CheckStyle — Google Style）

### 配置文件

使用 `shared/configs/checkstyle.xml`（基于 Google Java Style 定制）。

### Maven 插件配置（`pom.xml` 中添加）

```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-checkstyle-plugin</artifactId>
    <version>3.3.1</version>
    <configuration>
        <configLocation>${project.basedir}/../../shared/configs/checkstyle.xml</configLocation>
        <consoleOutput>true</consoleOutput>
        <failsOnError>true</failsOnError>
        <linkXRef>false</linkXRef>
    </configuration>
    <executions>
        <execution>
            <id>validate</id>
            <phase>validate</phase>
            <goals><goal>check</goal></goals>
        </execution>
    </executions>
</plugin>
```

### 核心风格规则

| 规则 | 要求 |
|------|------|
| 缩进 | 4 个空格（禁止 Tab） |
| 行长度 | ≤ 120 字符 |
| 大括号 | K&R 风格（左括号不换行） |
| 类名 | PascalCase |
| 方法名/变量名 | camelCase |
| 常量 | UPPER_SNAKE_CASE |
| 包名 | 全小写 |

### IDE 配置

- **IntelliJ IDEA**：安装 `CheckStyle-IDEA` 插件，指向 `shared/configs/checkstyle.xml`
- **VS Code**：安装 `Checkstyle for Java` 扩展

---

## 三、测试框架

### 测试技术栈

| 类型 | 框架 | 说明 |
|------|------|------|
| 单元测试 | JUnit 5 + Mockito | `@ExtendWith(MockitoExtension.class)` |
| 集成测试 | Spring Boot Test | `@SpringBootTest` |
| 覆盖率 | JaCoCo | 阈值：所有指标 ≥ 80% |

### 目录结构

```
src/
├── main/java/com/example/
│   ├── controller/
│   ├── service/
│   └── repository/
└── test/java/com/example/
    ├── unit/              ← 单元测试（不启动 Spring 容器��
    │   ├── service/
    │   └── util/
    └── integration/       ← 集成测试（启动完整 Spring 容器）
        └── controller/
```

### JaCoCo 覆盖率配置（`pom.xml`）

```xml
<plugin>
    <groupId>org.jacoco</groupId>
    <artifactId>jacoco-maven-plugin</artifactId>
    <version>0.8.11</version>
    <executions>
        <execution>
            <goals><goal>prepare-agent</goal></goals>
        </execution>
        <execution>
            <id>report</id>
            <phase>test</phase>
            <goals><goal>report</goal></goals>
        </execution>
        <execution>
            <id>check</id>
            <goals><goal>check</goal></goals>
            <configuration>
                <rules>
                    <rule>
                        <element>BUNDLE</element>
                        <limits>
                            <limit>
                                <counter>INSTRUCTION</counter>
                                <value>COVEREDRATIO</value>
                                <minimum>0.80</minimum>
                            </limit>
                            <limit>
                                <counter>BRANCH</counter>
                                <value>COVEREDRATIO</value>
                                <minimum>0.80</minimum>
                            </limit>
                        </limits>
                    </rule>
                </rules>
            </configuration>
        </execution>
    </executions>
</plugin>
```

### 运行命令

```bash
./mvnw test                    # 运行测试
./mvnw test jacoco:report      # 生成覆盖率报告（target/site/jacoco/index.html）
./mvnw verify                  # 完整构建（含覆盖率门禁检查）
./mvnw checkstyle:check        # 仅检查代码风格
```

---

## 四、静态代码分析

### SonarQube / SonarCloud

**本地运行**（需提前启动 SonarQube 服务）：
```bash
./mvnw sonar:sonar \
  -Dsonar.projectKey=<project-key> \
  -Dsonar.host.url=http://localhost:9000 \
  -Dsonar.login=<your-token>
```

**质量门禁要求**（Quality Gate）**：
- 新增代码覆盖率 ≥ 80%
- 新增代码重复率 ≤ 3%
- 严重/阻断 Bug = 0
- 严重/阻断漏洞 = 0

### SpotBugs（静态 Bug 检测）

```xml
<!-- pom.xml 中添加 -->
<plugin>
    <groupId>com.github.spotbugs</groupId>
    <artifactId>spotbugs-maven-plugin</artifactId>
    <version>4.8.3.1</version>
    <executions>
        <execution>
            <goals><goal>check</goal></goals>
        </execution>
    </executions>
</plugin>
```

---

## 五、依赖漏洞扫描

```xml
<!-- pom.xml 中添加 -->
<plugin>
    <groupId>org.owasp</groupId>
    <artifactId>dependency-check-maven</artifactId>
    <version>9.0.9</version>
    <executions>
        <execution>
            <goals><goal>check</goal></goals>
        </execution>
    </executions>
    <configuration>
        <failBuildOnCVSS>7</failBuildOnCVSS>  <!-- CVSS ≥ 7 ��构建失败 -->
    </configuration>
</plugin>
```

```bash
./mvnw dependency-check:check   # 本地运行漏洞扫描
```

---

## 六、提交前检查（pre-commit 补充）

Java 项目在 `pre-commit` hook 中应增加：

```bash
# Java 项目检查（如果有 Java 文件变更）
JAVA_CHANGED=$(git diff --cached --name-only | grep -E "^projects/.+\.java$" | head -1)
if [ -n "$JAVA_CHANGED" ]; then
  JAVA_PROJECT=$(echo "$JAVA_CHANGED" | grep -oE "^projects/[^/]+" | head -1)
  if [ -f "$JAVA_PROJECT/mvnw" ]; then
    echo "🔍 Java 代码检查..."
    (cd "$JAVA_PROJECT" && ./mvnw checkstyle:check -q)
    [ $? -ne 0 ] && echo "❌ Java CheckStyle 检查失败" && exit 1
    echo "✅ Java 代码检查通过"
  fi
fi
```

---

## 七、AI 工具协作标记

Java 代码中的 AI 生成标记使用 Javadoc 注释：

```java
// [AI:Claude] 架构设计 — 策略模式选择
// [AI:Lingma] 基础实现
// [AI:CodeBuddy] 重构优化

/**
 * 订单服务实现
 * [AI:Claude] 分析并设计了幂等性处理方案
 */
public class OrderService {
    // ...
}
```
