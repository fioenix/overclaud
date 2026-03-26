<!-- Path-Scoped Rule: API Design Standards
Place this in: .claude/rules/api-design.md in your repo
Purpose: Enforce consistent, maintainable API patterns
Applies to: API routes/handlers matching paths in frontmatter below

---
paths:
  - src/api/**/*
  - app/api/**/*
  - routes/**/*
  - controllers/**/*
  - handlers/**/*
---

API design is contract surface area—breaking changes propagate downstream immediately.

**Versioning**: URL path versioning (/v1/, /v2/) for major breaking changes. Minor changes backward-compatible (new optional fields, new endpoints). Deprecation window: [DEPRECATION_MONTHS] months min.

**HTTP semantics**: GET (read, no side effects), POST (create), PUT (full replace), PATCH (partial update), DELETE (remove). Match semantics—don't use GET for mutations, POST for retrieval.

**Status codes**: 200 (success), 201 (created), 204 (no content), 400 (bad request), 401 (auth), 403 (forbidden), 404 (not found), 409 (conflict), 5xx (server error). Use standard codes—don't invent.

**Errors**: Structured response with `code` (machine-readable), `message` (human), `details` (context). Example: `{ code: "INVALID_EMAIL", message: "...", details: {...} }`.

**Pagination**: Cursor-based preferred over offset (scales better). Include `pageInfo: { hasNextPage, endCursor }` in response.

**Authentication**: Bearer token in Authorization header. Refresh token rotation if session-based. Rate limiting by user/IP: [RATE_LIMIT_SPEC].

**Documentation**: OpenAPI/Swagger spec auto-generated from code. Every endpoint documented: path, method, auth, request schema, response schema, error codes.

**Backward compatibility**: New fields optional. Don't rename/remove without deprecation. Client-driven versioning: support old formats while encouraging upgrade.

**Testing**: API contract tests alongside unit tests. Verify request validation, response schema, error handling, rate limits.
