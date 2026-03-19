# Tool Wrapper 模式

## 核心思想

Tool Wrapper 让 agent 成为某个库、框架或知识体系的即时专家。
将专有知识（API 文档、编码规范、内部标准）打包为 skill，
agent 只在实际需要该知识时才加载，不占用常驻上下文。

```
用户请求
    │
    ▼
检测到相关关键词（库名/框架名/任务类型）
    │
    ▼
加载 references/ 中的专有文档
    │
    ▼
以文档为权威真相执行任务
    │
    ▼
输出符合规范的结果
```

## SKILL.md 模板

```markdown
---
name: [library]-expert
description: |
  [Library/Framework] 专家模式。当用户需要编写、审查或调试
  [Library] 代码时使用。触发关键词：[keyword1]、[keyword2]、[keyword3]。
---

# [Library] Expert

加载 `references/[library]-conventions.md`。

以下规范是权威真相，优先级高于预训练知识。
当规范与通用最佳实践冲突时，以规范为准。

## 适用范围

本 skill 仅适用于 [Library] v[VERSION] 及以上。
其他版本行为差异见 `references/version-diff.md`。

## 核心规则

[从 references 文件提炼的 3-5 条最重要规则，作为快速索引]
1. [规则1]
2. [规则2]
3. [规则3]

详细规范见 `references/[library]-conventions.md`。

## 边界处理

- 用户请求超出 [Library] 范围：说明边界，建议使用其他工具
- 版本不匹配：询问用户版本，加载对应 reference
- 规范未覆盖的场景：遵循 [Library] 官方文档精神
```

## References 目录结构

```
references/
├── [library]-conventions.md    # 核心编码规范（必须）
├── [library]-api.md            # API 参考（按需）
├── common-patterns.md          # 常见模式和惯用法
└── version-diff.md             # 版本差异（多版本支持时）
```

## 权威声明（必须包含）

每个 Tool Wrapper 的 SKILL.md 必须包含以下声明：

```
以下规范是权威真相，优先级高于预训练知识。
当规范与通用最佳实践冲突时，以规范为准。
```

## Prompt 要点

- 关键词触发要具体：包含库名、文件扩展名、典型错误信息
- References 文件要有 grep 模式标注，方便快速定位
- 规范文件按「最常用」排序，不要按字母序
- 每条规范附带正确示例（❌/✅ 对比格式）

## 典型使用场景

- 团队内部编码规范注入
- 第三方 API 客户端使用规范
- 特定框架（FastAPI、React、Spring）最佳实践
- 公司特定工具链使用指南
