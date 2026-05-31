# TypeScript 特定规则

## 类型安全

**原则**: 使用 `unknown` + 类型守卫 (type guards) 代替 `any` 以实现完全的类型安全。

**`any` 类型的替代方案 (优先级顺序)**
1. **`unknown` 类型 + 类型守卫**
2. **泛型 (Generics)**
3. **联合类型 (Union Types)・交叉类型 (Intersection Types)**
4. **类型断言 (Type Assertions) (最后手段)**: 仅在类型确定无疑时使用

**类型守卫实现模式**
```typescript
// 安全地验证外部输入
function isUser(value: unknown): value is User {
  return typeof value === 'object' && value !== null &&
    'id' in value && 'name' in value
}
// 用法: if (isUser(data)) { /* 此处 data 被类型化为 User */ }
```

**现代类型特性**
- **`satisfies` 操作符**: 在保留类型推断的同时进行类型检查
  ```typescript
  const config = { port: 3000 } satisfies Config  // ✅ 保留了推断
  const config: Config = { port: 3000 }           // ❌ 失去了推断
  ```
- **`const` 断言**: 使用字面量类型确保不可变性
  ```typescript
  const ROUTES = { HOME: '/' } as const satisfies Routes  // ✅ 不可变且类型安全
  ```
- **品牌类型 (Branded Types)**: 为相同的原始类型区分含义
  ```typescript
  type UserId = string & { __brand: 'UserId' }
  type OrderId = string & { __brand: 'OrderId' }
  // UserId 和 OrderId 不兼容 - 防止混用
  ```
- **模板字面量类型 (Template Literal Types)**: 用类型表示字符串模式
  ```typescript
  type Route = `/${string}`
  type HttpMethod = 'GET' | 'POST'
  type Endpoint = `${HttpMethod} ${Route}`
  ```

**实现中的类型安全**
- API 通信: 始终将响应接收为 `unknown`，并使用类型守卫进行验证
- 表单输入: 外部输入为 `unknown`，验证后确定类型
- 遗留系统集成: 逐步断言，如 `window as unknown as LegacyWindow`
- 测试代码: 始终为模拟对象定义类型，利用 `Partial<T>` 和 `vi.fn<[Args], Return>()`

**数据流中的类型安全**
输入层 (`unknown`) → 类型守卫 → 业务层 (类型保证) → 输出层 (序列化)

**类型复杂度管理**
- 字段数量: 最多 20 个 (如果超过则按职责拆分，外部 API 类型是例外)
- 可选字段比例: 最多 30% (如果超过则分离必需/可选字段)
- 嵌套深度: 最多 3 层 (如果超过则扁平化)
- 类型断言: 如果使用超过 3 次，则审查设计
- **外部 API 类型**: 放宽约束并根据实际情况定义 (在内部进行适当转换)

## 编码约定

**类 (Class) 的使用标准**
- **推荐: 使用函数和接口实现**
  - 理由: 提高函数组合的可测试性和灵活性
- **允许使用类的情况**:
  - 框架要求 (NestJS Controller/Service, TypeORM Entity 等)
  - 自定义错误类的定义
  - 当状态和业务逻辑紧密耦合时 (例如，购物车、会话、状态机)
- **决策标准**: 如果“这个数据是否有行为？”的答案是肯定的，则考虑使用类
  ```typescript
  // ✅ 函数和接口
  interface UserService { create(data: UserData): User }
  const userService: UserService = { create: (data) => {...} }
  // ❌ 不必要的类
  class UserService { create(data: UserData) {...} }
  ```

**函数设计**
```typescript
// ✅ 对象参数
function createUser({ name, email, role }: CreateUserParams) {}
// ❌ 多个参数
function createUser(name: string, email: string, role: string) {}
```

**依赖注入**
```typescript
// ✅ 作为参数接收依赖
function createService(repository: Repository) { return {...} }
// ❌ 直接导入依赖
import { userRepository } from './infrastructure/repository'
```

**异步处理**
- Promise 处理: 始终使用 `async/await`
- 错误处理: 始终使用 `try-catch` 处理
- 类型定义: 明确定义返回值类型 (例如, `Promise<Result>`)

**格式规则**
- 省略分号 (遵循 Biome 设置)
- 类型使用 `PascalCase`，变量/函数使用 `camelCase`
- 导入使用绝对路径 (`src/`)

## 错误处理

**`Result` 类型模式**: 用类型表示错误以进行显式处理
```typescript
type Result<T, E> = { ok: true; value: T } | { ok: false; error: E }

// 示例: 用类型表示错误的可能性
function parseUser(data: unknown): Result<User, ValidationError> {
  if (!isValid(data)) return { ok: false, error: new ValidationError() }
  return { ok: true, value: data as User }
}
```

**自定义错误类**
```typescript
export class AppError extends Error {
  constructor(message: string, public readonly code: string, public readonly statusCode = 500) {
    super(message)
    this.name = this.constructor.name
  }
}
// 特定用途: ValidationError(400), BusinessRuleError(400), DatabaseError(500), ExternalServiceError(502)
```

**异步错误处理**
- 必须设置全局处理器: `unhandledRejection`, `uncaughtException`
- 所有 `async/await` 都使用 `try-catch`
- 始终记录并重新抛出错误

## 重构优先级

重复代码移除 > 大型函数拆分 > 复杂条件分支简化 > **类型安全改进**

## 性能优化

- 流式处理: 使用流处理大型数据集
- 防止内存泄漏: 显式释放不必要的对象
