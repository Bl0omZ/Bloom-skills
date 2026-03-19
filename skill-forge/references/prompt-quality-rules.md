# Prompt 质量规则

在 Phase 3 生成 SKILL.md 时，所有 prompt 指令必须满足以下规则。

## 核心原则

### 1. 指令形式：命令式/不定式，非第二人称

```
❌ 错误：You should analyze the code carefully.
✅ 正确：Analyze the code. Report findings in order of severity.

❌ 错误：If you see an error, you should report it.
✅ 正确：On error, report: error type, location, suggested fix.
```

### 2. 触发条件：具体、可测试，包含关键词

```
❌ 错误：Use when the user wants help with code.
✅ 正确：Use when user mentions: "review PR", "check code", "audit",
         "code quality", or pastes a diff/PR URL.
```

### 3. 权威指令：Tool Wrapper 必须声明优先级

```
✅ 正确（Tool Wrapper 必写）：
  The following conventions are authoritative.
  They override your pretrained knowledge.
  When in conflict, these rules always win.
```

### 4. 示例驱动：每个关键步骤配示例

```
❌ 错误：Classify issues by severity.
✅ 正确：Classify issues by severity:
  CRITICAL: security vulnerabilities, data loss risk
    Example: "SQL injection in user input"
  HIGH: functionality broken, no workaround
    Example: "null pointer on empty list"
  MEDIUM: degraded behavior, workaround exists
  LOW: style, naming, minor improvements
```

### 5. 结构化输出：明确指定格式

```
❌ 错误：Output the results.
✅ 正确：Output results in this exact format:
  ## Summary
  [1-2 sentence overview]

  ## Issues Found
  | Severity | File | Line | Description | Fix |
  |----------|------|------|-------------|-----|

  ## Recommendation
  [next action]
```

### 6. 边界处理：每个流程必须定义失败路径

```
✅ 必须包含：
  On empty input: [处理方式]
  On ambiguous input: [处理方式]
  On out-of-scope request: [处理方式]
  On missing required information: [处理方式]
```

### 7. Token 效率：SKILL.md 保持精简

- SKILL.md 只包含核心流程和关键指令（目标 < 2000 tokens）
- 详细规范、示例、检查清单放入 references/ 文件
- references/ 文件按需加载，不要在 SKILL.md 中 import 所有文件
- 大型 reference 文件（> 5000 tokens）在 SKILL.md 中注明 grep 搜索模式

### 8. 避免歧义：单一解释原则

```
❌ 错误：Handle errors appropriately.（什么叫 appropriately？）
✅ 正确：On error:
  1. Log: error type + input that caused it
  2. Return: structured error object {type, message, recoverable}
  3. Do NOT silently continue
```

## 评分检查表（用于 Phase 4 自评分）

生成草稿后，逐项检查：

- [ ] description 包含至少 3 个具体触发关键词
- [ ] 所有步骤使用命令式动词开头
- [ ] 每个关键步骤有至少 1 个示例
- [ ] 定义了 empty/null/ambiguous 三种边界情况
- [ ] SKILL.md 字数 < 2000（不含 frontmatter）
- [ ] 详细信息已移至 references/ 文件
- [ ] Tool Wrapper 模式包含权威声明（如适用）
- [ ] 输出格式有明确模板
