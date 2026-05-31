# Agentic Code — 面向 AI 编码代理的 AGENTS.md 工作流框架

为 Cursor、Codex、Gemini CLI 以及任何兼容 AGENTS.md 的工具提供的 Agentic 编码工作流。零配置、测试优先、语言无关。

[![MIT 许可证](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)
[![AGENTS.md](https://img.shields.io/badge/AGENTS.md-compliant-blue.svg)](https://agents.md)
[![版本](https://img.shields.io/badge/version-0.6.7-blue.svg)](package.json)

![演示：使用 Agentic Code 构建 Slack 机器人](.github/assets/demo.gif)

*AI 在 30 秒内构建一个带测试和文档的 Slack 机器人*

## Agentic Code 如何工作

```
你：“用 Gemini API 构建一个 Slack 机器人”
AI： ✓ 读取 AGENTS.md
     ✓ 分析需求
     ✓ 规划架构
     ✓ 首先编写测试
     ✓ 以最佳实践实现
     ✓ 验证一切正常
```

**开箱即用——无需配置。**

## 快速入门 (30 秒)

```bash
npx agentic-code my-project && cd my-project
# 准备就绪
```

就是这样。它适用于 **Cursor、Codex、Gemini CLI** 以及[许多兼容 AGENTS.md 的工具](https://agents.md)。

## 兼容工具

| 工具 | 支持 | 说明 |
|------|---------|-------|
| [Cursor](https://cursor.com) | 主要 | 通过 `npx agentic-code skills --cursor` 安装技能 |
| [Codex](https://github.com/openai/codex) | 主要 | 通过 `npx agentic-code skills --codex` 安装技能 |
| [Gemini CLI](https://github.com/google-gemini/gemini-cli) | 主要 | 自动读取 AGENTS.md |
| 任何 AGENTS.md 工具 | 兼容 | [在 agents.md 查看完整列表](https://agents.md) |

## 使用 Codex？

如果你主要使用 Codex，请参阅 [codex-workflows](https://github.com/shinpr/codex-workflows) 以获得更针对 Codex 的设置。

它包括基于子代理的审查和验证任务隔离，通过在同一工作会话内分离上下文来提高可靠性。

## AI 编码代理的问题

每个 AI 编码工具都有同样的问题：
- 在 10 条消息后忘记你的项目结构
- 添加功能时删除测试
- 忽略架构决策
- 跳过质量检查

**我们将解决方案构建到了框架中。** AGENTS.md 会自动引导你的 AI 完成专业工作流。

## 特性

### 🎯 **零配置**
无需设置即可使用的预构建工作流。

### 🌐 **通用兼容性**
适用于任何编程语言和任何读取 AGENTS.md 的 AI 工具。

### ✅ **默认为测试优先**
在编写实现代码之前生成测试骨架。

### 📈 **智能扩展**
- 简单任务 → 直接执行
- 复杂特性 → 带有批准环节的完整工作流

## 架构：任务、工作流和 skills

1.  **AGENTS.md 告诉你的 AI 流程** - 就像一个为 AI 代理准备的 README
2.  **渐进式规则加载** - 只在需要时加载所需内容
3.  **质量关卡** - 自动检查点确保输出一致
4.  **你保持控制** - 重大决策的批准点

```
.agents/
├── tasks/                   # 构建什么
│   ├── task-analysis.md     # 入口点 - AI 从这里开始
│   └── ...                  # 设计、测试、实现、QA 任务
├── workflows/               # 如何构建
└── skills/                  # 质量标准 (兼容 Codex)
```

## 真实示例

### 简单任务
```bash
你：“为用户搜索添加 API 端点”
# AI: 读取现有代码 → 规划变更 → 测试 → 实现 → 完成
```

### 复杂特性
```bash
你：“构建用户认证系统”
# AI: 需求 → 设计文档 → 你的批准 → 测试骨架 →
#     实现 → 质量检查 → 完成
```

## 安装

### 对于新项目
```bash
npx agentic-code my-project
```

### 对于现有项目
```bash
# 复制框架文件
cp -r path/to/agentic-code/AGENTS.md .
cp -r path/to/agentic-code/.agents .
```

### Skills

`.agents/skills/` 包含可重用的 [Codex skill 格式](https://github.com/openai/codex/blob/main/docs/skills.md) 的 skill 文件。每个 skill 都有一个 `SKILL.md`，其中包含 AI 代理可以发现和应用的指令。

**Cursor**: 为 Cursor 编辑器安装 skills：

```bash
# 用户范围 (所有项目)
npx agentic-code skills --cursor
# 安装到 ~/.cursor/skills/agentic-code/

# 项目范围 (仅当前项目)
npx agentic-code skills --cursor --project
# 安装到 ./.cursor/skills/agentic-code/
```

> 注意：Cursor 中的 skills 功能需要 Nightly 发布通道。

**Codex**: 为 Codex CLI 安装 skills：

```bash
# 用户范围 (所有项目)
npx agentic-code skills --codex
# 安装到 ~/.codex/skills/agentic-code/

# 项目范围 (仅当前项目)
npx agentic-code skills --codex --project
# 安装到 ./.codex/skills/agentic-code/
```

**自定义路径**:

```bash
npx agentic-code skills --path ./custom/skills
# 安装到 ./custom/skills/agentic-code/
```

## 常见问题解答

**问：什么是 AGENTS.md？**
答：AGENTS.md 是一个[开放的社区标准](https://agents.md)，它告诉 AI 编码代理如何在你的项目中工作。它定义了任务、工作流和质量标准，以便任何兼容的工具都能遵循一致的开发过程。

**问：Agentic 编码与 AI 代码补全有何不同？**
答：AI 代码补全建议下一行代码。Agentic 编码为 AI 提供了整个工作流——需求分析、架构规划、测试优先开发和质量检查——因此它能像专业开发人员一样构建功能。

**问：我可以将此框架与其他 AI 编码工具一起使用吗？**
答：可以！该框架适用于任何兼容 AGENTS.md 的工具，如 Cursor、Codex、Gemini CLI 以及其他 LLM 辅助的开发环境。

**问：支持哪些编程语言？**
答：该框架与语言无关，通过通用的开发原则适用于任何编程语言。`skills/*/references/typescript.md` 中提供了特定于 TypeScript 的规则。

**问：我需要学习新的语法吗？**  
答：不需要。用自然语言描述你想要什么；框架会处理剩下的事情。

**问：如果我的 AI 不支持 AGENTS.md 怎么办？**  
答：检查你的工具是否[兼容 AGENTS.md](https://agents.md)。如果兼容，首先将它指向 AGENTS.md 文件。

**问：我可以自定义工作流吗？**  
答：可以，`.agents/` 中的所有内容都是可自定义的。默认值是生产就绪的，但你可以根据团队的流程进行调整。

**问：对于我现有的代码库呢？**
答：它适用于现有项目。你的 AI 会分析代码并遵循你已建立的模式。

**问：这适用于本地 LLM 吗？**
答：是的，只要你的 AI 工具能读取 AGENTS.md。该框架与模型无关——它既适用于云 API，也适用于本地模型。

## 技术细节

该框架有三大支柱：

1.  **任务** - 定义要构建 **什么**
2.  **工作流** - 定义 **如何** 构建
3.  **Skills** - 定义质量 **标准**

<details>
<summary>为好奇者准备的高级功能...</summary>

### 渐进式 skills 加载
skills 根据任务分析加载：
- 小型 (1-2 个文件) → 使用最少 skills 直接执行
- 中/大型 (3+ 个文件) → 带有设计文档的结构化工作流
- 每个任务定义都指定了其所需的 skills

### 质量关卡
自动检查点确保：
- 继续前测试通过
- 代码符合标准
- 文档保持更新

### 特殊功能
- **元认知** - AI 自我评估和错误恢复
- **计划注入** - 强制所有必需步骤都在工作计划中
- **测试生成** - 从验收标准生成测试骨架
- **单一提交原则** - 每个任务 = 一个原子提交

</details>

## 审查生成结果

**重要提示：始终在单独的会话中审查 AI 生成的输出。**

LLM 无法在同一上下文中可靠地审查自己的输出。当 AI 生成代码或文档时，它会将相同的假设和盲点带入任何“自我审查”中。这会导致遗漏那些从全新视角本可以发现的问题。

### 为什么单独会话很重要

| 同一会话 | 新会话 |
|--------------|-------------|
| 共享上下文和假设 | 全新视角，无先前偏见 |
| 可能忽略自身错误 | 客观地发现问题 |
| 审查中的“确认偏误” | 独立地应用标准 |

### 如何使用审查任务

在完成实现或文档编写后，开始一个 **新会话** 并请求审查：

```bash
# 对于代码审查
你：“根据 docs/design/auth-design.md 审查 src/auth/ 中的实现”
# AI 加载 code-review 任务 → 对照设计文档进行验证 → 报告发现

# 对于文档审查
你：“将 docs/design/payment-design.md 作为设计文档进行审查”
# AI 加载 technical-document-review 任务 → 检查结构和内容 → 报告差距

# 对于测试审查
你：“审查 tests/integration/auth.test.ts 中的集成测试”
# AI 加载 integration-test-review 任务 → 验证测试质量 → 报告问题
```

### 可用的审查任务

| 任务 | 目标 | 检查内容 |
|------|--------|----------------|
| `code-review` | 实现文件 | 设计文档合规性、代码质量、架构 |
| `technical-document-review` | 设计文档、ADR、PRD | 结构、内容质量、失败场景 |
| `integration-test-review` | 集成/E2E 测试 | 骨架合规性、AAA 结构、模拟边界 |

**专业提示：** 将审查作为你工作流的一部分。在任何重要的生成之后，切换会话并在合并前进行审查。

### 对于 Cursor 用户：通过 MCP 进行隔离上下文审查

Cursor 用户可以使用 [`sub-agents-mcp`](https://github.com/shinpr/sub-agents-mcp) 在隔离的上下文中运行审查，而无需切换会话。当审查作为子代理运行时，它在完全独立的上下文中执行——达到了与切换会话相同的“全新视角”好处，但无需离开你的工作流。

**快速设置：**

将以下内容添加到你的 MCP 配置 (`~/.cursor/mcp.json` 或 `.cursor/mcp.json`)：

```json
{
  "mcpServers": {
    "sub-agents": {
      "command": "npx",
      "args": ["-y", "sub-agents-mcp"],
      "env": {
        "AGENTS_DIR": "/absolute/path/to/your/project/.agents/tasks",
        "AGENT_TYPE": "cursor"
      }
    }
  }
}
```

重启 Cursor 后，任务定义将作为子代理可用：

```bash
你：“使用 code-review 代理根据 docs/design/auth-design.md 审查 src/auth/”
```

## 开始构建

```bash
npx agentic-code my-awesome-project
cd my-awesome-project
# 告诉你的 AI 要构建什么
```

**一致、专业的 AI 辅助开发。**

---

## 贡献

发现了一个 bug？想添加特定语言的规则？欢迎提交 PR！

- 🐛 [报告问题](https://github.com/shinpr/agentic-code/issues)
- 🔧 [提交 PR](https://github.com/shinpr/agentic-code/pulls)
- 📚 [改进文档](https://github.com/shinpr/agentic-code/pulls)

## 许可证

MIT - 你可以随心所欲地使用它。

---

基于 [AGENTS.md 规范](https://agents.md) 构建——一个面向 AI 编码代理的开放社区规范。

**准备好用 AI 进行规范编码了吗？** `npx agentic-code my-project`
ject`
