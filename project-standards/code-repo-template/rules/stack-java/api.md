# API 设计规则（Java/Spring）

## URL 规范

- RESTful：`/api/{version}/{resource}/{id}`
- 资源名词复数，动词走 HTTP method
- 网关统一前缀，服务内部不带 `/api`

## 统一响应

- 成功：`{ "code": 0, "message": "success", "data": {...} }`
- 失败：`{ "code": 40001, "message": "中文错误" }`（由全局异常处理器统一）
- 分页：`{ "list": [], "total": N, "page": 1, "pageSize": 20 }`

## 校验

- 入参用 Bean Validation（`@Valid` + `@NotBlank` 等），错误信息中文
- 金额、地址等关键字段必须校验格式与边界
