<!-- Path-Scoped Rule: Code Style Guide
Place this in: .claude/rules/code-style.md in your repo
Purpose: Enforce consistent code style and maintainability standards
Applies to: Source files matching paths in frontmatter below

---
paths:
  - src/**/*.js
  - src/**/*.ts
  - app/**/*.jsx
  - app/**/*.tsx
  - lib/**/*
---

Code style—not arbitrary taste, but readability and maintainability.

**Naming**: camelCase for functions/variables, PascalCase for classes/components. Names should express intent—avoid abbreviations unless ubiquitous (e.g., id, url). Commit message prefix with type: feat(), fix(), refactor(), test(), docs().

**Structure**: Max line length 100 chars (except URLs). Functions <50 lines; break longer logic into sub-functions. File size target: <300 lines.

**Comments**: Explain *why*, not what. If code needs explanation to understand *what*, refactor instead. JSDoc for public APIs only.

**Formatting**: Use [PROJECT_LINTER] config. No manual formatting debates—let tooling decide.

**Testing**: New code needs tests (unit and integration). Coverage floor: [COVERAGE_THRESHOLD]%.

**Dependencies**: Prefer built-in/standard lib. Justify external deps—vendor lock-in cost matters. Check license compatibility.

**Review gate**: Code review catches style gaps, but linting should catch most. If tests pass and linter passes, approve timing depends on code criticality, not style nits.
