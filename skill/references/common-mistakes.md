# Common Mistakes in Claude Instructions

Instruction mistakes are expensive: they waste tokens, contradict each other, and lead to ignored rules. These patterns recur across teams.

## 1. Putting Everything in Global CLAUDE.md (Scope Pollution)

**Mistake:**
```
~/.claude/CLAUDE.md (350 lines):
- Personal style guidelines
- Python-specific conventions
- TypeScript-specific conventions
- React patterns
- SQL best practices
- Error handling (general)
- Error handling (API-specific)
- Error handling (background jobs)
- Logging standards
- Testing approach
- Database migration safety
... (continues)
```

**Problem:**
- Loads on every project, every invocation
- Most content irrelevant to any given project
- Context bloat; hard to find relevant guidance
- Difficult to update without impacting unrelated work
- Team members can't opt out of irrelevant rules

**Solution:**
```
~/.claude/CLAUDE.md (80 lines):
- Role and domain ("CTO, retail/SaaS")
- Cross-project coding principles (type safety, testing mindset)
- Communication preferences
- Banned patterns (don't mention these everywhere)

~/.claude/rules/:
- python-conventions.md
- typescript-conventions.md
- react-patterns.md
- sql-safety.md
- error-handling.md
- testing-approach.md
```

**Rule:** Global CLAUDE.md should be <150 lines. Everything else goes to rules or project-level.

## 2. Vague Instructions That Don't Affect Output

**Mistake:**
```
Write good code.
Be helpful and friendly.
Think about performance.
Follow best practices.
Use common sense.
Write maintainable solutions.
```

**Why it's a mistake:** These are expectations, not instructions. Claude doesn't know what "good" means. No code path is affected.

**Solution:** Replace with concrete, verifiable rules.

**Before:**
```
We care about code quality.
```

**After:**
```
Code quality requirements:
- Type coverage ≥90%
- Test coverage ≥80%
- Cyclomatic complexity ≤5 per function
- No console.log in production
- All errors have context (not just "error")
```

**Test:** Can a code reviewer check if the rule was followed? If not, it's not an instruction.

## 3. Contradicting Instructions Across Layers

**Mistake:**
```
~/.claude/CLAUDE.md:
"Use simple, clear variable names. Prefer short names over descriptive."

project/CLAUDE.md:
"Variable names must be descriptive and self-documenting. Avoid abbreviations."

project/.claude/rules/naming.md:
"snake_case for variables. camelCase for functions. Hungarian notation for types."
(What's camelCase if no abbreviations allowed?)
```

**Problem:**
- Claude doesn't know which to follow; results are unpredictable
- Rules conflict creates confusion for new team members
- Impossible to verify compliance

**Solution:**
1. Identify the true rule (what does the team actually do?)
2. Put it in one place (usually project level)
3. Remove or clarify others

**Fixed version:**
```
~/.claude/CLAUDE.md:
(no naming rules — too project-specific)

project/CLAUDE.md:
"Variables: descriptive snake_case (user_count not uc). Functions: camelCase.
No abbreviations except well-known (html, url, id)."
```

**Prevention:** Review layers systematically. Check `claude-opz debug --config` output for overlapping instructions.

## 4. Over-Formatting Instructions (Treating Them as Documents)

**Mistake:**
```
# Code Quality Standards

## Naming Conventions

### Variable Naming

Variables are a fundamental part of any codebase. They hold values that are used
throughout the program. The names we choose for variables have a significant impact
on code readability and maintainability.

**Best Practices for Variable Naming:**

1. Use descriptive names that clearly indicate the variable's purpose
2. Use snake_case for variable names in Python
3. Use camelCase for variable names in JavaScript
4. Avoid single-letter variable names (except for loop counters and math)
5. Don't use names that could be confused with built-ins or keywords

#### Examples of Good Variable Names
- user_id (Python)
- userName (JavaScript)
- customer_email_list (Python)

#### Examples of Bad Variable Names
- x (too vague)
- temp (doesn't explain purpose)
- data (meaningless)
```

**Problem:**
- Document-style formatting adds tokens without improving instruction clarity
- Excessive headers create noise
- Examples take up tokens; most aren't needed
- Prose about the *why* doesn't help Claude execute the rule
- Reads like a team handbook, not executable instructions

**Solution:** Instructions aren't documents. Write them for machines.

**Fixed version:**
```
Naming:
- Variables: snake_case, descriptive (user_count not x)
- Functions: camelCase, verb-first (getUser not user)
- No abbreviations except: html, url, id, api, auth
```

**Rule:** If it reads like a team handbook, it's over-formatted.

## 5. Forgetting That Project CLAUDE.md Is Shared (Git Leakage)

**Mistake:**
```
./project/CLAUDE.md (committed to git):
"When pairing with @alice, use her naming conventions. When pairing with @bob,
note that he prefers detailed comments. Here's my personal timezone: PST.
I usually work 9am-6pm but take Fridays off. When scheduling, reach out to @me."
```

