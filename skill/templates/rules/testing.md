<!-- Path-Scoped Rule: Testing Standards
Place this in: .claude/rules/testing.md in your repo
Purpose: Define test structure, coverage expectations, and quality gates
Applies to: Test files matching paths in frontmatter below

---
paths:
  - "**/*.test.js"
  - "**/*.test.ts"
  - "**/*.spec.js"
  - "**/*.spec.ts"
  - tests/**/*
  - __tests__/**/*
---

Testing is a contract with future maintainers, not check-box coverage.

**Unit tests**: Test *behavior*, not implementation. Mock external dependencies. One assertion per test preferred—multiple assertions OK if they describe one behavior.

**Integration tests**: Real [DATABASE_ENV], [API_ENDPOINT]. Test workflows, not individual functions. Cleanup after each test (transactions rollback or fixtures reset).

**Test names**: Describe what should happen, not the code path. Bad: `testAdd`. Good: `shouldReturnSumWhenGivenTwoNumbers`.

**Assertions**: Use [TESTING_FRAMEWORK] matchers. Assertion messages should help diagnose failure in CI without digging code.

**Flakiness**: Tests must pass consistently. No sleeps/timeouts as workarounds—fix race conditions. Use test fixtures for deterministic data.

**Coverage gates**: Minimum [COVERAGE_THRESHOLD]% across statements/branches/lines. Report coverage diffs in PRs. Exclude: generated code, vendored code, test helpers.

**Speed**: Unit tests <100ms. Integration tests <1s each. If slower, parallelize or refactor into smaller tests.

**CI/CD**: All tests run on every push to feature branches. Failures block merge. Success is prerequisite for code review, not substitute for it.
