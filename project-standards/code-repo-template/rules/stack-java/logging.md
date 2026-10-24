# 日志规范（Java/Spring）

## 日志框架

- 统一 SLF4J + logback，配置在各服务 `logback-spring.xml`
- 禁止 `System.out.println` 打印日志

## 结构化

- 请求入口打 access 日志：method / path / traceId / 耗时 / 状态码
- 业务关键路径（转账/充值/提现/授权）打审计日志，含操作者、金额、地址、交易哈希
- 异常日志：`logger.error("操作描述: {}", msg, e)`，携带堆栈

## traceId 透传

- 网关生成 `traceId`（UUID），注入 MDC；Feign 调用透传，日志统一带 traceId
- 跨服务排查按 traceId 串联
