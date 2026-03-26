# Contributing to claude-opz

Thanks for wanting to make Claude work better for everyone.

## What We Need Most

**New templates** — the more roles and use cases covered, the more useful this becomes. Each template should be:

- Under 30 lines (global) or 25 lines (project)
- Token-optimized: no filler, every line carries signal
- Using `[placeholders]` for user-specific values
- Immediately usable after filling placeholders

## How to Contribute

1. Fork the repo
2. Create a branch (`feature/template-data-engineer`)
3. Add your template to the appropriate directory
4. Submit a PR with a brief description of the role/use case

## Template Quality Bar

Before submitting, check:

- [ ] Every instruction is actionable and verifiable
- [ ] No filler words ("really", "please", "try to")
- [ ] No overlap with what should be in a different layer
- [ ] Examples only where ambiguity exists without them
- [ ] Tested with Claude — does it actually change behavior?

## Reference Docs

If you find a mistake or want to improve a reference doc (`skill/references/`), PRs welcome. Keep the same style: practical, example-heavy, no fluff.

## Bug Reports

If the skill setup flow doesn't work correctly, open an issue with:

- Which Claude surface you used (Code, Cowork, Chat)
- What you expected vs what happened
- Any error messages

## Code of Conduct

Be kind. Be helpful. Skip the drama.
