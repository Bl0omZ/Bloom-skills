# Generator 模式

## 核心思想

Generator 从可复用模板生成结构化文档。
输入是变量集合，输出是填充完整的文档。
适合批量、标准化的内容生成任务。

```
变量输入（用户提供 或 从上下文提取）
    │
    ▼
加载模板（assets/ 或 references/ 中的模板文件）
    │
    ▼
填充变量 → 验证完整性
    │
    ├── 缺少必填变量 → 询问用户
    └── 完整 → 输出文档
```

## SKILL.md 模板

```markdown
---
name: [output-type]-generator
description: |
  [输出类型] 生成器。当用户需要生成 [输出类型] 时使用。
  触发关键词：生成、创建、写一个 [输出类型]、[keyword2]。
  输入：[必填变量列表]。输出：完整的 [输出类型] 文档。
---

# [Output Type] Generator

## 必填变量

从用户输入中提取以下变量。缺失时主动询问：
- `{{NAME}}`：名称（示例："用户认证模块"）
- `{{PURPOSE}}`：用途说明
- `{{AUDIENCE}}`：目标受众

## 可选变量（有默认值）
- `{{TONE}}`：语气风格（默认：专业简洁）
- `{{LENGTH}}`：长度要求（默认：适中）

## 生成步骤

1. 提取所有变量，列出缺失项
2. 询问缺失的必填变量（每次最多问 3 个）
3. 加载模板并填充变量
4. 检查：所有 [TODO] 占位符是否已填充
5. 输出完整文档

## 边界处理

- 变量冲突（如 TONE=幽默 但 AUDIENCE=法律团队）：提示用户确认
- 必填变量用户拒绝提供：用 [TODO: 请填写XXX] 占位，注明需补充
- 模板不存在：列出可用模板，请用户选择
```

## References 目录结构

```
references/
└── variable-definitions.md     # 变量说明和默认值

assets/
└── templates/
    ├── [output-type]-template.md   # 主模板
    └── [output-type]-template-short.md  # 简版（可选）
```

## 模板文件格式规范

模板文件使用 `{{VARIABLE}}` 语法标记变量：

```markdown
# {{NAME}}

**用途：** {{PURPOSE}}
**受众：** {{AUDIENCE}}

## 概述

{{OVERVIEW}}

## 详细说明

[TODO: 根据具体内容填充]
```

## Prompt 要点

- 必填变量不超过 5 个（超过则考虑用 Inversion 模式先采访）
- 变量命名使用 SCREAMING_SNAKE_CASE
- 每个变量提供具体示例，不要只写「示例：略」
- 输出前必须验证：无残留 `{{VARIABLE}}` 未填充
- 对于复杂文档，先生成结构骨架，再逐节填充

## 典型使用场景

- SKILL.md 草稿生成（本 SKILL 本身就是 Generator）
- PR 描述、Changelog、Release Notes
- API 文档、README 模板
- 周报、项目复盘、技术方案文档
- 测试用例模板批量生成
