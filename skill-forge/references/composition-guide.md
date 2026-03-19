# 模式组合指南

五个模式不是孤立的，它们可以串联（sequential）或嵌套（nested）使用。
本文件提供组合决策图和三个完整的组合配方（Recipe）。

## 组合决策图

```
任务需要「先了解需求」？
├── YES → 以 Inversion 开头
│         然后根据后续任务类型选择：
│         ├── 需要生成文档 → Inversion + Generator
│         ├── 需要执行多步骤 → Inversion + Pipeline
│         └── 需要注入专业知识 → Inversion + Tool Wrapper
└── NO ↓

任务需要「注入特定知识/规范」？
├── YES → Tool Wrapper 为基础
│         然后：
│         ├── 还需要生成输出 → Tool Wrapper + Generator
│         └── 还需要审查质量 → Tool Wrapper + Reviewer
└── NO ↓

任务的核心是「生成结构化输出」？
├── YES → Generator
│         然后：
│         └── 需要质量保证 → Generator + Reviewer（末尾）
└── NO ↓

任务的核心是「检查/评估质量」？
├── YES → Reviewer
│         通常独立使用，或作为 Pipeline 的最后一步
└── NO ↓

任务有「固定顺序的多个步骤」？
├── YES → Pipeline（可在步骤内嵌入其他模式）
└── NO → Tool Wrapper（默认：注入上下文知识）
```

## 组合矩阵

| 主模式 + 次模式 | 适用场景 | 典型例子 |
|---------------|---------|----------|
| Inversion + Generator | 需求不明确的内容生成 | 「帮我写篇技术文章」 |
| Inversion + Pipeline | 需求不明确的多步骤任务 | 「帮我做代码重构」 |
| Tool Wrapper + Reviewer | 规范驱动的质量检查 | FastAPI 代码审查 |
| Tool Wrapper + Generator | 规范驱动的内容生成 | 按团队规范写 PR 描述 |
| Generator + Reviewer | 生成后自动质检 | 生成 SKILL.md 后评分 |
| Pipeline + Reviewer | 流程最后一步质量门控 | 发布前的质量检查 |
| Inversion + Tool Wrapper + Generator | 全定制化生成 | 定制化的技术方案文档 |

## Recipe A：文档生成流水线

**适用场景：** 生成高质量的结构化文档，需要采集需求、生成内容、保证质量。

**模式组合：** Inversion → Generator → Reviewer

```
Phase 1 [Inversion]：采访需求
  问题1：文档的目标读者是谁？
  问题2：文档需要包含哪些核心章节？
  问题3：有没有现有文档可以参考风格？
  ↓ 输出：需求摘要 + 确认

Phase 2 [Generator]：生成文档
  加载对应模板（按文档类型）
  填充 Phase 1 收集的变量
  ↓ 输出：完整文档草稿

Phase 3 [Reviewer]：质量检查
  检查维度：
  - 结构完整性（所有必要章节）
  - 内容准确性（无 [TODO] 残留）
  - 受众适配性（语言风格匹配）
  ↓ 输出：评分 + 改进建议
```

**SKILL.md 关键片段：**

```markdown
## 工作流

### Phase 1：了解需求（Inversion）
提问以下问题，收集变量：
1. 文档目标读者？（影响语言风格）
2. 核心章节？（影响结构模板选择）
3. 风格参考？（可选）

### Phase 2：生成文档（Generator）
根据 Phase 1 答案，加载对应模板并填充。
验证：无残留占位符。

### Phase 3：质量评审（Reviewer）
按 references/doc-quality-checklist.md 评分。
评分 >= 8.0 才输出，否则自动修复后重评。
```

## Recipe B：专家顾问

**适用场景：** 在特定知识领域内，为用户的具体问题提供定制化建议。

**模式组合：** Tool Wrapper（知识注入）+ Inversion（了解具体问题）→ Generator（输出建议报告）

```
[Tool Wrapper 基础层]：始终加载
  加载 references/domain-knowledge.md
  声明权威性：以此为唯一真相
  ↓ 建立专业知识背景

Phase 1 [Inversion]：了解具体问题
  在领域知识背景下，采访用户：
  问题1：具体遇到什么问题？
  问题2：已经尝试过什么方案？
  问题3：有什么约束条件？
  ↓ 输出：问题摘要

Phase 2 [Generator]：生成顾问报告
  模板：
  ## 问题诊断
  ## 推荐方案（主方案 + 备选）
  ## 实施步骤
  ## 风险与注意事项
  ↓ 输出：结构化顾问报告
```

**适合这个 Recipe 的 skill 类型：**
- 架构顾问（注入架构原则 + 采访具体系统 + 生成方案）
- 安全顾问（注入安全规范 + 采访具体代码 + 生成审查报告）
- 法律顾问（注入法规文本 + 采访具体情况 + 生成建议）

## Recipe C：代码安全审查

**适用场景：** 按安全规范审查代码，分级报告漏洞，提供修复方案。

**模式组合：** Tool Wrapper（注入安全规则）→ Reviewer（分级评估）→ Pipeline（确保完整流程）

```
Step 1 [Tool Wrapper]：加载安全规范
  加载 references/security-rules.md
  声明：以下安全规则是权威标准
  完成条件：规则文件已加载

Step 2 [Reviewer]：执行安全审查
  按规范逐项检查：
  - CRITICAL: 注入攻击、认证绕过、数据泄露
  - HIGH: 权限错误、不安全的依赖
  - MEDIUM: 信息泄露、错误处理
  - LOW: 代码质量、最佳实践
  完成条件：所有检查项已评估，报告已生成

Step 3 [Pipeline 输出门控]：质量验证
  验证报告包含：
  - [ ] 总体风险评级
  - [ ] 所有 CRITICAL 和 HIGH 问题已有修复方案
  - [ ] 建议的下一步行动
  完成条件：报告格式完整，无遗漏章节

输出：结构化安全审查报告
```

**本 SKILL（agent-patterns）本身就是 Recipe 的实践：**

```
Phase 1 = Inversion（采访需求）
Phase 2 = Generator/决策树（推荐模式）
Phase 3 = Generator（生成 SKILL.md 草稿）
Phase 4 = Reviewer（质量自评分）
整体 = Pipeline（四步有序执行，每步有检查点）
```

## 组合时的注意事项

1. **Inversion 永远在开头**：如果用了 Inversion，它必须是第一步，不能在中途插入
2. **Reviewer 通常在末尾**：作为质量门控，在生成内容之后执行
3. **Tool Wrapper 是背景层**：在整个对话期间持续有效，不是某个 Phase
4. **Pipeline 是外层结构**：Pipeline 的每个 Step 内部可以嵌入其他模式
5. **避免过度组合**：超过 3 个模式组合时，考虑拆分为多个独立 skill
