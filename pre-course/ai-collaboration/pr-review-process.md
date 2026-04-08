# PR/MR 审查流程规范

> **适用范围**: 20 人混合团队  
> **目标**: 确保代码质量，促进知识共享

---

## 一、分支策略

```
main          ← 生产环境，只接受 PR 合并
  ↑
develop       ← 开发集成分支
  ↑
feature/PROJ-123-desc   ← 功能分支
fix/PROJ-456-desc       ← Bug 修复分支
hotfix/desc             ← 紧急修复分支
```

### 分支命名规范

```
feature/<ticket-id>-<简短描述>
fix/<ticket-id>-<简短描述>
hotfix/<简短描述>
```

---

## 二、PR 创建规范

### 2.1 PR 大小限制

| 指标 | 限制 |
|------|------|
| 代码行数 | ≤ 400 行 |
| 文件数量 | ≤ 15 个 |
| 提交数量 | ≤ 5 个 |

### 2.2 PR 标题格式

```
<type>(<scope>): <简短描述>

# 示例
feat(auth): 实现 JWT 双 Token 刷新机制
fix(dashboard): 修复逾期金额统计错误
```

---

## 三、审查流程

### 3.1 Reviewer 分配规则

| PR 类型 | 最少 Reviewers | 特殊要求 |
|---------|---------------|----------|
| 普通 feature/fix | 1 人 | 同模块开发者 |
| 影响共享配置 | 2 人 | 含 CODEOWNER |
| hotfix | 1 人 | Tech Lead |

### 3.2 审查检查清单

**代码质量**:
- [ ] 代码逻辑正确，无明显 Bug
- [ ] 符合编码规范（ESLint/CheckStyle/PHP-CS-Fixer 通过）
- [ ] 单元测试覆盖 ≥ 80%
- [ ] 无敏感信息泄露

**架构设计**:
- [ ] 与现有架构一致
- [ ] 无重复代码（DRY 原则）
- [ ] 边界情况已处理

**文档**:
- [ ] 代码注释清晰
- [ ] 外部文档已更新

### 3.3 审查状态标记

| 标记 | 含义 | 操作 |
|------|------|------|
| ✅ Approved | 审查通过，可以合并 | 合并到目标分支 |
| 🔄 Changes Requested | 需要修改 | 作者修改后重新请求审查 |
| 💬 Comment | 仅评论，不阻止合并 | 作者自行判断是否修改 |

---

## 四、合并规则

### 4.1 合并前必须满足

1. 所有 CI 检查通过
2. 获得规定数量的 Approved
3. 无冲突需要解决
4. 提交信息符合规范

### 4.2 合并方式

```
squash and merge  ← 推荐（保持主干历史整洁）
```

---

## 五、特殊场景

### 5.1 hotfix 流程

```
1. 从 main 创建 hotfix 分支
2. 修复问题，本地测试
3. 创建 PR → main（Tech Lead 审查）
4. 合并后同步到 develop
```

### 5.2 冲突解决

```
1. 本地更新目标分支: git fetch origin
2. 变基: git rebase origin/develop
3. 解决冲突，继续变基
4. 强制推送: git push -f
```

---

## 六、相关文件

| 文件 | 用途 |
|------|------|
| `.github/CODEOWNERS` | 模块负责人绑定 |
| `.github/pull_request_template.md` | PR 模板 |
| `.github/workflows/pr-check.yml` | CI 自动检查 |
