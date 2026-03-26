# Claude Instruction Layers — Complete Architecture

Claude loads instructions from multiple sources in a precise precedence order. Understanding the layer hierarchy is critical for effective configuration without conflicts or wasted context.

## Layer Hierarchy & Precedence

Instructions are loaded in this order, with more specific layers overriding more general ones:

| Priority | Layer | Location | Scope | Format | Shared | Loading Behavior |
|----------|-------|----------|-------|--------|--------|-------------------|
| 1 | System Prompt | `--append-system-prompt` flag or API | Per invocation | Code/markdown | N/A | Override; highest precedence |
| 2 | Project Rules | `.claude/rules/*.md` | File path scoped | Code | Team (git) | All matching rules combined; YAML `paths:` field applies filter |
| 3 | Subdirectory CLAUDE.md | `subdir/CLAUDE.md` | Triggered on-demand | Code | Team (git) | Loaded when Claude reads from that subdirectory |
| 4 | Project CLAUDE.md | `./CLAUDE.md` or `./.claude/CLAUDE.md` | Team-shared, one project | Code | Team (git) | Loaded fully; ~200 line soft target |
| 5 | User Rules | `~/.claude/rules/*.md` | All projects | Code | Personal | All matching rules combined; path-scoped if specified |
| 6 | User CLAUDE.md | `~/.claude/CLAUDE.md` | All projects | Code | Personal | Loaded fully; ~200 line soft target |
| 7 | Auto Memory | `~/.claude/projects/<project>/memory/MEMORY.md` | Per project | Code | Personal | First 200 lines only; auto-written by Claude |
| 8 | Managed Policy | macOS: `/Library/Application Support/ClaudeCode/CLAUDE.md`; Linux: `/etc/claude-code/CLAUDE.md` | Org-wide | Code | All org users | Cannot be excluded; lowest precedence |
| 9 | Personal Preferences | Settings UI | All conversations | Chat/Cowork | Personal | Applied at runtime |

**Override Rule:** More specific always wins. File path matters: `subdir/CLAUDE.md` > `./CLAUDE.md` > `~/.claude/CLAUDE.md`.

## Loading Behavior Details

### CLAUDE.md Files
- Loaded in full on every agent invocation
- Target size: under 200 lines (measured at writing time; no hard limit)
- Can use `@import other-file.md` syntax to reference external markdown files (relative paths work)
- HTML comments `<!-- comment -->` are stripped before loading — use for human notes, not instructions

### Auto Memory (MEMORY.md)
- Claude auto-writes to `~/.claude/projects/<project>/memory/MEMORY.md` between invocations
- Only first 200 lines loaded per invocation (hard cap)
- Used to store project-specific learnings, discovered patterns, and decision history
- Not shared; personal to you
- Useful for remembering context across sessions without cluttering CLAUDE.md

### Rule Files
- `.claude/rules/*.md` and `~/.claude/rules/*.md` load ALL matching `.md` files in those directories
- Typical use: one file per concern (e.g., `naming-conventions.md`, `error-handling.md`, `security.md`)
- Can be path-scoped using YAML frontmatter:
  ```yaml
  ---
  paths:
    - src/components/**
    - lib/**
  ---
  ```
- Path matching uses glob syntax; rules only apply to files matching the pattern
- Project-level rules (`.claude/rules/`) override user-level rules (`~/.claude/rules/`)

### Subdirectory CLAUDE.md
- Not automatically loaded; loaded on-demand when Claude reads files from that directory
- Useful for repo structure with distinct domains needing different conventions
- Example: monorepo with `/api/CLAUDE.md` and `/web/CLAUDE.md` for API-specific and web-specific guidance
- Overrides project root CLAUDE.md for that subtree only

## Decision Framework: Where Instructions Belong

### Use Global User CLAUDE.md (`~/.claude/CLAUDE.md`) for:
- Personal style and communication preferences
- Coding standards that apply across all your projects (naming, patterns, testing approach)
- Your role, context, and domain expertise ("I'm a CTO focused on retail SaaS")
- Constraints you impose everywhere (language preferences, banned patterns)
- Decision-making frameworks that follow you from project to project

**Cost:** Loaded on every invocation for every project. Keep it tight — every line taxes all your conversations.

### Use User Rules (`~/.claude/rules/`) for:
- File-type-specific conventions (markdown formatting, YAML structure, SQL patterns)
- Domain-specific practices that cross projects (supply chain terminology, accessibility rules)
- Each file addresses one cohesive topic

**Cost:** Loaded only if matching files are edited. Safer than CLAUDE.md for verbose content.

### Use Project CLAUDE.md (`./CLAUDE.md` or `./.claude/CLAUDE.md`) for:
- Team agreements and project-specific conventions
- Context this team needs every conversation (tech stack choices, deployment model, error budgets)
- Current project constraints and priorities
- Shared via git; all team members see and follow it

**Cost:** Loaded every invocation for this project. Watch the line count; it compounds for large teams.

### Use Project Rules (`.claude/rules/`) for:
- File-type or directory-specific guidance scoped to this project
- Detailed implementation patterns you don't want cluttering project CLAUDE.md
- Path-scoped rules: "rules for `/src/api/` differ from `/src/web/`"
- Easier to diff and manage than monolithic CLAUDE.md

**Cost:** Loaded only when matching files are involved.

### Use Subdirectory CLAUDE.md (`subdir/CLAUDE.md`) for:
- Highly specialized guidance for one module/domain within the project
- Monorepo with distinct patterns per service (API, web frontend, admin)
- Infrequently-changing guidance you don't want in version control churn

**Cost:** Loaded on-demand; minimal impact if not triggered.