**Problem:**
- Personal preferences are in version control
- Entire team now sees and follows one person's schedule/preferences
- This belongs in personal auto memory, not shared project rules
- Creates merge conflicts and noise in git history

**Solution:** Personal context goes to auto memory or user CLAUDE.md.

**Fixed version:**
```
./project/CLAUDE.md:
(Remove personal timezone, schedule, pairing notes — not team policy)

~/.claude/projects/[project]/memory/MEMORY.md:
"Working with Alice: she prefers [preference]. Working with Bob: he prefers [preference].
My timezone: PST. Typical hours: 9am-6pm, Fridays off."
```

**Rule:** Project CLAUDE.md (in git) = team agreements. Personal context = auto memory.

## 6. Instructions That Are "Approved" But Never Actually Checked

**Mistake:**
```
"The code must follow SOLID principles."
"Ensure the solution is elegant and well-thought-out."
"Consider edge cases comprehensively."
"Write code that future developers will appreciate."
```

**Problem:**
- No one verifies these on code review
- They're expectations, not rules
- Claude has no signal on whether they were "satisfied"
- They waste tokens while accomplishing nothing

**Solution:** Replace with checkable rules.

**Before:**
```
Follow DRY principle.
```

**After:**
```
Don't repeat code. Extract to helper functions/components.
Max 3-line duplication before extraction required.
```

(Now reviewable: find duplication, count lines, check if extracted.)

**Test:** Can this be checked in code review? If the reviewer never mentions it, remove it.

## 7. Not Using .claude/rules/ for File-Type or Path-Specific Guidance

**Mistake:**
```
./project/CLAUDE.md (becoming a monolith):
"When writing SQL migrations, use this pattern...
When writing React components, use this pattern...
When writing API endpoint handlers, use this pattern...
When writing bash scripts, use this pattern...
When setting up Docker configs, use this pattern..."
```

**Problem:**
- CLAUDE.md loads on every invocation, regardless of file type
- 90% of the guidance is irrelevant on any given edit
- Hard to find the right section when you need it
- Difficult to update rules without cluttering the main file

**Solution:** Move to `.claude/rules/` with path scoping.

**Fixed version:**
```
./project/.claude/rules/sql-migrations.md:
(SQL-specific guidance)

./project/.claude/rules/react-components.md:
(path: src/components/**)
(React-specific guidance)

./project/.claude/rules/api-handlers.md:
(path: src/api/**)
(Handler-specific guidance)

./project/CLAUDE.md:
(Just project-wide context, tech stack, shared agreements)
```

**Rule:** If guidance is file-type or path-specific, use rules with path scoping.

## 8. Writing Instructions in Narrative Form Instead of Scannable Bullets

**Mistake:**
```
Testing Strategy

Our approach to testing is comprehensive and thoughtful. We write tests for all
critical paths, and we aim for high coverage metrics. Tests should verify behavior,
not implementation. When you write a test, think about what the user or caller is
trying to do, and test that outcome. Mock external dependencies to keep tests fast
and isolated. Before writing a test, think about edge cases and error conditions.
Run the full test suite before committing to catch regressions. Test names should
clearly describe what scenario is being tested.
```

**Problem:**
- Requires parsing to extract the actual rules
- Dense paragraph format is slow to scan
- Hard to reference ("what did it say about mocking?")
- More tokens than needed

**Solution:** Scannable bullets.

**Fixed version:**
```
Testing:
- Test critical paths and error cases
- Verify behavior, not implementation
- Mock external dependencies (faster, isolated)
- Test names: describe the scenario clearly
- Run full suite before commit
- Target coverage: ≥80%
```

**Rule:** Instructions should be scannable in <10 seconds.

## 9. Including Explanatory Examples Meant for Humans That Waste Tokens

**Mistake:**
```
Commit Messages

We use present tense for commit messages. This keeps our history consistent and
readable. For example, instead of writing "Added feature X", write "Add feature X".
Instead of "Fixed bug in payment", write "Fix bug in payment flow". This style matches
the imperative mood, as if telling git what to do. Here are some good examples:
- "Add user authentication"
- "Fix race condition in checkout"
- "Refactor payment processor"
- "Update dependencies"

And here are some bad examples to avoid:
- "Added user authentication"
- "Fixed race condition in checkout"
- "Refactored payment processor"
- "Dependencies updated"

The reason we use this style is that it reads naturally in changelogs and makes history
easier to understand at a glance.
```

**Problem:**
- ~70 tokens for a 5-word rule: "Use present tense in commit messages"
- Human-facing explanations waste Claude's context
- Examples are redundant (Claude understands "Add" vs "Added")
- Explanatory prose doesn't improve output

**Solution:** Instruction only, no explanatory content.

**Fixed version:**
```
Commit messages: present tense imperative. "Add" not "Added", "Fix" not "Fixed".
```

(Fewer tokens, same outcome.)

