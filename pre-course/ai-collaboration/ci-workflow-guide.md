# GitHub Actions CI/CD 管理规范

## 工作流命名规范

- `ci-*.yml` - 持续集成工作流（代码检查、测试、构建）
- `cd-*.yml` - 持续部署工作流（部署到服务器、云平台）
- `scheduled-*.yml` - 定时任务工作流（数据备份、依赖更新等）

## 工作流触发条件

### 推送触发（Push）
```yaml
on:
  push:
    branches:
      - master
      - develop
      - 'feature/**'
```

### Pull Request 触发
```yaml
on:
  pull_request:
    branches:
      - master
      - develop
    types: [opened, synchronize, reopened]
```

### 定时触发
```yaml
on:
  schedule:
    - cron: '0 2 * * *'  # 每天凌晨 2 点
```

### 手动触发
```yaml
on:
  workflow_dispatch:
    inputs:
      environment:
        description: '部署环境'
        required: true
        default: 'staging'
        type: choice
        options:
          - staging
          - production
```

## Job 最佳实践

### 1. 使用矩阵策略处理多版本
```yaml
jobs:
  test:
    strategy:
      matrix:
        node-version: [18.x, 20.x]
        os: [ubuntu-latest, windows-latest]
    steps:
      - uses: actions/setup-node@v4
        with:
          node-version: ${{ matrix.node-version }}
```

### 2. 缓存依赖加速构建
```yaml
- name: Setup Node.js
  uses: actions/setup-node@v4
  with:
    node-version: '20'
    cache: 'npm'  # 或 'pnpm', 'yarn'
    cache-dependency-path: path/to/package-lock.json
```

### 3. 并行执行独立任务
```yaml
jobs:
  lint:
    # 代码检查
  test:
    # 单元测试
  build:
    # 构建项目
```

### 4. 依赖关系管理
```yaml
jobs:
  test:
    runs-on: ubuntu-latest
    # ...

  deploy:
    runs-on: ubuntu-latest
    needs: test  # 依赖 test 任务完成后执行
    # ...
```

### 5. 条件执行
```yaml
- name: Deploy to production
  if: github.ref == 'refs/heads/master' && github.event_name == 'push'
  run: deploy-script.sh
```

## 安全规范

### 1. 使用 Secrets 存储敏感信息
```yaml
- name: Deploy to server
  env:
    SSH_KEY: ${{ secrets.SSH_PRIVATE_KEY }}
    DB_PASSWORD: ${{ secrets.DB_PASSWORD }}
  run: |
    echo "$SSH_KEY" > key.pem
    chmod 600 key.pem
```

### 2. 限制 Token 权限
```yaml
permissions:
  contents: read
  pull-requests: write
```

### 3. 使用 OIDC 代替静态凭证
```yaml
- name: Configure AWS credentials
  uses: aws-actions/configure-aws-credentials@v4
  with:
    role-to-assume: ${{ secrets.AWS_ROLE_ARN }}
    aws-region: us-east-1
```

## 产物管理

### 1. 上传构建产物
```yaml
- name: Upload artifacts
  uses: actions/upload-artifact@v4
  with:
    name: dist-files
    path: dist/
    retention-days: 7
    if-no-files-found: warn
```

### 2. 下载产物
```yaml
- name: Download artifacts
  uses: actions/download-artifact@v4
  with:
    name: dist-files
    path: ./dist
```

### 3. 使用缓存替代产物
对于频繁复用的依赖，优先使用缓存而非上传产物。

## 通知规范

### 1. 失败通知
```yaml
- name: Notify on failure
  if: failure()
  uses: 8398a7/action-slack@v3
  with:
    status: ${{ job.status }}
    webhook_url: ${{ secrets.SLACK_WEBHOOK }}
```

### 2. PR 评论
```yaml
- name: Comment PR
  if: github.event_name == 'pull_request'
  uses: actions/github-script@v7
  with:
    script: |
      github.rest.issues.createComment({
        issue_number: context.issue.number,
        owner: context.repo.owner,
        repo: context.repo.repo,
        body: '✅ Build passed!'
      })
```

## 常用 Actions 版本

| Action | 推荐版本 | 说明 |
|--------|---------|------|
| actions/checkout | v4 | 检出代码 |
| actions/setup-node | v4 | 设置 Node.js |
| actions/setup-python | v5 | 设置 Python |
| shivammathur/setup-php | v2 | 设置 PHP |
| pnpm/action-setup | v2 | 设置 pnpm |
| actions/upload-artifact | v4 | 上传产物 |
| actions/download-artifact | v4 | 下载产物 |
| actions/cache | v4 | 缓存 |

## 故障排查

### 常见问题

1. **缓存失效**：更新 `cache-dependency-path` 或清理缓存
2. **权限错误**：检查 `permissions` 配置和 Token 权限
3. **超时问题**：增加 `timeout-minutes` 或优化任务
4. **环境变量未生效**：使用 `env` 块或在步骤中设置

### 调试技巧

```yaml
- name: Debug environment
  run: |
    echo "Runner OS: ${{ runner.os }}"
    echo "Ref: ${{ github.ref }}"
    echo "Event: ${{ github.event_name }}"
    env | sort
```

## 参考资源

- [GitHub Actions 官方文档](https://docs.github.com/en/actions)
- [Actions 市场](https://github.com/marketplace?type=actions)
- [工作流语法参考](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions)