### Use Auto Memory (`~/.claude/projects/<project>/memory/MEMORY.md`) for:
- Discoveries and patterns learned in this project session
- Context that would be perfect in project CLAUDE.md but you're not ready to commit to git
- Temporary experiments or evolving practices
- Between-session continuity without cluttering permanent instructions

**Cost:** Personal only; no team burden. Limited to 200 lines per invocation.

### Use System Prompt (`--append-system-prompt`) for:
- One-off invocation-specific overrides
- API usage with custom Claude instances
- Testing or evaluation scenarios
- Not recommended for routine project work

**Cost:** Must be passed each invocation; API overhead if large.

## Practical Layering Examples

### Example 1: Full-Stack Team on Retail SaaS
```
~/.claude/CLAUDE.md (150 lines)
  └─ Role, domain (retail), company standards (Python/TypeScript, testing, code review)

~/.claude/rules/
  ├─ sql-patterns.md (safe queries, CTEs, no raw strings)
  ├─ error-handling.md (error types, logging, recovery)
  └─ api-security.md (auth, CORS, rate limiting)

project/CLAUDE.md (100 lines)
  └─ Tech stack (FastAPI, React, Postgres), deployment (AWS), team agreements

project/.claude/rules/
  ├─ naming-conventions.md (domain model naming, API endpoints)
  └─ database-migrations.md (safety checks, rollback patterns)

project/services/checkout/CLAUDE.md (80 lines)
  └─ Checkout-specific: payment flow, idempotency, retry logic

project/services/checkout/.claude/rules/
  └─ stripe-integration.md (webhook handling, webhook validation)
```

**Precedence:** When editing `/project/services/checkout/payment.py`:
1. Stripe rules (most specific file)
2. Checkout CLAUDE.md
3. Project rules
4. Project CLAUDE.md
5. User rules
6. User CLAUDE.md
7. Auto memory (first 200 lines)
8. Managed policy

### Example 2: Solo Developer, Private Project
```
~/.claude/CLAUDE.md (200 lines)
  └─ Comprehensive: style, approach, frameworks, requirements

~/.claude/rules/
  └─ patterns.md (your testing, error handling, async patterns)

project/CLAUDE.md (80 lines)
  └─ This project only: context, goals, current focus

project/.claude/rules/
  └─ (empty — project is small enough for CLAUDE.md)

Auto memory (grows per session)
  └─ Discoveries, TODOs, patterns being tested
```

### Example 3: Monorepo (API + Web + Admin)
```
project/CLAUDE.md (100 lines)
  └─ Shared: monorepo structure, shared patterns, cross-service contracts

project/api/CLAUDE.md (80 lines)
  └─ API: FastAPI specifics, authentication, rate limiting

project/api/.claude/rules/
  └─ database-safety.md (path: src/db/**, migration validation)

project/web/CLAUDE.md (80 lines)
  └─ Web: React patterns, state management, component hierarchy

project/web/.claude/rules/
  └─ component-testing.md (path: src/components/**, test structure)

project/admin/CLAUDE.md (70 lines)
  └─ Admin: internal tools, less polish than web, fast iteration

~/.claude/rules/
  └─ typescript-strict.md (enforced across all projects)
```

## Technical Details

### @import Syntax
Reference external markdown from CLAUDE.md:
```markdown
# Project Guidelines

@import ./guidelines/patterns.md
@import ~/.claude/shared-context.md
```

- Paths are relative to the CLAUDE.md file's directory, or absolute with `~`
- Imported content is inlined; no circular import protection (don't do it)
- Useful for sharing across CLAUDE.md files without duplication

### Path-Scoped Rules
Apply rules only to matching paths:
```yaml
---
paths:
  - src/api/**
  - lib/backend/**
---

# API-Specific Conventions

All API routes use Pydantic models for validation...
```

- Glob syntax: `*` matches within directory, `**` matches recursively
- Rules apply only to files matching ANY pattern
- Multiple rules can match one file; all are loaded and combined

### HTML Comments
```markdown
<!-- This is a note for humans reading the file, not for Claude -->

Instructions here are loaded as-is.

<!-- TODO: Update this when we migrate to async/await -->
```

- Stripped before loading; no impact on Claude's instruction size
- Use for TODOs, explanations for teammates, or temporarily disabled instructions

## Key Limits & Targets

| Item | Target/Limit | Reason |
|------|--------------|--------|
| CLAUDE.md size | ~200 lines | Loaded every invocation; context matters |
| MEMORY.md size | Hard 200 line cap | Auto-written; prevents unbounded growth |
| Rule files | Unlimited | Loaded only on-demand |
| Subdirectory CLAUDE.md | ~100 lines each | Loaded on-demand; specificity helps focus |
| Total project rules | ~500 lines combined | Reasonable limit before collapsing to CLAUDE.md |
| Layer depth | 7 practical layers | Beyond this, precedence becomes unmaintainable |

## Troubleshooting Instruction Conflicts

**Problem:** Claude sometimes ignores an instruction.

**Diagnosis:**
1. Verify the file was loaded: check `claude-opz debug --config` output
2. Check precedence: a higher-priority layer may be overriding it
3. Verify path matching: rules with `paths:` may not apply to the file being edited
4. Check line count: auto memory caps at 200 lines (older content is lost)
5. Check CLAUDE.md size: if above 200 lines, consider refactoring to rules

**Solution:**
- Move to a more specific layer if being overridden
- Use rules with path scoping to isolate domain-specific guidance
- Split bloated CLAUDE.md into rules files
- Check auto memory; older entries may have been truncated

## Next Steps

See also:
- **Token Optimization** (`token-optimization.md`): How to write efficient instructions
- **Common Mistakes** (`common-mistakes.md`): Anti-patterns to avoid