**When to keep examples:**
- The rule is counterintuitive or non-obvious
- Ambiguity without the example (e.g., "kebab-case not snake_case" — visual)
- The example shows a subtle distinction

## 10. Not Updating Instructions As Workflows Evolve

**Mistake:**
```
./project/CLAUDE.md (last updated 6 months ago):
"We're migrating from REST to GraphQL. Currently 60% done.
Use REST for now; ask before using GraphQL.
This guidance will change in Q2."

(It's now Q4. That guidance is stale.)

Also:
"Node 16 support required (older clients). Avoid async/await."
(But the team dropped Node 16 support last month.)

Also:
"Use Jest for testing. We're evaluating Vitest."
(Vitest was chosen 3 months ago. Jest is no longer in use.)
```

**Problem:**
- Claude follows stale guidance
- Team argues about rules ("but CLAUDE.md says...")
- Guidance drifts from practice
- Waste tokens on deprecated patterns

**Solution:** Treat CLAUDE.md like code: maintain it actively.

**Best practice:**
- Review on code review (PRs to CLAUDE.md)
- Assign an owner to update quarterly
- Remove deprecated guidance immediately
- Document decision date for recent major changes
- Use auto memory to track interim decisions

**Example (fixed):**
```
<!-- Updated: 2026-03-20. Next review: 2026-06-20 -->

Tech stack:
- Node 18+ (dropped 16 in Feb 2026)
- Vitest (switched from Jest in Dec 2025)
- GraphQL only (REST deprecated, removed Oct 2025)
```

## 11. Creating Instruction Layers Without Understanding Precedence

**Mistake:**
```
~/.claude/CLAUDE.md:
"Always use camelCase for variable names."

project/CLAUDE.md:
"Use snake_case for variable names to match the Python backend."

Result: Claude is inconsistent. Some variables camelCase, some snake_case.
The team doesn't know why, and both files claim to be right.
```

**Problem:**
- Precedence is unintuitive; team doesn't know which wins
- No one realizes there's a conflict until code review
- Debugging why a rule "didn't work" is hard

**Solution:** Understand and document precedence.

- Project rules > user rules
- More specific path rules > general rules
- Subdirectory rules > project rules

**Fixed version:**
```
~/.claude/CLAUDE.md:
(Remove variable naming — too project-specific)

project/CLAUDE.md:
"Variables: snake_case (match Python backend)."

Result: Clear, no conflicts, precedence is obvious.
```

See `layers.md` for full precedence chart.

## 12. Duplicating Instructions Across Multiple Rule Files

**Mistake:**
```
project/.claude/rules/api.md:
"Always parameterize SQL to prevent injection."

project/.claude/rules/database.md:
"SQL: parameterize all queries."

project/.claude/rules/security.md:
"SQL injection prevention: use parameterized queries."

~/.claude/rules/security.md:
"Parameterize database queries."
```

**Problem:**
- Same rule in 4 places
- Wasted tokens (loaded multiple times)
- Maintenance nightmare: update once, remember to update 3 other places
- Impossible to keep in sync

**Solution:** Rule lives in one place; others reference it.

**Fixed version:**
```
project/.claude/rules/sql-safety.md:
<!-- Applies to all SQL code in project -->
"SQL:
- Parameterize all queries (prevent injection)
- Index WHERE clause columns
- Batch queries (avoid N+1)"

~/.claude/rules/database-conventions.md:
"See project rules for SQL guidance (applies everywhere)."

(Or just delete duplicates.)
```

## Prevention Checklist

Before committing CLAUDE.md or rules:

- [ ] No global CLAUDE.md rule applies to only one project
- [ ] No vague instructions ("good code", "best practices")
- [ ] No contradictions between layers or rules
- [ ] Formatting is minimal (bullets not prose)
- [ ] No personal preferences in project files
- [ ] Every rule is checkable by code review
- [ ] File-type guidance uses path-scoped rules
- [ ] Instructions are scannable (<10 seconds)
- [ ] Examples only where rule is ambiguous without them
- [ ] No stale or deprecated guidance
- [ ] Precedence between layers is clear
- [ ] No duplicate rules across files
- [ ] CLAUDE.md updated in last 6 months (or noted as stable)

## Debugging Ignored Instructions

**If Claude ignores a rule:**

1. **Verify it loaded.** Run `claude-opz debug --config` and check output.
2. **Check precedence.** A higher-priority layer may override it.
3. **Check path matching.** Rule files with `paths:` may not apply to the file being edited.
4. **Verify the rule is clear.** If instruction is vague, Claude may "miss" it.
5. **Check auto memory truncation.** If over 200 lines, older content is dropped.
6. **Review CLAUDE.md size.** If over 250 lines, consider refactoring.

Common fix: Move rule to higher-priority layer or clarify the instruction.

## Next Steps

See also:
- **Layers** (`layers.md`): Understanding where instructions go and why
- **Token Optimization** (`token-optimization.md`): Writing efficient instructions
