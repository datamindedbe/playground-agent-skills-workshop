# Solution: PR Review

## Design choices

**Description with trigger words** — "Reviews staged git changes for bugs, security issues, and code quality. Use when the user asks to review code, check a diff, or audit staged changes before committing." Third person, says what AND when. Includes key terms (review, diff, staged, commit) that Claude matches against user intent.

**Dynamic context with `git diff --cached`** — Injects only the staged diff, not the entire repo. Claude sees exactly what the user is about to commit — nothing more, nothing less. This runs at invocation time so context is always fresh.

**Four specific review categories** — "Review this code" produces vague output. Naming correctness, security, style, and performance gives Claude a checklist to work through systematically. Each category includes concrete examples of what to look for (off-by-one, SQL injection, single-letter variables, N+1).

**"Explain why, not just what"** — The instruction to cite the line and explain *why* something is a problem forces Claude to produce actionable feedback, not just "this looks wrong."

**Severity levels** — Three tiers (critical/warning/info) let the user quickly triage. The definitions are explicit: critical = security or data loss, warning = likely bug, info = style. No ambiguity.

**Verdict section** — A clear ship-it/fix-before-merge decision at the end. The user gets a bottom-line answer without re-reading the findings table.

**No `allowed-tools`** — This skill only reads the injected diff context and produces text output. Claude doesn't need to run commands or read additional files, so there's no reason to restrict tools (and no risk in leaving them open).
