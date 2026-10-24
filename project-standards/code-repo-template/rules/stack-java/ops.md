# 运维与运行规则（Java/Spring）

## 健康检查

- 接入 Spring Boot Actuator：`/actuator/health`，含 DB/Redis/Nacos 就绪探针
- 服务注册 Nacos，启动需 Nacos 可达

## 优雅关闭

- `spring.lifecycle.timeout-per-shutdown-phase` 配置合理值
- 关闭时停止拉新、处理在途提现/扫描任务（幂等可恢复）

## 配置与密钥

- 配置中心 Nacos；敏感配置（私钥/助记词/密码）用环境变量或 `credentials.local.md`（gitignored），**不入配置中心明文**
