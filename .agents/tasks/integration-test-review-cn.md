# 集成测试审查

## 必要 skills [强制性 - 必须激活]

**skill 可用性验证：**
1. [如果未激活则加载] `.agents/skills/testing/SKILL.md`
2. [如果未激活则加载] `.agents/skills/integration-e2e-testing/SKILL.md`

**加载协议：**
- 步骤 1: 检查 `testing/SKILL.md` 在工作内存中是否为激活状态
- 步骤 2: 如果 `testing/SKILL.md` 未激活 → 执行 **阻塞性读取 (BLOCKING READ)**
- 步骤 3: 检查 `integration-e2e-testing/SKILL.md` 在工作内存中是否为激活状态
- 步骤 4: 如果 `integration-e2e-testing/SKILL.md` 未激活 → 执行 **阻塞性读取**
- 步骤 5: 在继续前 **确认** 所有必需的 skills 都已激活

## 目的

验证集成测试和端到端（E2E）测试的实现质量。评估骨架注释（AC、行为、元数据）与实际测试实现之间的一致性。

## 使用时机

- 在集成/E2E测试实现完成之后
- 在质量保证阶段之前
- 当测试骨架已转换为实际的测试实现时

## 所需信息

- **testFile**：要审查的测试文件的路径 (必需)
- **designDocPath**：相关设计文档的路径 (可选)

## 完成条件

□ 所有骨架注释都已与实现进行验证
□ 所有可观察结果都有行为验证断言
□ 所有测试用例中都确认了 AAA 结构
□ Mock 边界适当 (外部=Mock，内部=实际)
□ 已验证测试独立性 (无共享的可变状态)
□ 已识别并记录质量问题

## 审查流程

### 阶段 1: 骨架注释提取

从测试文件中提取以下内容：
- `AC:` - 验收标准参考
- `ROI:` - 投资回报率分数和值
- `Behavior:` - 触发器 → 过程 → 可观察结果
- `@category:` - 测试类别
- `@dependency:` - 组件依赖
- `@complexity:` - 复杂度级别

### 阶段 2: 骨架与实现一致性检查

对每个测试用例进行验证：

| 检查项 | 验证内容 | 失败条件 |
|------------|---------------------|-------------------|
| AC 对应性 | 为 AC 注释实现了测试 | 仍有待定/待办标记 |
| 行为验证 | “可观察结果”存在断言 | 无断言 |
| 验证项覆盖 | 所有列出的项都包含在断言中 | 项目缺失 |

### 阶段 3: 实现质量检查

| 检查项 | 验证内容 | 失败条件 |
|------------|---------------------|-------------------|
| AAA 结构 | Arrange/Act/Assert (准备/行动/断言) 的分离清晰 | 分离不清晰 |
| 独立性 | 测试之间无状态共享 | 修改了共享状态 |
| 可复现性 | 未直接使用当前时间或随机值 | 存在非确定性元素 |
| 可读性 | 测试名称与验证内容匹配 | 名称与内容不一致 |

### 阶段 4: Mock 边界检查 (仅限集成测试)

| 判断标准 | 预期状态 | 失败条件 |
|-------------------|----------------|-------------------|
| 外部 API | 需要 Mock | 实际的 HTTP 通信 |
| 内部组件 | 使用实际对象 | 不必要的 Mock |

## 输出格式

### 状态确定

**approved (已批准)**:
- 所有 AC 都有已实现的测试 (无待定/待办标记)
- 所有可观察结果都有断言
- 无质量问题或仅有低优先级问题

**needs_revision (需要修订)**:
- 仍有待定/待办标记
- 行为验证缺失
- 存在中到高优先级的质量问题

**blocked (阻塞)**:
- 未找到骨架文件
- AC 意图不明确
- 设计文档与实现之间存在重大矛盾

### 报告结构

```
[审查结果]
status: approved | needs_revision | blocked
testFile: [路径]
summary: [结果摘要]

[骨架合规性]
totalACs: [总数]
implementedACs: [已实现数]
pendingTodos: [待办数]
missingAssertions: [如有，则列出]

[质量问题]
- severity: high | medium | low
  category: [aaa_structure | independence | reproducibility | mock_boundary | readability]
  location: [文件:行号]
  description: [问题描述]
  suggestion: [修复建议]

[结论]
decision: approved | needs_revision | blocked
reason: [决策理由]
prioritizedActions:
  1. [最高优先级修复]
  2. [下一个修复]
```

## 要避免的反模式

- 批准仍有待定/待办标记的测试
- 忽略可观察结果的断言缺失
- 忽视测试之间共享的可变状态
- 接受对内部组件的过度 Mock
- 跳过 AAA 结构验证
