# 自动化上下文更新机制实施指南

> **文档类型**: 技术实施指南  
> **目标**: 实现代码提交后自动更新项目上下文和工作区知识库  
> **适用对象**: DevOps、技术负责人、开发团队  

---

## 概述

自动化上下文更新机制通过在 Git Hooks 中集成 `post-commit` 钩子，在每次代码提交后自动执行上下文更新脚本，实现以下目标：

1. **自动更新项目上下文**: 记录项目最近的提交历史、变更类型、关键文件等
2. **维护工作区知识库**: 汇总团队活动、项目统计、技术栈更新等信息
3. **多工具上下文同步**: 将更新后的上下文同步到所有 AI 工具（Trae、CodeBuddy、Lingma）

---

## 架构设计

### 工作流程

```
代码提交 (git commit)
    ↓
post-commit Hook 触发
    ↓
auto-update-context.js 执行
    ├── 获取 Git 提交信息
    ├── 识别变更的项目
    ├── 分析变更类型
    ├── 更新项目上下文 (project-contexts/<project>.json)
    ├── 更新工作区知识库 (team-knowledge.json)
    ├── 记录更新日志
    └── 同步到其他 AI 工具
    ↓
完成更新
```

### 文件结构

```
shared/
├── .ai-memory/
│   ├── project-contexts/           # 项目上下文目录
│   │   ├── qwlogin.json
│   │   ├── SpDataView.json
│   │   └── vbenData.json
│   ├── team-knowledge.json         # 团队知识库
│   └── logs/                       # 更新日志
│       └── context-update-2026-03-26.log
├── scripts/
│   └── auto-update-context.js      # 自动更新脚本
└── docs/
    └── auto-context-update-guide.md # 本文档

.husky/
└── post-commit                     # Git Hook
```

---

## 实施步骤

### 步骤 1: 安装 post-commit Hook

#### 方法一：使用 Husky（推荐）

如果项目已配置 Husky，直接将 `post-commit` 文件复制到 `.husky/` 目录：

```bash
# 确保 post-commit 文件存在且可执行
cp shared/templates/.husky/post-commit .husky/post-commit
chmod +x .husky/post-commit
```

#### 方法二：手动配置 Git Hooks

如果没有使用 Husky，手动配置 Git Hooks：

```bash
# 复制 hook 文件到 .git/hooks/
cp .husky/post-commit .git/hooks/post-commit
chmod +x .git/hooks/post-commit
```

**注意**: 手动配置的方式在重新克隆仓库后需要重新设置。

### 步骤 2: 验证 Node.js 环境

确保系统中已安装 Node.js：

```bash
node --version  # 要求 v14.0.0 或更高版本
npm --version
```

### 步骤 3: 测试自动更新

执行一次测试提交，验证自动更新机制：

```bash
# 创建测试文件
echo "# Test" > projects/qwlogin/test-file.md
git add projects/qwlogin/test-file.md
git commit -m "test: verify auto context update"

# 观察输出，应该看到上下文更新信息
```

### 步骤 4: 验证更新结果

检查上下文文件是否正确更新：

```bash
# 查看项目上下文
cat shared/.ai-memory/project-contexts/qwlogin.json

# 查看团队知识库
cat shared/.ai-memory/team-knowledge.json

# 查看更新日志
cat shared/.ai-memory/logs/context-update-$(date +%Y-%m-%d).log
```

---

## 配置说明

### 自动更新脚本配置

编辑 `shared/scripts/auto-update-context.js` 中的 `CONFIG` 对象：

```javascript
const CONFIG = {
  memoryPath: './shared/.ai-memory',    // 上下文存储路径
  projectsPath: './projects',           // 项目目录路径
  logPath: './shared/.ai-memory/logs',  // 日志存储路径
  maxLogFiles: 10                       // 保留的最大日志文件数
};
```

### 上下文文件格式

#### 项目上下文 (project-contexts/<project>.json)

```json
{
  "name": "qwlogin",
  "lastUpdated": "2026-03-26T10:30:00.000Z",
  "recentCommits": [
    {
      "hash": "a1b2c3d",
      "message": "feat: add user authentication",
      "author": "Developer Name",
      "date": "2026-03-26T10:30:00.000Z",
      "types": ["feature", "api-change"]
    }
  ],
  "changeHistory": [
    {
      "date": "2026-03-26T10:30:00.000Z",
      "commit": "a1b2c3d",
      "types": ["feature", "api-change"],
      "filesChanged": 5
    }
  ],
  "keyFiles": [
    "src/components/Login.vue",
    "src/api/auth.ts"
  ],
  "techStack": ["Vue 3", "TypeScript", "Vite"]
}
```

#### 团队知识库 (team-knowledge.json)

```json
{
  "lastUpdated": "2026-03-26T10:30:00.000Z",
  "recentActivity": [
    {
      "date": "2026-03-26T10:30:00.000Z",
      "commit": "a1b2c3d",
      "author": "Developer Name",
      "message": "feat: add user authentication",
      "projects": ["qwlogin"],
      "changeTypes": ["feature", "api-change"]
    }
  ],
  "projectStats": {
    "qwlogin": {
      "totalCommits": 42,
      "lastCommitDate": "2026-03-26T10:30:00.000Z",
      "contributors": ["Developer A", "Developer B"],
      "changeTypes": {
        "feature": 15,
        "bugfix": 8,
        "refactor": 5
      }
    }
  },
  "commonPatterns": [],
  "techStackUpdates": []
}
```

