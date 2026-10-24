# 架构与分层规则（Java/Spring）

## 分层架构（每个微服务内）

- Controller（`xxx/controller/`）：参数校验 + 调 Service + 组装响应，不含业务逻辑
- Service（`xxx/service/`）：业务逻辑 + 事务编排 + 跨 Repo 协调，不直接操作 DB
- Repository（`xxx/repository/`）：MyBatis-Plus 等 CRUD 封装，不含业务判断
- Entity/Model（`xxx/entity/`）：数据库实体，不得直接用于响应序列化

- 禁止跨层：Controller 不调 Repository，Service 不返回 HTTP 响应

## 微服务边界

- 服务间调用用 OpenFeign，接口定义在调用方 `client/` 包（含 fallback）
- 跨服务数据一致性：不跨库事务；用状态机 + 幂等键保证最终一致（如提现状态机）
- 网关（gateway）只做路由/鉴权/限流，不落业务逻辑
- 服务划分：gateway / account-service / deposit-scanner / withdrawal-service，公共代码进 `common`

## DTO/VO 分离

- Request DTO（含校验注解）定义在 Controller 层
- Response VO（过滤敏感字段、格式化输出）定义在 Controller 层
- Entity 不得直接用于请求绑定或响应序列化
- 转换用 MapStruct 或手动 `toVO()`，禁止在 Entity 上加业务方法
