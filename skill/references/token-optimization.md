# Token Optimization for Claude Instructions

Every token in a CLAUDE.md or rule file loads on every Claude invocation. Token waste compounds quickly and directly reduces context available for your actual work.

## The Math: Why This Matters

```
Baseline: 50 excess tokens in instructions
Invocations per day: 100 (typical agent or pair-programmer usage)
Excess tokens per day: 50 × 100 = 5,000 wasted tokens

Over a month (22 working days):
5,000 × 22 = 110,000 wasted tokens

At standard Claude pricing ($3/MTok input):
110,000 / 1,000,000 × $3 = $0.33/month

But the real cost is context: those 5,000 tokens/day could be used for:
- 10 additional file reads in a session
- More detailed problem analysis
- Richer conversation history before cutoff
- Longer solution explanations
```

For large teams, this scales:
- Team of 50: 110K tokens → context bloat for 50 people's daily work
- Conversions to smaller models: 5K extra tokens may push you out of Haiku tier to Sonnet

**Rule:** Optimize instructions as aggressively as you optimize code. Treat every token like you'd treat a function call.

## Token Optimization Techniques

### 1. Remove Filler Words and Qualifiers
**Before:**
```
It would be really helpful if you could please try to write clean, well-organized code
that is maintainable and easy to understand for other team members who might read it later.
```

**After:**
```
Write clean, maintainable code.
```

**Savings:** 18 tokens → 5 tokens (-72%)

Removed:
- "It would be", "really helpful if", "please try to", "could"
- "well-organized" (redundant with "clean")
- "that is" (weak construction)
- "for other team members who might read it later" (obvious implication)

### 2. Use Telegraphic Style for Instructions
Instruction prose doesn't need natural English. Use fragments.

**Before:**
```
When you are writing error messages for the user to see, you should always make sure
that they are specific and actionable. The user should be able to understand what went
wrong and what they can do about it.
```

**After:**
```
Error messages: specific, actionable, tell user what went wrong and how to fix it.
```

**Savings:** 40 tokens → 14 tokens (-65%)

### 3. Use Headers and Bullets Instead of Prose
Headers are scannable; prose requires parsing.

**Before:**
```
There are several important things to keep in mind when writing database queries.
First, you should always parameterize queries to prevent SQL injection attacks.
Second, avoid N+1 queries by using joins or batch loading. Third, make sure indexes
exist on columns used in WHERE clauses. Finally, test queries with realistic data volumes.
```

**After:**
```
Database queries:
- Parameterize all queries (prevent SQL injection)
- Avoid N+1 (use joins/batch loading)
- Index WHERE clause columns
- Test with realistic data volume
```

**Savings:** 60 tokens → 18 tokens (-70%)

### 4. Cut Examples That Don't Add Signal
Examples are expensive. Use only when the instruction is genuinely ambiguous without one.

**Before:**
```
Use present tense in commit messages. For example, "Add feature X" instead of "Added feature X",
or "Fix bug in payment flow" instead of "Fixed bug in payment flow". This keeps commit history
consistent and readable.
```

**After:**
```
Commit messages: present tense ("Add feature" not "Added feature").
```

**Savings:** 47 tokens → 11 tokens (-76%)

When to keep examples:
- The instruction is non-obvious or counterintuitive
- Ambiguity would cause frequent misinterpretation
- Example shows a subtle distinction (e.g., "snake_case not Snake_Case" — visual)

### 5. Remove Redundant Instructions Across Layers
Duplication across user CLAUDE.md, project CLAUDE.md, and rules wastes context.

**Anti-pattern:**
```
~/.claude/CLAUDE.md:
"Use Python 3.11+ with type hints. All functions must have type annotations."

project/CLAUDE.md:
"This project uses Python 3.11 with strict type checking enabled.
All functions and variables must be annotated with types."

project/.claude/rules/typing.md:
"Type annotations are required on all functions and module-level variables.
Use typing module for complex types. No Any unless justified."
```

**Solution:** Keep rule in one place. Reference it from others.

```
~/.claude/CLAUDE.md:
"Python: type hints mandatory. See project rules for specifics."

project/.claude/rules/typing.md:
"Type annotations: all functions and module-level vars. typing module for complex types.
No Any without justification comment."
```

### 6. Remove "Nice to Have" Instructions
Instructions that don't affect output waste tokens.

**Example (remove):**
```
Try to be helpful and friendly in code comments.
Consider the developer experience when writing error messages.
Think about performance even if it's not explicitly required.
```

**Why:** These are expectations, not instructions. They don't change Claude's behavior; they're noise.

**Keep:** Only instructions where Claude must choose, and the choice matters.

### 7. Use Abbreviations and Shorthand
Once established, abbreviations save tokens.

**Before:**
```
For error handling, follow this pattern: catch the exception, log it to the application
logging system, then return a meaningful error response to the client.
```

**After:**
```
Error handling: catch → log → return response.
```

Define abbreviations once at the top; reuse throughout.

### 8. Collapse Related Instructions
Combine similar rules to reduce repetition.

**Before:**
```
Use meaningful variable names. Use meaningful function names. Use meaningful class names.
All names should reflect their purpose.
```

**After:**
```
Names reflect purpose: variables, functions, classes, modules.
```

### 9. Remove Explanatory Context
Claude doesn't need to understand *why* a rule exists; it needs the rule.

**Before:**
```
We use Postgres instead of MongoDB because we have complex relational queries,
ACID transactions are critical for financial accuracy, and the team has deep expertise
in SQL. Therefore, all data should be stored in Postgres with normalized schemas.
```

**After:**
```
Postgres only. Normalized schemas. No document stores.
```

