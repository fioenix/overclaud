# How to Write Great Templates

This guide is for contributors who want to add new role or use-case templates.

## Template Principles

**Token efficiency first.** Templates load into context every message. Every word must earn its place.

**Specific beats general.** "Use 2-space indentation" works. "Write clean code" doesn't. If Claude can't verify compliance from a diff, the instruction is too vague.

**Role-specific, not generic.** A PM template should think in user outcomes and metrics, not code quality. A designer template should prioritize accessibility and visual hierarchy, not API patterns. The value is in the differentiation.

**Placeholders over defaults.** Use `[placeholder]` for anything user-specific. Don't pre-fill with assumptions that might not match.

## Template Structure

### Global Templates (under 30 lines)

```markdown
<!-- Global CLAUDE.md template for [Role]
     Copy to ~/.claude/CLAUDE.md and fill [placeholders] -->

# Who I Am
[Role]. [Thinking style].
Primary domains: [industries].
[Key priority].

Communicate as [style].

# Language
[Preference]. Technical terms always in English.

# Depth & Reasoning
[Default depth]. Always expose:
- [Role-specific reasoning requirements]

# Recommendations
[How to present options]

# Format
[Output preferences]

# Agentic Behavior
- [Role-specific workflow rules]
```

### Project Templates (under 25 lines)

Focus ONLY on project-scoped concerns. If it belongs in global, don't include it.

```markdown
<!-- Project CLAUDE.md template: [Use Case]
     Copy to .claude/CLAUDE.md and fill [placeholders] -->

# Project Context
[Brief description]

# [Use-case-specific sections]
...
```

## Quality Checklist

Before submitting a PR:

- [ ] Under line limit (30 global, 25 project, 15 cowork)
- [ ] All customizable parts use `[placeholders]`
- [ ] No overlap with other layers' responsibility
- [ ] No filler words or vague instructions
- [ ] Tested with Claude — instructions actually change behavior
- [ ] Comment at top explains target role and where to install
