---
name: skill-forge
description: |
  五模式 Agent Skill 锻造工坊。当需要创建新 Agent Skill、设计 Agent 工作流、
  选择合适的 Skill 架构模式（Tool Wrapper、Generator、Reviewer、Inversion、Pipeline）、
  优化现有 Skill 的 prompt 结构、或评估 Skill 质量时使用。
  触发关键词：创建 skill、设计 agent、五模式、skill 架构、agent 工作流、skill 质量评分、
  skill 哪里不好、优化现有 skill、评分 skill、这个 skill 写得怎么样、skill-forge。
---

# Agent Patterns — 五模式 Agent Skill 架构设计指南

本 SKILL 将引导完成从需求采集到生成可用 SKILL.md 草稿的完整流程，
并对输出进行质量自评分。SKILL 本身同时演示了 Inversion、Generator、
Reviewer 和 Pipeline 四个模式的组合应用。

## 边界说明

本 SKILL 负责 **内容设计**（逻辑架构、模式选择、prompt 质量）。
打包与发布请使用 `skill-creator` SKILL。两者互补，不重叠。

## 工作流（四阶段 Pipeline）

### Phase 1：Inversion 采访

在做任何推荐之前，先向用户提问以下三个问题。必须全部收集答案后才进入 Phase 2。

**问题 1**：这个 skill 要解决什么任务？请用一句话描述。
（例：「帮我审查 PR 代码，按严重程度分类问题」）

**问题 2**：什么情况下会触发这个 skill？用户会说什么话？
（例：「当用户说'帮我看看这个 PR'或'代码审查'时」）

**问题 3**：输出给谁看？
- A) 开发者（技术细节，代码片段）
- B) 最终用户（简洁，无技术术语）
- C) 系统/下游 agent（结构化 JSON/YAML）

**边界情况处理：**
- 用户回答含糊（「做个 agent」）→ 追问：「这个 agent 的核心动作是什么？输入什么，输出什么？」
- 用户直接说出模式名（「我要 Pipeline 模式」）→ 跳过推荐，直接进入 Phase 3
- 用户拒绝回答 → 使用默认值（通用任务/关键词触发/开发者受众）并说明

收集完成后输出摘要：
```
已收集信息：
- 任务：[用户答案]
- 触发：[用户答案]
- 受众：[用户答案]
```

---

### Phase 2：模式推荐

加载 `references/decision-tree.md`，按规则树推荐主模式。
同时加载 `references/composition-guide.md`，推荐可组合的次模式。

输出格式：
```
推荐主模式：[模式名]
理由：[一句话说明]

可组合次模式：
- [模式A]：用于 [场景]
- [模式B]：用于 [场景]

推荐配方：[Recipe 名称]（详见 composition-guide.md）
```

若任务匹配多个主模式（得分相近），列出 2-3 个候选并说明区别，让用户选择。
若任务完全不匹配任何模式，说明原因并建议：「考虑将任务拆分为多个 skill」。

---

### Phase 3：Generator 生成

根据推荐的主模式，加载对应 reference 文件：
- Tool Wrapper → `references/tool-wrapper-pattern.md`
- Generator → `references/generator-pattern.md`
- Reviewer → `references/reviewer-pattern.md`
- Inversion → `references/inversion-pattern.md`
- Pipeline → `references/pipeline-pattern.md`

同时加载 `references/prompt-quality-rules.md` 确保 prompt 质量。

生成完整的 SKILL.md 草稿，包含：
1. YAML frontmatter（name + description）
2. 主工作流（按模式规范）
3. 边界情况处理
4. 所有未确定信息用 `[TODO: 说明]` 占位

输出前验证：扫描生成内容，确认无残留 `{{VARIABLE}}` 或 `[TODO]` 占位符。
若有残留，用合理默认值填充或明确标注「需用户补充」。

最简输出示例（草稿最低标准）：
```markdown
---
name: pr-reviewer
description: |
  PR 代码审查助手。当用户说「review PR」、「看一下这个 PR」、
  「帮我审查代码」时使用。输出按严重程度分级的问题列表。
---

# PR Reviewer

加载 `references/review-checklist.md`。

审查输入的代码变更，按 CRITICAL/HIGH/MEDIUM/LOW 分级输出问题。
每个问题必须包含：位置、问题描述、修复方案。

边界处理：
- 无代码输入：要求用户提供 PR diff 或代码片段
- 超过 500 行：按模块分批，优先审查核心逻辑
```

---

### Phase 4：Reviewer 自评分

加载 `references/anti-patterns.md`，对生成的 SKILL.md 草稿进行质量评分。

按以下 5 个维度评分（每项 2 分，满分 10 分）：

| 维度 | 评分标准 |
|------|----------|
| 触发条件 | description 清晰、具体、包含触发关键词 |
| 流程完整性 | 所有步骤有明确的输入/输出/边界处理 |
| Prompt 质量 | 指令明确、无歧义、包含示例 |
| Reference 组织 | 详细信息在 references/，SKILL.md 保持精简 |
| 边界处理 | 定义了失败场景和 fallback 行为 |

输出格式：
```
质量评分：X.X/10

✅ 触发条件（2/2）：...
⚠️ 流程完整性（1.5/2）：...
✅ Prompt 质量（2/2）：...
⚠️ Reference 组织（1/2）：...
✅ 边界处理（2/2）：...

改进建议：
1. [具体建议]
2. [具体建议]
3. [具体建议]（如有）
```

评分 ≥ 8.0：「草稿质量良好，可直接使用」
评分 6.0-7.9：「建议按改进建议修改后使用」
评分 < 6.0：「建议重新设计」——重置方式：重新回答 Phase 1 的三个采访问题（不沿用之前的答案），从头执行四个阶段

---

## 五模式速查

| 模式 | 核心问题 | 典型场景 |
|------|---------|----------|
| Tool Wrapper | 让 agent 成为某库/框架的即时专家 | 注入 API 文档、编码规范、内部知识 |
| Generator | 从模板生成结构化文档 | 生成报告、SKILL.md、PR 描述 |
| Reviewer | 按检查清单评分并分级 | 代码审查、安全审计、质量检查 |
| Inversion | agent 先采访你，再行动 | 需求收集、定制化生成、避免猜测 |
| Pipeline | 强制多步骤工作流+检查点 | 发布流程、文档生成、数据处理 |

详细规范见各 pattern reference 文件。
模式组合方案见 `references/composition-guide.md`。
常见错误见 `references/anti-patterns.md`。
