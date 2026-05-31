# TypeScript/Vitest 测试规则

## 测试框架
- **Vitest**: 本项目使用 Vitest
- 测试导入: `import { describe, it, expect, beforeEach, vi } from 'vitest'`
- Mock 创建: 使用 `vi.mock()`

## 目录结构

**文件结构:**
- `src/application/services/service.ts`: 主要服务文件
- `src/application/services/__tests__/service.test.ts`: 单元测试
- `src/application/services/__tests__/service.int.test.ts`: 集成测试

**命名约定:**
- 测试文件: `{target-file-name}.test.ts`
- 集成测试文件: `{target-file-name}.int.test.ts`

## 跨功能 E2E 测试模式

```typescript
describe('跨功能 E2E 测试', () => {
  // 模式 1: 基线 → 变更 → 验证
  it('在新功能加入后应保持现有行为', async () => {
    // 1. 捕获基线
    const baseline = await testExistingFeature()

    // 2. 启用新功能
    await enableNewFeature()

    // 3. 验证连续性
    const result = await testExistingFeature()
    expect(result).toEqual(baseline)
    expect(result.responseTime).toBeLessThan(
      baseline.responseTime * 1.2 // 项目特定的阈值
    )
  })

  // 模式 2: 跨功能的数据完整性
  it('应保持数据完整性', async () => {
    const data = await createTestData()
    await newFeatureOperation(data.id)

    const retrieved = await existingFeatureGet(data.id)
    expect(retrieved).toEqual(data) // 没有意外的突变
  })
})
```

**注意**: LLM 的输出自然会有所不同 - 测试行为，而不是精确匹配

## 测试辅助工具使用示例

```typescript
// ✅ 推荐: 利用构建者模式
const testData = new TestDataBuilder()
  .withDefaults()
  .withName('Test User')
  .build()

// ✅ 推荐: 自定义断言
function assertValidUser(user: unknown): asserts user is User {
  // 验证逻辑
}

// ❌ 避免: 单独实现重复的复杂模拟
```

## 测试粒度示例

```typescript
// ✅ 测试可观察的行为
expect(calculatePrice(100, 0.1)).toBe(110)

// ❌ 测试实现细节 (如使用 any 访问)
expect((calculator as any).taxRate).toBe(0.1)
expect((service as any).validate(input)).toBe(true)
```

## Mock 类型安全强制

### 最小类型定义要求
```typescript
// ✅ 只包含必要部分
type TestRepo = Pick<Repository, 'find' | 'save'>
const mock: TestRepo = { find: vi.fn(), save: vi.fn() }

// 仅在绝对必要时使用，并附有明确理由
const sdkMock = {
  call: vi.fn()
} as unknown as ExternalSDK // 复杂的外部 SDK 类型结构
```

## 基本 Vitest 示例

```typescript
import { describe, it, expect, beforeEach, vi } from 'vitest'

// Mock 设置示例
vi.mock('./userService', () => ({
  getUserById: vi.fn(),
  updateUser: vi.fn()
}))

describe('ComponentName', () => {
  it('应遵循 AAA 模式', () => {
    // Arrange (准备)
    const input = 'test'

    // Act (行动)
    const result = someFunction(input)

    // Assert (断言)
    expect(result).toBe('expected')
  })
})
```

## 质量检查命令 [验证 (VERIFY) 阶段强制执行]

**所有 TypeScript/JavaScript 命令在任务完成前必须以 0 错误通过：**

```bash
npm test              # 必须通过所有测试
npm run build         # 必须成功构建
npm run lint          # 必须有 0 个 lint 错误
npm run type-check    # 必须有 0 个类型错误
```

**强制执行：**
- 运行上面列出的所有适用命令
- 在将任务标记为完成之前修复任何错误或警告
- 如果 `package.json` 中不存在该命令，则跳过该特定命令
- 在任务完成时记录运行了哪些命令
