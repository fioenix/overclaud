<!-- Project CLAUDE.md template: Architecture & System Design
     Copy to .claude/CLAUDE.md in your project and fill [placeholders] -->

# System Context
[System name]. [High-level: what it does, who uses it, scale].

# Architecture Overview
- **Current state**: [diagram location or ASCII sketch]
- **Key services/modules**: [list with brief purpose]
- **Data flow**: [overview — databases, caches, external systems]
- **Deployment**: [infrastructure — K8s, serverless, on-prem, etc.]

# Design Documentation
- **ADR location**: [where architecture decision records live]
- **ADR template**: [link or inline — if using custom format]
- **Diagram format**: [C4, Mermaid, draw.io — what we use]
- **Last major update**: [date and what changed]

# Constraints & Trade-offs
- **Cost constraints**: [budget, acceptable $/request, who tracks it]
- **Team constraints**: [team size, skill gaps, time pressure]
- **Compliance**: [GDPR, PCI, SOC2, etc. — what impacts design]
- **Performance targets**: [latency, throughput, uptime SLOs]
- **Technical debt**: [known issues, refactoring priorities]

# When to Propose vs Execute
- **Propose first**: [list decision categories — service adds, DB changes, etc.]
- **Execute**: [low-risk categories — config, monitoring, docs]
- **Ask if uncertain**: [unclear scope, cross-service impact]

# Key Assumptions
[List critical assumptions about load, reliability, user behavior — what would break if wrong]
