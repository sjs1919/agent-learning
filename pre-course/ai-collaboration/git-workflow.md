# Git 团队协作工作流

## 🌿 分支模型

```
main (生产环境)
  ↑
develop (开发集成)
  ↑
feature/* (功能开发)
hotfix/* (紧急修复)
release/* (发布准备)
```

## 📋 分支命名规范

| 分支类型 | 命名格式 | 示例 |
|---------|---------|------|
| 功能分支 | `feature/<ticket-id>-描述` | `feature/PROJ-123-user-login` |
| Bug修复 | `fix/<ticket-id>-描述` | `fix/PROJ-456-api-timeout` |
| 热修复 | `hotfix/版本号-描述` | `hotfix/v1.2.1-security-patch` |
| 发布 | `release/版本号` | `release/v1.3.0` |

## 🔄 标准开发流程

### 1. 开始新功能

```bash
# 确保 develop 分支是最新的
git checkout develop
git pull origin develop

# 创建功能分支
git checkout -b feature/PROJ-123-add-search

# 进行开发并提交
git add .
git commit -m "feat(search): 添加商品搜索功能

- 实现基于关键词的搜索
- 添加搜索历史记录
- 支持筛选和排序

Closes PROJ-123"
```

### 2. 提交规范（Conventional Commits）

```
<type>(<scope>): <subject>

<body>

<footer>
```

**类型说明：**
- `feat`: 新功能
- `fix`: Bug 修复
- `docs`: 文档更新
- `style`: 代码格式（不影响功能）
- `refactor`: 重构
- `perf`: 性能优化
- `test`: 测试相关
- `chore`: 构建/工具
- `ci`: CI/CD 配置
- `revert`: 回滚

**示例：**
```bash
feat(user): 添加用户注册功能

- 实现邮箱验证
- 添加密码强度检查
- 集成验证码服务

Closes #123
```

### 3. 提交前检查

```bash
# 提交前会自动运行：
# 1. ESLint 代码检查
# 2. Prettier 格式化
# 3. TypeScript 类型检查
# 4. 单元测试

git add .
git commit -m "feat: xxx"
```

如果检查失败，提交会被阻止。修复问题后重新提交。

### 4. 推送与 PR

```bash
# 推送分支
git push -u origin feature/PROJ-123-add-search

# 创建 PR 到 develop 分支（通过 GitHub/GitLab 界面）
```

**PR 要求：**
- 标题清晰描述变更
- 填写 PR 模板中的所有项目
- 关联相关 Issue
- 确保 CI 检查通过
- 至少 1 人审查通过

### 5. 代码审查流程

**审查者职责：**
1. 理解代码变更的意图
2. 检查代码质量和规范
3. 验证测试覆盖
4. 提出改进建议

**审查清单：**
- [ ] 代码符合规范
- [ ] 命名清晰
- [ ] 有适当的注释
- [ ] 测试覆盖充分
- [ ] 没有安全问题
- [ ] 性能考虑到位

**审查评论级别：**
- 🔴 **阻塞 (Blocking)**: 必须修复
- 🟡 **建议 (Suggestion)**: 建议采纳
- 🟢 **赞赏 (Praise)**: 好的实践

### 6. 合并与部署

```bash
# PR 审查通过后，使用 Squash Merge 合并
git checkout develop
git pull origin develop

# 删除已合并的功能分支
git branch -d feature/PROJ-123-add-search
git push origin --delete feature/PROJ-123-add-search
```

## 🆘 常见问题

### 提交被阻止

```bash
# 查看具体错误
npm run lint:check
npm run typecheck
npm run test:unit

# 自动修复可修复的问题
npm run lint
npm run format
```

### 解决冲突

```bash
git checkout develop
git pull origin develop

git checkout feature/xxx
git rebase develop

# 解决冲突后
git add .
git rebase --continue

git push --force-with-lease
```

### 撤销提交

```bash
# 撤销最后一次提交，保留更改
git reset --soft HEAD~1

# 完全丢弃最后一次提交
git reset --hard HEAD~1

# 创建撤销提交
git revert <commit-hash>
```

## 📊 提交统计

```bash
# 查看提交统计
git shortlog -sn --all

# 查看某人的提交
git log --author="用户名" --oneline

# 查看提交热力图
git log --pretty=format:%ad --date=short | sort | uniq -c
```
