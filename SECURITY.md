# Security Policy

## Reporting Vulnerabilities

If you discover a security vulnerability in overclaud, please report it responsibly:

1. **Do not** open a public GitHub issue
2. Email: tangduyphuong@gmail.com with subject "overclaud security"
3. Include: description, reproduction steps, potential impact

We aim to respond within 48 hours.

## Scope

overclaud is an instruction optimization skill. It:
- Reads and writes local files (CLAUDE.md, settings.json, rules/)
- Never makes external API calls or network requests
- Never collects, stores, or transmits user data
- Includes a PostToolUse hook that only outputs a hardcoded system message

## Hook Security

The auto-suggest hook processes `$TOOL_INPUT` through `jq` (JSON parser) before any string operations. User input never appears in the hook's output. The output is a hardcoded JSON system message.
