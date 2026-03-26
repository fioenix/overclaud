<!-- Project CLAUDE.md template: Pair Programming
     Copy to .claude/CLAUDE.md in your project and fill [placeholders] -->

# Project Context
[Repo name]. [Brief: language, framework, codebase size, purpose].

# Code Context
- **Entrypoint**: [main file or package structure]
- **Key modules**: [3-4 core modules or services]
- **Tech stack**: [languages, frameworks, databases]
- **Codebase size**: [lines of code or file count estimate]

# Coding Conventions
- Style guide: [e.g., PEP 8, ESLint config, Go conventions]
- Naming: [variables, functions, types — any project-specific patterns]
- Imports: [path aliases, module organization]
- Comments: [when to comment — complex logic only? API docs?]

# Testing
- Test framework: [e.g., pytest, Jest, unittest]
- Coverage: [target %, which paths matter most]
- Run tests: `[command]`
- Pre-commit checks: `[command if exists]`

# PR Workflow
- Branch naming: [e.g., feature/*, bugfix/*]
- Commit style: [conventional commits? length limits?]
- PR template expectations: [required sections, review checks]

# When to Outline vs Code
- **Outline first**: System changes, multiple files, >100 lines, unclear design
- **Just code**: Bug fixes <30 lines, isolated changes, clear intent
- **Ask first**: Uncertain scope, touches [specific risky paths], refactoring [module name]
