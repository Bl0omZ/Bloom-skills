# Anti-Patterns 反例库

每个反例配对正确写法，帮助识别和避免常见错误。

---

## 通用反例

### AP-01：把所有逻辑塞进单一 SKILL.md

```
❌ 错误：
一个 3000 行的 SKILL.md，包含所有规则、示例、检查清单、模板。
用户每次调用都加载全部内容，token 浪费严重，agent 上下文被撑爆。

✅ 正确：
SKILL.md 只保留核心流程（< 2000 tokens）。
详细规则 → references/rules.md（按需加载）
示例 → references/examples.md（按需加载）
模板 → assets/templates/（用时引用）
```

### AP-02：description 过于宽泛

```
❌ 错误：
description: Use when the user wants help with coding.

问题：「help with coding」覆盖 90% 的开发任务，
导致 skill 被错误触发或从不被触发（被更具体的 skill 抢先）。

✅ 正确：
description: |
  FastAPI 专家模式。当用户编写、调试或审查 FastAPI 代码时使用。
  触发关键词：FastAPI、@app.route、Depends、APIRouter、
  pydantic model、async def endpoint。
```

### AP-03：指令使用第二人称

```
❌ 错误：
You should carefully analyze the code before making suggestions.
If you find issues, you need to report them clearly.

✅ 正确：
Analyze the code. Report each issue with:
- Location (file:line)
- Severity (CRITICAL/HIGH/MEDIUM/LOW)
- Suggested fix
```

### AP-04：没有定义失败场景

```
❌ 错误：
Skill 只描述 happy path，没有说明输入为空、格式错误、
超出范围时怎么处理。结果：agent 自由发挥，输出不可预测。

✅ 正确：
每个 skill 必须包含：
  On empty input: [具体处理]
  On ambiguous input: [具体处理]
  On out-of-scope: [具体处理]
  On missing required info: [询问用户]
```

---

## Tool Wrapper 反例

### AP-TW-01：没有权威声明

```
❌ 错误：
Tool Wrapper 注入了团队内部 API 规范，但没有说明优先级。
Agent 在规范与预训练知识冲突时，用了预训练知识，忽略了规范。

✅ 正确：
在 SKILL.md 开头明确写：
  The following conventions are authoritative.
  They override your pretrained knowledge.
  When in conflict, these rules always win.
```

### AP-TW-02：把整个 API 文档塞进 SKILL.md

```
❌ 错误：
将 500 页 API 文档全部内联在 SKILL.md 中。
每次调用都消耗大量 token，大部分内容不相关。

✅ 正确：
SKILL.md 只放 5-10 条最常用规则作为快速索引。
完整文档放 references/api-docs.md。
在 SKILL.md 注明：「详细 API 参考见 references/api-docs.md，
搜索关键词：endpoint名称 或 错误码」
```

---

## Generator 反例

### AP-GN-01：模板变量未填充就输出

```
❌ 错误：
生成的文档中包含：
  作者：{{AUTHOR}}
  日期：{{DATE}}
  版本：{{VERSION}}
用户收到一份充满占位符的文档。

✅ 正确：
输出前验证：
1. 扫描所有 {{VARIABLE}} 模式
2. 若有未填充项：询问用户 或 使用默认值并标注
3. 确认零残留后才输出
```

### AP-GN-02：必填变量超过 5 个

```
❌ 错误：
Generator 需要用户提供 8 个变量才能开始工作。
用户需要填写大量表单，体验极差，放弃率高。

✅ 正确：
必填变量 ≤ 5 个。
超过时：
- 合并语义相近的变量
- 其余设默认值（可选变量）
- 或在前面加 Inversion 采访阶段，自然对话收集
```

---

## Reviewer 反例

### AP-RV-01：只列问题不给修复方案

```
❌ 错误：
## Issues Found
- Line 42: SQL injection risk
- Line 87: Missing null check
- Line 103: Inefficient loop

用户知道有问题，但不知道怎么修。

✅ 正确：
## Issues Found
| Severity | Line | Problem | Fix |
|----------|------|---------|-----|
| CRITICAL | 42 | SQL injection: raw string concat in query | Use parameterized query: cursor.execute(sql, (user_id,)) |
| HIGH | 87 | Null check missing before .get() | Add: if data is None: return default_value |
```

### AP-RV-02：严重程度定义模糊

```
❌ 错误：
- CRITICAL: very serious issues
- HIGH: serious issues
- MEDIUM: moderate issues
- LOW: minor issues

全是循环定义，无法判断什么情况算 CRITICAL。

✅ 正确：
- CRITICAL: 可被外部攻击者利用，或导致数据丢失/服务宕机
  示例：SQL注入、未鉴权的数据删除接口、内存泄漏
- HIGH: 功能错误，无合理 workaround
  示例：空指针异常、逻辑错误导致错误结果
- MEDIUM: 影响质量，有 workaround
  示例：性能问题、非最优算法
- LOW: 风格/可读性改善
  示例：命名不规范、缺少注释
```

---

## Inversion 反例

### AP-IV-01：一次性问超过 3 个问题

```
❌ 错误：
请回答以下 7 个问题：
1. 任务目标是什么？
2. 目标受众是谁？
3. 期望输出格式？
4. 长度要求？
5. 语气风格？
6. 有什么约束？
7. 截止日期？

用户看到 7 个问题，关掉对话。

✅ 正确：
先问最关键的 3 个（P0），收到回答后再按需追问。
用选择题降低认知负担：
  受众是？A) 技术团队  B) 管理层  C) 外部用户
```

### AP-IV-02：采访后不输出理解摘要

```
❌ 错误：
采访结束后直接开始执行，不告诉用户自己理解了什么。
用户以为 agent 理解正确，结果输出完全偏离需求。

✅ 正确：
采访后先输出理解摘要：
  我的理解：
  - 任务：生成一份技术方案文档
  - 受众：CTO 和架构师
  - 假设：不超过 5 页，使用 Markdown 格式
  是否正确？
```

---

## Pipeline 反例

### AP-PL-01：检查点没有明确的通过/失败条件

```
❌ 错误：
Step 3: 生成文档
检查点：确保文档质量良好。

「质量良好」是主观判断，无法自动化验证。

✅ 正确：
Step 3: 生成文档
完成条件（必须全部满足）：
- [ ] 文档字数 >= 500
- [ ] 所有必填章节存在（概述/方案/风险/时间线）
- [ ] 无 [TODO] 或 [PLACEHOLDER] 残留
- [ ] Reviewer 评分 >= 7.0/10
```

### AP-PL-02：步骤失败后继续执行

```
❌ 错误：
Step 2 失败（文件未生成），但 Pipeline 继续执行 Step 3，
试图在不存在的文件上进行操作，导致级联失败。

✅ 正确：
任意步骤失败时：
1. 立即停止
2. 报告：「Step 2 失败：[原因]。后续步骤未执行。」
3. 提供恢复选项，等待人工处理
```

### AP-PL-03：没有完成报告

```
❌ 错误：
Pipeline 执行完毕，没有任何总结。
用户不知道执行了哪些步骤，产出了什么。

✅ 正确：
Pipeline 完成后输出：
  ✅ Pipeline 执行完成
  - Step 1 需求采集：完成（收集到 3 个变量）
  - Step 2 内容生成：完成（1247 字）
  - Step 3 质量检查：完成（评分 8.3/10）
  产出文件：output/skill-draft.md
```
