# Bloom Skills

A curated collection of agent skills for Claude Code, Gemini CLI, and other SKILL.md-compatible AI coding tools.

---

## skill-forge

**五模式 Agent Skill 锻造工坊** — 从需求到可用 SKILL.md 的完整引导流程。

### What it does

`skill-forge` guides you through designing a production-ready Agent Skill using five proven architectural patterns. Instead of writing a SKILL.md from scratch, you answer three questions — and the skill recommends a pattern, generates a complete draft, and scores its own output.

The skill itself demonstrates four patterns working together: **Inversion** (interview) → **Generator** (draft) → **Reviewer** (self-score) → **Pipeline** (ordered phases).

### The Five Patterns

| Pattern | Core Question | Typical Use |
|---------|--------------|-------------|
| **Tool Wrapper** | Make the agent an instant expert on any library | Inject API docs, coding standards, internal knowledge |
| **Generator** | Produce structured documents from a reusable template | Generate reports, SKILL.md files, PR descriptions |
| **Reviewer** | Score input against a checklist by severity | Code review, security audit, quality check |
| **Inversion** | Agent interviews you before acting | Requirement gathering, customized output, avoid guessing |
| **Pipeline** | Enforce a strict multi-step workflow with checkpoints | Release flow, doc generation, data processing |

### How it works

```
Phase 1 [Inversion]   → 3 questions: task / trigger / audience
Phase 2 [Generator]   → Rule-tree recommends pattern + composition
Phase 3 [Generator]   → Generates complete SKILL.md draft
Phase 4 [Reviewer]    → Self-scores on 5 dimensions (X/10) + 3 improvements
```

### Installation

Copy the `skill-forge/` directory into your skills folder:

```bash
# Claude Code (macOS)
cp -r skill-forge ~/.claude/skills/

# Or your agent's configured skills directory
```

### Trigger keywords

> 创建 skill、设计 agent、五模式、skill 架构、skill 质量评分、skill-forge

### File structure

```
skill-forge/
├── SKILL.md                        # Main: 4-phase Pipeline workflow
├── assets/
│   └── decision-tree.md            # Rule-tree for pattern selection
└── references/
    ├── tool-wrapper-pattern.md
    ├── generator-pattern.md
    ├── reviewer-pattern.md
    ├── inversion-pattern.md
    ├── pipeline-pattern.md
    ├── anti-patterns.md            # Common mistakes + correct alternatives
    ├── composition-guide.md        # 3 composition recipes
    └── prompt-quality-rules.md     # Shared prompt quality rules (DRY)
```

### Quality score

`skill-forge` self-evaluated its own SKILL.md using Phase 4 Reviewer:

```
质量评分：9.3/10
✅ 触发条件（1.9/2）
✅ 流程完整性（1.9/2）
✅ Prompt 质量（1.9/2）
✅ Reference 组织（2.0/2）
✅ 边界处理（1.6/2）
```

### Inspiration

Based on the article ["5 Agent Skill design patterns every ADK developer should know"](https://x.com/GoogleCloudTech/status/2033953579824758855) by @Saboo_Shubham_ and @lavinigam (Google Cloud Tech, March 2026).

`skill-forge` extends the original five patterns with:
- Interactive Wizard (Inversion-mode interview before generation)
- Self-scoring Reviewer (quality gate after generation)
- Anti-patterns library (what NOT to do, with correct alternatives)
- Composition guide (3 complete recipes for combining patterns)

---

## License

MIT
