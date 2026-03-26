# Layering Strategy: What Goes Where

This guide helps you decide which instruction layer to use for any given rule or preference.

## The Decision Tree

Ask these questions in order:

**1. Does this apply to ALL my projects?**
→ Yes: `~/.claude/CLAUDE.md` (global) or `~/.claude/rules/` (if path-scoped)
→ No: continue

**2. Does this apply to my whole team on this project?**
→ Yes: `./.claude/CLAUDE.md` (project) or `.claude/rules/` (if path-scoped)
→ No: continue

**3. Does this apply only to certain file types or directories?**
→ Yes: `.claude/rules/*.md` with `paths:` frontmatter
→ No: continue

**4. Does this apply only in Cowork?**
→ Yes: Cowork Project Instructions
→ No: continue

**5. Does this apply across Chat and Cowork but not Code?**
→ Yes: Personal Preferences (Settings UI)
→ No: consider if you actually need this instruction

## Common Mistakes in Layering

**Scope pollution**: Putting project-specific rules in global CLAUDE.md. Cost: every project pays the token cost for rules that only matter in one.

**Scope leak**: Putting personal preferences in project CLAUDE.md that's committed to git. Your teammates don't need your language preference.

**Redundant coverage**: Same rule in global + project + rules. Pick one location; reference from others if needed.

**Over-centralization**: Everything in one CLAUDE.md instead of using rules for file-type-specific guidance. Result: bloated context for every interaction.

## Layer Characteristics

| Layer | Token cost | Sharing | Update frequency | Best for |
|---|---|---|---|---|
| Global CLAUDE.md | Every message, every project | Personal | Rarely | Identity, communication style |
| User Rules | On-demand | Personal | Occasionally | Cross-project coding patterns |
| Project CLAUDE.md | Every message, this project | Team (git) | Occasionally | Team standards, project context |
| Project Rules | On-demand | Team (git) | Frequently | File-type conventions |
| Cowork Instructions | Every Cowork message | Project members | Per project | Workflow-specific guidance |
| Personal Preferences | Every Chat/Cowork message | Personal | Rarely | Communication preferences |

## Real Example: Before and After Layering

**Before** (everything in one global CLAUDE.md, 350 lines):
- Personal style preferences
- Python coding standards
- React component patterns
- Testing conventions
- API design rules
- Project-specific deployment steps
- Team communication preferences

**After** (properly layered):
- `~/.claude/CLAUDE.md` (25 lines): role, language, reasoning style, format
- `~/.claude/rules/python.md` (30 lines): Python standards with `paths: ["**/*.py"]`
- `./.claude/CLAUDE.md` (40 lines): project context, team agreements
- `.claude/rules/react.md` (25 lines): React patterns with `paths: ["src/components/**"]`
- `.claude/rules/testing.md` (20 lines): test conventions with `paths: ["**/*.test.*"]`
- `.claude/rules/api.md` (25 lines): API standards with `paths: ["src/api/**"]`

**Result**: Global context dropped from 350 to 25 tokens. Most rules load only when relevant files are touched.
