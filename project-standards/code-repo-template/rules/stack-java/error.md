# 错误处理与错误码规则（Java/Spring）

## 异常处理

- 所有业务异常必须向上传播，**🚫 严禁「catch 后打日志然后丢弃」**
- 业务错误用 `BusinessException`（含错误码），在 Service 层抛出
- 全局统一用 `@RestControllerAdvice` 捕获，返回统一错误响应，Controller 不手写 try-catch
- 下层记录日志后抛出，上层不重复记录（除非补充上下文）
- **关键写操作失败必须向上抛**：涉及资金、权限、审计等对账关键路径，仅日志不足以上报故障，必须抛异常确保上层补偿或告警

## 错误码

- 格式：5 位 = 3 位 HTTP 状态码 + 2 位业务细分码（如 `40001`、`40101`、`40301`）
- 全部集中在 `common` 模块 `errcode` 包定义，禁止硬编码
- 每个错误码含 `code` + `message`（中文），支持自定义 message
- 新增错误码必须先更新 `common` 的码表，再引用使用

## 错误信息语言

- 错误 message 统一中文，专有名词保留英文（如 mysql、redis、web3j、private key、nonce）
- 格式：`操作描述: 具体原因`
