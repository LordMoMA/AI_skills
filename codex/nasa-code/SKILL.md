---
name: nasa-code
description: Audit code against NASA JPL's Power of Ten rules, extended for AI-assisted development. Use when checking code for dangerous complexity, unsafe control flow, resource leaks, hidden side effects, missing validation, weak CI gates, AI-generated artifacts, or missing failure-mode tests.
metadata:
  short-description: Safety-critical code audit
---

# NASA Code Audit

You are auditing code against a 12-rule discipline adapted from Gerard Holzmann's
"Power of Ten" rules for safety-critical software at NASA/JPL, extended for
AI-assisted development.

## Scope

If the user gives a target, audit that file, directory, glob, staged diff, or branch diff.
Otherwise:

1. If in a git repo with staged changes, audit `git diff --cached`.
2. Else if on a non-default branch, audit `git diff <default-branch>...HEAD`.
3. Else ask the user what to audit.

Read the actual code. Do not audit from filenames alone.

## The 12 Rules

For each violation, cite `file:line` and include the offending snippet.

### Control & Structure

1. **Keep it linear** - No deep nesting (>2 levels), no recursion without an explicit base-case bound, no control flow that requires a diagram. Severity: high.
2. **Bound every loop** - Every loop must have an explicit, enforceable maximum iteration count. Polling, retry, and crawl loops without caps are critical.

### Memory & Resources

3. **Know what you own** - Every opened resource (file handle, DB connection, lock, socket, subscription) must be closed on every exit path, including errors. Missing cleanup on an error path is critical.
4. **One function, one job** - Functions >60 lines, or describable only with "and", are medium. Flag for decomposition.

### Correctness & Observability

5. **State your assumptions** - Preconditions, invariants, and contracts should be asserted in code, not just documented. Missing validation at trust boundaries is high.
6. **Never swallow errors** - `except: pass`, empty catch blocks, ignored return codes, and discarded `err` values are critical. Every failure must be logged, raised, or explicitly returned.
7. **Narrow your state** - Module-level globals, wide-scope mutable state, and class-level state used as a passthrough are medium. Prefer local scope and explicit parameters.
8. **Surface your side effects** - I/O, network calls, and mutations hidden inside innocuously named helpers are high. Side effects should be visible at the call site.

### Abstraction & Indirection

9. **One layer of magic** - Stacked decorators, middleware chains, dynamic dispatch, or callback chains that obscure what actually runs are medium. Favor readable composition.
10. **Warnings are errors** - Check linter, typechecker, and static-analysis configuration. If absent or advisory rather than blocking, mark high. Missing CI gate is high.

### Working With AI

11. **Read every line** - Look for telltale AI-generated patterns left unreviewed: dead code, unused imports, commented-out blocks "just in case", overly defensive try/catch wrapping trivial operations, and generic variable names (`data`, `result`, `temp`) in critical paths. Severity: medium.
12. **Tests first** - Check whether changed code has corresponding tests. Production code without tests covering at least one failure mode is high.

## Severity

- Critical - silent data loss, resource leak, swallowed error, unbounded production loop.
- High - likely bug, missing invariant check, surprising side effect.
- Medium - maintainability hazard or comprehension cost.
- Low - style or clarity issue.
- Info - observation or good practice noted.

## Output

Return the audit in this structure:

````markdown
# NASA Power-of-Ten Audit

**Target:** <what you audited>
**Files reviewed:** N

## Findings

### Critical (N)
- `path/to/file.ext:LINE` - Rule N (name) - one-line issue
  ```lang
  offending snippet
  ```
  Fix: concrete suggestion.

### High (N)
...

### Medium (N)
...

## Rule-by-rule summary
| Rule | Status |
|------|--------|
| 1. Keep it linear | Pass / N violations |
| ... | ... |

## Verdict
One paragraph: is this code safe to ship? What must change first?
````

## Guidelines

- Be specific. "`handleRequest` at `api.py:240` is 142 lines and mixes auth, validation, and persistence; split it into three functions" is useful.
- Cite line numbers from the actual file, not only from the diff.
- Do not invent violations. If the code is clean for a rule, say so.
- Do not flag stylistic preferences as rule violations. Each finding must trace back to one of the 12 rules.
- For tests-first, if the change is config, docs, or types only, mark rule 12 as N/A rather than flagging.
- If the codebase has an existing lint or CI setup, check that it enforces failures with a nonzero exit code.
- Skip generated code, vendored dependencies, and migrations unless the user explicitly includes them.