---

## 变更类型识别规则

脚本根据提交信息和文件变更自动识别变更类型：

### 基于提交信息前缀

| 前缀 | 变更类型 | 示例 |
|------|---------|------|
| `feat`, `feature` | feature | `feat: add login page` |
| `fix` | bugfix | `fix: resolve auth bug` |
| `docs` | documentation | `docs: update README` |
| `refactor` | refactor | `refactor: optimize query` |
| `test` | test | `test: add unit tests` |
| `chore` | chore | `chore: update dependencies` |

### 基于文件路径

| 文件路径模式 | 变更类型 |
|-------------|---------|
| `api/`, `controller/` | api-change |
| `database/`, `migration/` | database-change |
| `config/` | config-change |

---

## 故障排除

### 问题 1: post-commit Hook 未触发

**症状**: 提交代码后没有看到上下文更新信息

**排查步骤**:
1. 检查 hook 文件是否存在：
   ```bash
   ls -la .husky/post-commit
   # 或
   ls -la .git/hooks/post-commit
   ```

2. 检查文件是否有执行权限：
   ```bash
   chmod +x .husky/post-commit
   ```

3. 手动测试 hook：
   ```bash
   .husky/post-commit
   ```

### 问题 2: 脚本执行失败

**症状**: 看到 "AI 上下文更新失败" 提示

**排查步骤**:
1. 检查 Node.js 是否安装：
   ```bash
   node --version
   ```

2. 手动执行脚本查看错误：
   ```bash
   node shared/scripts/auto-update-context.js
   ```

3. 检查文件权限：
   ```bash
   ls -la shared/.ai-memory/
   ```

### 问题 3: 上下文文件未更新

**症状**: 脚本执行成功但文件内容未变化

**排查步骤**:
1. 检查项目识别是否正确：
   - 确保变更文件在 `projects/<project-name>/` 目录下

2. 检查文件权限：
   ```bash
   chmod -R 755 shared/.ai-memory/
   ```

3. 查看详细日志：
   ```bash
   cat shared/.ai-memory/logs/context-update-$(date +%Y-%m-%d).log
   ```

### 问题 4: 多工具同步失败

**症状**: 其他 AI 工具上下文未更新

**排查步骤**:
1. 检查目标工具目录是否存在：
   ```bash
   ls -la .trae/context/project-contexts/
   ls -la .codebuddy/context/project-contexts/
   ls -la .lingma/context/project-contexts/
   ```

2. 手动执行同步：
   ```bash
   node shared/scripts/sync-ai-context.js
   ```

---

## 最佳实践

### 1. 定期清理旧日志

虽然脚本会自动清理旧日志，但建议定期检查：

```bash
# 查看日志文件大小
ls -lh shared/.ai-memory/logs/

# 手动清理（保留最近 7 天）
find shared/.ai-memory/logs/ -name "*.log" -mtime +7 -delete
```

### 2. 监控上下文文件大小

上下文文件过大可能影响性能：

```bash
# 检查文件大小
ls -lh shared/.ai-memory/project-contexts/
ls -lh shared/.ai-memory/team-knowledge.json

# 如果过大，可以手动清理历史记录
```

### 3. 备份重要上下文

定期备份上下文文件：

```bash
# 创建备份
cp -r shared/.ai-memory/ shared/.ai-memory.backup.$(date +%Y%m%d)
```

### 4. 团队培训

确保团队成员了解：
- 提交信息规范（影响变更类型识别）
- 如何查看和利用上下文信息
- 故障时的排查方法

---

## 扩展功能

### 自定义变更类型识别

编辑 `shared/scripts/auto-update-context.js` 中的 `analyzeChangeType` 函数：

```javascript
function analyzeChangeType(commitMessage, changedFiles) {
  const types = [];
  
  // 添加自定义规则
  if (commitMessage.match(/^security/i)) types.push('security');
  if (changedFiles.some(f => f.includes('performance/'))) types.push('performance');
  
  // ... 原有规则
  
  return types;
}
```

### 集成到 CI/CD

在 CI 流水线中添加上下文更新步骤：

```yaml
# .github/workflows/update-context.yml
name: Update AI Context
on:
  push:
    branches: [main, develop]

jobs:
  update-context:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '18'
      - name: Update Context
        run: node shared/scripts/auto-update-context.js
      - name: Commit Changes
        run: |
          git config user.name "GitHub Actions"
          git config user.email "actions@github.com"
          git add shared/.ai-memory/
          git diff --quiet && git diff --staged --quiet || git commit -m "chore: auto-update ai context"
          git push
```

---

## 参考文档

- [Git Hooks 官方文档](https://git-scm.com/book/en/v2/Customizing-Git-Git-Hooks)
- [Husky 文档](https://typicode.github.io/husky/)
- [团队协作规范实施评估报告](../团队协作规范实施评估报告.md)

---

**文档版本**: v1.0  
**最后更新**: 2026-03-26  
**维护者**: DevOps 团队
