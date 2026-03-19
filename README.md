# Bloom Skills

精选 Agent Skill 合集，兼容 Claude Code、Gemini CLI 及所有支持 SKILL.md 规范的 AI 编程工具。

---

## skill-forge

**五模式 Agent Skill 锻造工坊** — 从需求采集到可用 SKILL.md 草稿的完整引导流程。

### 这是什么

`skill-forge` 帮你用五种经过验证的架构模式设计 Agent Skill。你不需要从零写 SKILL.md——只需回答三个问题，skill 会自动推荐合适的模式、生成完整草稿，并对输出进行质量评分。

skill-forge 本身同时演示了四种模式的组合：**Inversion**（采访）→ **Generator**（生成）→ **Reviewer**（自评分）→ **Pipeline**（有序执行）。

### 五种模式

| 模式 | 核心问题 | 典型场景 |
|------|---------|----------|
| **Tool Wrapper** | 让 agent 成为某库/框架的即时专家 | 注入 API 文档、编码规范、内部知识 |
| **Generator** | 从模板生成结构化文档 | 生成报告、SKILL.md、PR 描述 |
| **Reviewer** | 按检查清单评分并按严重程度分级 | 代码审查、安全审计、质量检查 |
| **Inversion** | agent 先采访你，再行动 | 需求收集、定制化生成、避免猜测 |
| **Pipeline** | 强制多步骤工作流 + 检查点 | 发布流程、文档生成、数据处理 |

### 工作流程

```
Phase 1 [Inversion]   → 采访3个问题：任务 / 触发条件 / 目标受众
Phase 2 [决策树]      → 推荐主模式 + 组合建议
Phase 3 [Generator]   → 生成完整 SKILL.md 草稿
Phase 4 [Reviewer]    → 5维度质量评分（X/10）+ 3条改进建议
```

### 安装方式

将 `skill-forge/` 目录复制到你的 skills 文件夹：

```bash
# Claude Code（macOS）
cp -r skill-forge ~/.claude/skills/

# 或复制到你的 agent 配置的 skills 目录
```

### 触发关键词

> 创建 skill、设计 agent、五模式、skill 架构、skill 质量评分、skill-forge
> skill 哪里不好、优化现有 skill、这个 skill 写得怎么样

### 文件结构

```
skill-forge/
├── SKILL.md                        # 主控文件：四阶段 Pipeline 工作流
├── assets/
│   └── decision-tree.md            # 规则树：模式选择逻辑
└── references/
    ├── tool-wrapper-pattern.md     # 模式1：权威声明 + 模板
    ├── generator-pattern.md        # 模式2：变量填充 + 模板
    ├── reviewer-pattern.md         # 模式3：评分维度 + 检查清单
    ├── inversion-pattern.md        # 模式4：采访设计 + 优先级
    ├── pipeline-pattern.md         # 模式5：检查点设计
    ├── anti-patterns.md            # 反例库：每模式 2-3 个常见错误 + 正确对比
    ├── composition-guide.md        # 组合指南：3 个完整配方（Recipe）
    └── prompt-quality-rules.md     # 共用 prompt 质量规则（DRY）
```

### 自评分

skill-forge 用自身的 Phase 4 Reviewer 对 SKILL.md 进行了质量评估：

```
质量评分：9.3/10

✅ 触发条件（1.9/2）：关键词覆盖全面，包含自然语言触发词
✅ 流程完整性（1.9/2）：四阶段有明确输入/输出/边界处理
✅ Prompt 质量（1.9/2）：命令式指令，含最简输出示例
✅ Reference 组织（2.0/2）：主文件精简，详细内容按需加载
✅ 边界处理（1.6/2）：定义了失败场景和重置流程
```

### 与现有 skill 的关系

- **不替代 `skill-creator`**：两者互补。`skill-forge` 负责内容设计（架构模式、prompt 质量），`skill-creator` 负责打包发布。
- **不替代 `prompt-engineering-patterns`**：`skill-forge` 将其核心原则适配到 agent 架构场景，提炼为 `references/prompt-quality-rules.md`。

### 灵感来源

基于 Google Cloud Tech 文章 [《5 Agent Skill design patterns every ADK developer should know》](https://x.com/GoogleCloudTech/status/2033953579824758855)（@Saboo_Shubham_ 和 @lavinigam，2026年3月）。

`skill-forge` 在原文五模式基础上新增：
- **Interactive Wizard**：Inversion 模式采访，生成前先收集需求
- **自评分 Reviewer**：生成后自动质量评分，吃自己的狗粮
- **反例库**：每个模式的常见错误写法 + 正确对比
- **组合配方**：3 个完整的模式组合 Recipe

---

## License

MIT
