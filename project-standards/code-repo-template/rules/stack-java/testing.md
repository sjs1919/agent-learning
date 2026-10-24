# 测试规范（Java/Spring）

## 单元测试

- JUnit 5 + Mockito，测试类与源码同包路径（`src/test/java`）
- Service 层测业务逻辑（mock Repository），Controller 层测参数校验与响应
- 关键路径必须测：转账成功/失败、余额不足、重复请求幂等、异常分支

## 集成测试

- `@SpringBootTest` + 内存库（H2）或本地 MySQL
- 链上交互用 mock 或本地 `anvil`，不依赖真实链
- 测试命令：`mvn test`（WSL 下）

## 覆盖

- 新增功能必须带测试；修复 bug 先写失败用例再修（TDD）
