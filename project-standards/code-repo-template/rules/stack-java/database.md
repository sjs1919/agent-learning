# 数据库规则（Java/Spring）

## ORM 与迁移

- MySQL + MyBatis-Plus（按服务选型统一）
- 表结构变更用 Flyway 迁移脚本（`db/migration/V{版本}__{描述}.sql`），幂等可回滚
- 迁移脚本变更后必须跑一次 `mvn flyway:migrate` 验证

## 金额与类型

- 金额一律 `BigDecimal`，禁止 float/double；数据库用 `DECIMAL(30,8)` 或按精度
- **Web3 链上金额用最小单位整数存储，展示时按 decimals 换算**（web3j `toWei`/`fromWei`）
- 时区统一 `Asia/Shanghai`，时间戳 `DATETIME`（本地时区）
- 不假设所有代币 18 位精度，按合约 decimals 读取

## 事务与一致性

- 单服务内跨表操作用 `@Transactional`，注意自调用失效
- **链上记录与内部余额分开存**；余额变化都有流水；用唯一约束 + 幂等键保证不重复入账
- 提现：同一请求幂等标识，同一地址并发统一 Nonce，防重复付款

## 连接池

- 数据库连接池用 **HikariCP**（Spring Boot 3 默认），零配置，性能优先
- 不引入 Druid；确需连接监控 / 慢 SQL 分析时按需再加

## 分库分表

- 第一版（最小学习闭环）**不引入**分库分表，保持单库单表
- 当交易 / 充值流水数据量大时，用 **Apache ShardingSphere-JDBC**（JDBC 层透明接入，与 MyBatis-Plus 兼容，与 Nacos 生态无冲突）
- 引入前置：明确分片键（如按 address / 用户维度）、事务边界（跨分片不保证分布式事务，配合状态机 / 幂等 / 对账）
