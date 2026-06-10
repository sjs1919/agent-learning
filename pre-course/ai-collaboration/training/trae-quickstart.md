# Trae 工作区与项目创建快速指南

> **目标**：让开发者在 Trae 中快速完成工作区搭建和项目创建
> **适用**：有编程基础的开发者

---

## 一、工作区创建（5分钟）

### 第一步：打开 Trae 创建工作区

```
1. 打开 Trae IDE
2. 点击 "Open Folder" 或 Ctrl+O
3. 选择/创建工作区根目录（如 e:\workspace\myworkspace）
```

### 第二步：让 Trae 初始化基础结构

**复制以下提示词给 Trae**：

```
请帮我初始化工作区，创建以下基础结构：

1. 创建目录：
   - projects/     # 项目目录
   - shared/       # 共享资源
   - shared/docs/  # 团队文档
   - shared/scripts/  # 共享脚本
   - libs/         # 公共库

2. 在根目录创建：
   - README.md     # 工作区说明
   - .gitignore    # Git忽略配置
   - AGENTS.md     # 团队协作规范（Trae规则）

3. 在 .trae/ 目录创建：
   - workspace-rules.md  # 工作区规则

完成后请列出创建的文件清单。
```

### 第三步：配置工作区规则（可选）

```
请帮我配置 .trae/workspace-rules.md，包含：
1. 项目列表（当前有哪些项目）
2. 技术栈规范
3. Git提交规范
4. 敏感信息处理规则
```

---

## 二、项目创建（10分钟/项目）

### 通用项目结构

大多数项目采用以下结构：

```
projects/MyProject/
├── frontend/          # 前端代码（如果有）
├── backend/           # 后端代码（如果有）
├── docs/              # 项目文档
│   ├── prd/          # 产品需求文档
│   └── api/          # API文档
├── db/                # 数据库脚本
├── tests/             # 测试代码
├── .trae/             # 项目规则
│   └── project-rules.md
├── .env.example       # 环境变量模板
├── .gitignore
└── README.md
```

### 创建新项目提示词模板

**复制以下提示词给 Trae**：

```
请帮我在 projects/ 目录下创建新项目 [项目名称]：

## 项目信息
- 项目名称：[名称]
- 项目类型：[前端/后端/全栈]
- 技术栈：[如 Vue 3 + PHP 8]

## 需要创建的内容

1. **目录结构**：
   - frontend/ 或 backend/ 或两者都有
   - docs/prd/、docs/api/
   - db/
   - tests/

2. **配置文件**：
   - .gitignore
   - README.md
   - .env.example（数据库配置、API密钥等占位符）

3. **项目规则**（.trae/project-rules.md）：
   - 技术栈说明
   - 代码规范要点
   - API规范
   - 命名规范

4. **AGENTS.md**：
   - 项目简介
   - 开发流程
   - 团队规范

请创建完成后，显示完整的项目结构。
```

---

## 三、已有项目导入 Trae

如果已有项目，只需添加 Trae 配置：

```
请帮我在当前项目配置 Trae：

1. 创建 .trae/project-rules.md（项目规则）
2. 创建 AGENTS.md（项目上下文）
3. 检查并完善 .gitignore
4. 创建 .env.example（如果不存在）

请先查看项目现有结构。
```

---

## 四、真实项目导入示例

### 4.1 SpDataView 项目导入

**项目信息**：

| 属性 | 说明 |
|------|------|
| 项目名称 | SpDataView |
| 项目类型 | 全栈项目（前端+后端+数据库） |
| 技术栈 | Vue 3 + PHP 8 原生 + MySQL |
| 项目描述 | 逾期回款数据看板 |

**项目结构**：
```
SpDataView/
├── frontend/          # Vue 3 前端
├── backend/           # PHP 8 API
│   ├── api/          # API接口
│   ├── utils/        # 工具类
│   └── index.php     # 入口文件
├── db/                # 数据库脚本
├── docs/              # 产品文档
├── tests/             # 测试代码
├── .env.example       # 环境变量模板
├── .gitignore
├── commitlint.config.js
└── package.json
```

**导入 Trae 步骤**：

```
请帮我在 SpDataView 项目中完善 Trae 配置：

1. 创建 .trae/project-rules.md，内容包含：
   - 技术栈：Vue 3 + PHP 8 + MySQL
   - API接口规范（RESTful，统一响应格式）
   - 数据库表结构说明
   - 代码规范（PSR-12 for PHP）

2. 创建 AGENTS.md，内容包含：
   - 项目简介：逾期回款数据看板
   - 开发流程：PRD → 原型 → 前端 → 后端 → 测试
   - 启动说明：
     * 前端：cd frontend && npm install && npm run dev
     * 后端：cd backend && php -S localhost:8000

3. 更新 .gitignore（如果需要）

4. 更新 README.md（补充启动说明）

完成后请显示项目结构。
```

**启动命令**：
```bash
# 前端启动
cd projects/SpDataView/frontend
npm install
npm run dev

# 后端启动（另一个终端）
cd projects/SpDataView/backend
php -S localhost:8000
```

---

### 4.2 vbenData 项目导入

**项目信息**：

