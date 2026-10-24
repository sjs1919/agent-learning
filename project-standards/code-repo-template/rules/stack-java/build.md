# 构建与版本约束（Java/Spring）

## 版本基线

- **Java 17 固定**（Spring Boot 3.x 最低要求，生产服务器不支持更高版本则禁止升级）
- Spring Boot 3.2.x + Spring Cloud Alibaba 2023.0.x（与 Spring Cloud 2023.0.x 版本对齐）+ Nacos 2.3.x
- web3j 4.9.x
- 数据库连接池：HikariCP（Spring Boot 3 自带，无需额外引入）
- 分库分表（扩展期引入）：Apache ShardingSphere-JDBC 5.4.x（第一版不引入，按需启用）

## 构建

- Maven 多模块：`backend/pom.xml` 用 `dependencyManagement` 统一依赖版本（所有 mvn 命令在 `backend/` 目录执行）
- 构建命令：`cd backend && mvn compile` / `cd backend && mvn test` / `cd backend && mvn -pl <模块> -am package`
- 构建/测试在 **WSL** 下执行（Windows npm/maven 可能遇 EPERM 文件锁）

## 启动

- 本地：先起 Nacos（8848）与 `anvil`（8545），再 `cd backend && mvn -pl <服务> spring-boot:run`
- 端口约定：网关 8080、account-service 8081、deposit-scanner 8082、withdrawal-service 8083
