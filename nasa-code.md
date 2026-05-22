---
description: Audit code against NASA JPL's Power of Ten rules (extended for AI-assisted development) — flags violations across control flow, resources, correctness, abstraction, and AI hygiene.
---

You are auditing code against a 12-rule discipline adapted from Gerard Holzmann's "Power of Ten" rules for safety-critical software at NASA/JPL, extended for AI-assisted development.

## Scope

If the user passed arguments, treat them as the target (file path, directory, glob, or `staged`/`branch`). Otherwise:
1. If in a git repo with staged changes → audit `git diff --cached`
2. Else if on a non-default branch → audit `git diff <default-branch>...HEAD`
3. Else → ask the user what to audit. Do not guess.

Read the actual code. Don't audit from filenames alone.

## The 12 Rules

For each violation, cite `file:line` and quote the offending snippet. Use the severity tags below.

### Control & Structure
1. **Keep it linear** — No deep nesting (>2 levels), no recursion without an explicit base-case bound, no control flow that requires a diagram. 🟠
2. **Bound every loop** — Every loop must have an explicit, enforceable maximum iteration count. Polling/retry/crawl loops without caps are 🔴.

### Memory & Resources
3. **Know what you own** — Every opened resource (file handle, DB connection, lock, socket, subscription) must be closed on every exit path, including errors. Missing cleanup on the error path is 🔴.
4. **One function, one job** — Functions >60 lines, or describable only with "and", are 🟡. Flag for decomposition.

### Correctness & Observability
5. **State your assumptions** — Preconditions, invariants, and contracts should be asserted in code, not just documented. Missing validation at trust boundaries is 🟠.
6. **Never swallow errors** — `except: pass`, empty catch blocks, ignored return codes, discarded `err` values: 🔴. Every failure must be logged, raised, or explicitly returned.
7. **Narrow your state** — Module-level globals, wide-scope mutable state, and class-level state used as a passthrough are 🟡. Prefer local scope + explicit parameters.
8. **Surface your side effects** — I/O, network calls, and mutations hidden inside innocuously-named helpers are 🟠. Side effects should be visible at the call site.

### Abstraction & Indirection
9. **One layer of magic** — Stacked decorators, middleware chains, dynamic dispatch, or callback chains that obscure what actually runs: 🟡. Favor readable composition.
10. **Warnings are errors** — Check for linter/typechecker/static-analysis configuration. If absent or configured as advisory rather than blocking: 🟠. Missing CI gate: 🟠.

### Working with AI
11. **Read every line** — Look for telltale AI-generated patterns left unreviewed: dead code, unused imports, commented-out blocks "just in case", overly defensive try/except wrapping trivial ops, generic variable names (`data`, `result`, `temp`) in critical paths. 🟡
12. **Tests first** — Check whether the changed code has corresponding tests. Production code without tests covering at least one failure mode: 🟠.

## Severity

- 🔴 **Critical** — silent data loss, resource leak, swallowed error, unbounded loop in production path
- 🟠 **High** — likely-bug, missing invariant check, side-effect surprise
- 🟡 **Medium** — maintainability hazard, comprehension cost
- 🟢 **Low** — style/clarity nit
- ℹ️ **Info** — observation, good practice noted

## Output

Print to the terminal (do not write a file unless the user asks). Structure:

```
# NASA Power-of-Ten Audit

**Target:** <what you audited>
**Files reviewed:** N

## Findings

### 🔴 Critical (N)
- `path/to/file.ext:LINE` — Rule N (name) — one-line issue
  ```lang
  offending snippet
  ```
  Fix: concrete suggestion.

### 🟠 High (N)
...

### 🟡 Medium (N)
...

## Rule-by-rule summary
| Rule | Status |
|------|--------|
| 1. Keep it linear | ✅ / N violations |
| ... | ... |

## Verdict
One paragraph: is this code safe to ship? What must change first?
```

## Guidelines

- Be specific. "This function is too long" is useless; "`handleRequest` at api.py:240 is 142 lines and mixes auth, validation, and persistence — split into three" is useful.
- Cite line numbers from the actual file, not the diff.
- Don't invent violations. If the code is clean for a rule, say so.
- Don't flag stylistic preferences as rule violations. Each finding must trace back to one of the 12 rules.
- For tests-first (rule 12): if the change is config/docs/types-only, mark N/A rather than flagging.
- If the codebase has an existing lint/CI setup, check it actually enforces (exit-code-failing), not just runs.
- Skip generated code, vendored deps, and migrations unless the user explicitly includes them.

Now perform the audit.
