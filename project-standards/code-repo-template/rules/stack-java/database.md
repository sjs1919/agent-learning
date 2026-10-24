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
