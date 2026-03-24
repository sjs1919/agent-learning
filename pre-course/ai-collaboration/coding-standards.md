# 团队编码规范

## 📐 代码风格

### TypeScript/JavaScript

```typescript
// ✅ Good
interface User {
  id: number
  name: string
  email: string
}

function getUserById(id: number): Promise<User> {
  return fetch(`/api/users/${id}`).then(res => res.json())
}

// ❌ Bad
function get_user_by_id(id: number) {
  return fetch('/api/users/' + id).then(function(res) { return res.json() })
}
```

### Vue 组件规范

```vue
<script setup lang="ts">
// 1. 类型导入
import type { User } from '@/types'

// 2. Vue 导入
import { ref, computed, onMounted } from 'vue'

// 3. 组件导入
import UserCard from './UserCard.vue'

// 4. 工具导入
import { formatDate } from '@/utils/format'

// 5. Props 定义
interface Props {
  userId: number
  editable?: boolean
}

const props = withDefaults(defineProps<Props>(), {
  editable: false,
})

// 6. Emits 定义
const emit = defineEmits<{
  (e: 'update', user: User): void
  (e: 'delete', id: number): void
}>()

// 7. 状态定义
const user = ref<User | null>(null)
const loading = ref(false)

// 8. 计算属性
const displayName = computed(() => {
  return user.value?.name ?? 'Unknown'
})

// 9. 方法定义
async function fetchUser() {
  loading.value = true
  try {
    user.value = await getUserById(props.userId)
  } finally {
    loading.value = false
  }
}

// 10. 生命周期
onMounted(fetchUser)
</script>

<template>
  <div class="user-profile">
    <UserCard v-if="user" :user="user" />
    <p v-else-if="loading">Loading...</p>
    <p v-else>User not found</p>
  </div>
</template>
```

## 🧪 单元测试规范

### 每个函数必须有单元测试

```typescript
// src/utils/calculate.ts
export function calculateTotal(
  items: { price: number; quantity: number }[],
  discount: number = 0
): number {
  if (!items || items.length === 0) return 0
  if (discount < 0 || discount > 1) throw new Error('Invalid discount')
  
  const subtotal = items.reduce((sum, item) => 
    sum + item.price * item.quantity, 0
  )
  return subtotal * (1 - discount)
}

// tests/unit/utils/calculate.test.ts
import { describe, it, expect } from 'vitest'
import { calculateTotal } from '@/utils/calculate'

describe('calculateTotal', () => {
  it('应正确计算总价', () => {
    const items = [
      { price: 100, quantity: 2 },
      { price: 50, quantity: 1 },
    ]
    expect(calculateTotal(items)).toBe(250)
  })

  it('应正确处理折扣', () => {
    const items = [{ price: 100, quantity: 1 }]
    expect(calculateTotal(items, 0.2)).toBe(80)
  })

  it('应处理空数组', () => {
    expect(calculateTotal([])).toBe(0)
  })

  it('应处理 null', () => {
    expect(calculateTotal(null as any)).toBe(0)
  })

  it('应拒绝无效折扣', () => {
    expect(() => calculateTotal([], -0.1)).toThrow('Invalid discount')
    expect(() => calculateTotal([], 1.1)).toThrow('Invalid discount')
  })
})
```

### 测试覆盖率要求

- **语句覆盖率 (Statements)**: ≥ 80%
- **分支覆盖率 (Branches)**: ≥ 80%
- **函数覆盖率 (Functions)**: ≥ 80%
- **行覆盖率 (Lines)**: ≥ 80%

## 🔒 安全规范

### 敏感信息处理

```typescript
// ✅ Good - 使用环境变量
const apiKey = import.meta.env.VITE_API_KEY

// ❌ Bad - 硬编码
const apiKey = 'sk-1234567890abcdef'
```

### 输入验证

```typescript
// ✅ Good
function createUser(input: unknown) {
  const schema = z.object({
    email: z.string().email(),
    name: z.string().min(2).max(50),
    age: z.number().int().min(0).max(150),
  })
  
  const data = schema.parse(input)
  // 继续处理...
}

// ❌ Bad
function createUser(input: any) {
  db.users.create(input) // 直接写入，无验证
}
```

## 📋 命名规范

| 类型 | 规范 | 示例 |
|-----|------|------|
| 组件 | PascalCase | `UserProfile.vue` |
| 函数 | camelCase | `getUserById()` |
| 常量 | UPPER_SNAKE_CASE | `MAX_RETRY_COUNT` |
| 类型/接口 | PascalCase | `User`, `UserProps` |
| 文件 | camelCase/kebab-case | `userUtils.ts`, `user-profile.vue` |
| 布尔变量 | 前缀 is/has/should | `isLoading`, `hasError` |

## 📚 文档注释

```typescript
/**
 * 计算订单总价
 * 
 * @param items - 订单商品列表
 * @param discount - 折扣比例 (0-1)
 * @returns 折后总价
 * @throws {Error} 当折扣无效时抛出
 * 
 * @example
 * ```ts
 * const total = calculateTotal([
 *   { price: 100, quantity: 2 }
 * ], 0.1)
 * // returns 180
 * ```
 */
function calculateTotal(
  items: OrderItem[],
  discount: number
): number {
  // 实现...
}
```

## 🔄 提交前检查清单

- [ ] 所有单元测试通过
- [ ] 测试覆盖率 ≥ 80%
- [ ] ESLint 无错误
- [ ] TypeScript 类型检查通过
- [ ] 代码已格式化
- [ ] 没有硬编码敏感信息
- [ ] 新功能有对应测试
- [ ] 文档已更新（如需要）