**Exception:** Keep *why* if it clarifies a non-obvious choice or helps Claude make decisions:
```
Typescript strict mode enabled (catches type errors before runtime). Stricter than you might expect.
```

### 10. Use YAML/Structured Data
Structured data is denser than prose.

**Before:**
```
When naming files, use lowercase with hyphens for web assets, snake_case for Python modules,
and PascalCase for React components. TypeScript files use PascalCase. Configuration files
use kebab-case.
```

**After:**
```
Naming conventions:
- Web assets: kebab-case (app-banner.css)
- Python: snake_case (helper_module.py)
- React components: PascalCase (Button.tsx)
- Config: kebab-case (webpack-config.js)
```

## Token Trade-Offs: Too Terse vs. Too Verbose

### Over-Optimization Risk: Too Terse
Overly terse instructions can backfire.

**Too terse:**
```
Code: clean, typed, tested.
```
(Claude might interpret "tested" as "has been run" not "has test coverage")

**Better:**
```
Code: clean, fully typed, test coverage >80%.
```

**Rule of thumb:** If you'd have to explain it verbally, it's too terse.

### Too Verbose: Wasted Context
Every extra sentence reduces space for actual work.

**Verbose:**
```
We believe that it is very important to write code that is easy for other developers
to understand and maintain. To achieve this goal, we recommend following best practices
such as meaningful naming, clear structure, and comprehensive documentation.
Therefore, when writing code, please keep these principles in mind.
```

**The instruction:** Write maintainable code.

(Everything else is fluff.)

## The Sweet Spot: Concrete, Verifiable, Minimal

Good instructions have these properties:

| Property | Meaning | Example |
|----------|---------|---------|
| **Concrete** | Specific enough to act on; no vague terms | "Error messages: max 100 chars" not "Write good errors" |
| **Verifiable** | Someone could check if it was followed | "Type coverage >90%" not "Be thorough with types" |
| **Minimal** | No filler; every word counts | "Parameterize SQL" not "Make sure you parameterize SQL to prevent injection" |
| **Actionable** | Tells Claude what to do, not what to think | "Use async/await" not "Think about async" |

**Test:** Can you check a code diff and verify compliance? If not, the instruction is too vague.

## HTML Comments: Notes for Humans
Use HTML comments in CLAUDE.md for human-readable explanation. They're stripped before loading.

```markdown
<!-- This team chose Postgres for complex relational queries and ACID guarantees.
     If we ever need to revisit, check Q3 2024 architecture decision log. -->

Data storage: Postgres only. Normalized schemas.
```

**Cost:** Zero. Comments are stripped before loading into Claude.

**Use for:**
- Rationale and context (too verbose for instruction format)
- TODOs and reminders to the team
- Links to design docs or decision logs
- Temporarily disabled rules

## Optimization Checklist

Before finalizing CLAUDE.md or rules:

- [ ] Every instruction is actionable (verifiable by code review)
- [ ] No filler words ("really", "please", "try to", "could")
- [ ] No redundant instructions across layers
- [ ] Examples only where ambiguity exists without them
- [ ] Prose replaced with headers/bullets where possible
- [ ] Abbreviations used consistently after definition
- [ ] "Nice to have" expectations removed (keep only must-do rules)
- [ ] CLAUDE.md under 200 lines (soft target)
- [ ] User CLAUDE.md applies to all projects (high bar for inclusion)
- [ ] Specific rules moved to `.claude/rules/` files
- [ ] Explanatory context moved to HTML comments

## Real-World Example: Before & After

### Before (250 tokens)
```markdown
# Code Quality Standards

This team is committed to writing clean, well-organized code that is easy to understand
and maintain. We believe that code is read much more often than it is written, so we should
prioritize readability above all else.

## Naming
Please use meaningful names for all variables, functions, and classes. The names should
clearly indicate the purpose of the entity. For example, instead of calling a variable "x",
consider calling it "user_count". Instead of "fn", call it "calculate_total".

## Testing
All code should be tested thoroughly. We aim for at least 80% code coverage. Tests should
be clear and test one thing at a time. You should avoid writing tests that are too broad
or test multiple things at once, as they are harder to debug when they fail.

## Types
Our codebase uses TypeScript with strict mode enabled. This means all variables, functions,
and return types must be explicitly typed. No implicit any is allowed.
```

### After (75 tokens)
```markdown
# Code Quality

Names reflect purpose: user_count not x, calculate_total not fn.

Testing:
- Test coverage ≥80%
- One assertion per test case
- Clear test names (describe expected behavior)

TypeScript strict mode enforced:
- All types explicit
- No implicit any
- No any without justification comment
```

**Savings:** 250 → 75 tokens (-70%)

**Loss:** Only explanatory prose (moved to team wiki or decision log).

## Performance Impact Across Team Size

| Team Size | Extra Tokens/Day | Context Impact | Optimization ROI |
|-----------|------------------|-----------------|-------------------|
| 1 person | 5K | Low | Low (still worth it) |
| 5 people | 25K | Noticeable | Medium |
| 20 people | 100K | Significant | High (compound effect) |
| 50+ people | 250K+ | Major | Critical |

For large organizations, token optimization of shared CLAUDE.md files has measurable impact on conversation quality and cost.

## When Token Optimization Is Wrong

Don't over-optimize:
- **Clarity matters more than brevity.** If an instruction is unclear when terse, expand it.
- **New team members.** If instructions need to onboard people, add helpful context.
- **Rarely-changed core rules.** If a rule is the foundation of your practice, clarity > tokens.

Optimization targets high-frequency, low-ambiguity instructions: naming, formatting, testing thresholds.

## Next Steps

See also:
- **Layers** (`layers.md`): Where to put instructions for best efficiency
- **Common Mistakes** (`common-mistakes.md`): Patterns that waste tokens (and lead to ignored instructions)