| 属性 | 说明 |
|------|------|
| 项目名称 | vbenData |
| 项目类型 | 前端项目（Vue 3 企业管理后台） |
| 技术栈 | Vue 3 + TypeScript + Vite + VbenAdmin |
| 项目描述 | Vue3 企业管理后台 |

**项目结构**：
```
vbenData/
├── apps/              # 应用入口
├── src/               # Vue 组件源码
│   ├── views/        # 页面视图
│   ├── api/          # API接口
│   ├── components/   # 公共组件
│   └── store/        # 状态管理
├── packages/          # 公共包
├── mock/             # Mock数据
├── types/            # TypeScript类型
├── internal/         # 内部工具包
├── public/           # 静态资源
├── .env.example      # 环境变量模板
├── .eslintrc.cjs     # ESLint配置
├── vite.config.ts    # Vite配置
└── package.json
```

**导入 Trae 步骤**：

```
请帮我在 vbenData 项目中完善 Trae 配置：

1. 创建 .trae/project-rules.md，内容包含：
   - 技术栈：Vue 3 + TypeScript + Vite + VbenAdmin
   - 代码规范：ESLint + Prettier
   - 命名规范：PascalCase（组件）、camelCase（函数）
   - API调用规范

2. 创建 AGENTS.md，内容包含：
   - 项目简介：Vue3 企业管理后台
   - 目录结构说明
   - 启动说明：pnpm install && pnpm dev
   - 构建说明：pnpm build

3. 检查并更新 .gitignore

4. 更新 README.md（补充中文说明）

完成后请显示项目结构。
```

**启动命令**：
```bash
cd projects/vbenData
pnpm install
pnpm dev
```

---

### 4.3 批量导入多个项目

如果工作区有多个项目需要导入：

```
请帮我在工作区批量配置 Trae：

项目列表：
1. SpDataView - 全栈项目（Vue3 + PHP + MySQL）
2. vbenData - 前端项目（Vue3 + VbenAdmin）

为每个项目：
1. 创建 .trae/project-rules.md
2. 创建 AGENTS.md
3. 检查 .gitignore
4. 完善 README.md

同时更新工作区根目录的：
- shared/projects.json（项目索引）
- .trae/workspace-rules.md（工作区规则）
```

---

## 四、常用操作提示词速查

### 1. 查看项目结构
```
请查看当前项目结构，列出主要目录和文件。
```

### 2. 添加新功能模块
```
请帮我在 [模块名] 下添加 [功能名]：

需要创建：
1. [文件1]
2. [文件2]

参考现有代码风格：[参考文件路径]
```

### 3. 创建数据库表
```
请帮我创建数据库表结构：

表名：[名称]
字段：
- id: 主键
- name: 名称
- ...

保存在 db/ 目录下。
```

### 4. 生成单元测试
```
请为以下文件生成单元测试：
[文件路径]

使用测试框架：[框架名称]
```

### 5. 整理 Git 提交
```
请帮我整理本次修改的提交列表：
1. 查看当前 git status
2. 按模块分组
3. 生成提交信息
```

---

## 五、项目类型快速模板

### 前端项目（Vue 3）
```
请创建 Vue 3 前端项目：

项目路径：projects/[名称]
技术栈：Vue 3 + TypeScript + Vite

需要：
1. package.json（Vite + Vue 3 依赖）
2. vite.config.ts
3. tsconfig.json
4. 目录结构（src/api, src/components, src/views 等）
5. .gitignore
6. .env.example
7. .trae/project-rules.md
```

### 后端项目（PHP）
```
请创建 PHP 后端项目：

项目路径：projects/[名称]/backend
技术栈：PHP 8 + 原生/框架

需要：
1. 目录结构（app/, routes/, config/）
2. composer.json
3. .env.example
4. .gitignore
5. API 入口文件
6. .trae/project-rules.md
```

### 全栈项目
```
请创建全栈项目：

项目路径：projects/[名称]
前端：Vue 3 + TypeScript
后端：PHP 8 + MySQL

需要：
1. frontend/ 前端目录
2. backend/ 后端目录
3. docs/ 文档目录
4. db/ 数据库脚本
5. .trae/project-rules.md
```

---

## 六、常见问题

### Q1：Trae 不识别项目结构？
**A**：确保在项目根目录打开 Trae，并创建 `.trae/project-rules.md`

### Q2：提示词不够具体？
**A**：添加具体信息：
- 具体文件路径
- 技术栈版本
- 参考现有代码

### Q3：如何让 Trae 保持上下文？
**A**：在同一对话中连续操作，Trae 会记住之前的上下文

---

## 八、文件清单

工作区创建后应包含：

```
workspace/
├── projects/                 # 项目目录
│   └── (你的项目)
├── shared/                   # 共享资源
│   ├── docs/
│   └── scripts/
├── libs/                     # 公共库
├── .trae/
│   └── workspace-rules.md
├── README.md
├── AGENTS.md
└── .gitignore
```

项目创建后应包含：

```
projects/MyProject/
├── frontend/ 或 backend/
├── docs/
├── db/
├── tests/
├── .trae/
│   └── project-rules.md
├── .env.example
├── .gitignore
├── README.md
└── AGENTS.md
```

---

*简化版指南 - 专注实操*
