---
name: pr-review
description: "Reviews staged git changes for bugs, security issues, and code quality. Use when the user asks to review code, check a diff, or audit staged changes before committing."
---

# PR Review

## Context

Staged changes to review:

```
!`git diff --cached`
```

## Instructions

Review every staged file. For each change, evaluate against these criteria:

1. **Correctness** — Logic errors, off-by-one mistakes, unhandled edge cases (e.g. None/null inputs), wrong return types
2. **Security** — SQL injection, XSS, command injection, hardcoded secrets, unsafe deserialization, missing input validation at trust boundaries
3. **Style** — Poor variable names (single letters, ambiguous abbreviations), unused imports, dead code, inconsistent formatting
4. **Performance** — Unnecessary allocations in loops, missing indexes for queries, N+1 patterns, blocking calls that should be async

Be precise. Cite the exact line and explain *why* it is a problem, not just *what* the problem is. If a file has no issues, say so — do not invent findings.

## Output Format

### Summary

One sentence describing the overall quality of the changes.

### Findings

| Severity | File | Line | Issue |
|----------|------|------|-------|
| ...      | ...  | ...  | ...   |

Severity levels: `critical` (must fix — security or data loss), `warning` (likely bug), `info` (style or minor improvement).

### Verdict

State one of:
- **Ship it** — No critical or warning findings.
- **Fix before merge** — Has critical or warning findings. List the must-fix items.
