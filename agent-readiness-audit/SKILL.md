---
name: agent-readiness-audit
description: "Use when asked to audit a repo's readiness for AI coding agents, or to review its agent instructions (AGENTS.md/CLAUDE.md), in-repo skills, lint guardrails, or commit/push gates. Read-only audit that outputs a report."
---

# Agent readiness audit

Audit the current repository against the checklist below and report gaps. This is a READ-ONLY audit: never modify, create, or delete repo files.

## Step 1: Discover the stack

Before judging anything, identify:

- Languages, frameworks, package manager, monorepo structure
- Linter, formatter, and typechecker in use
- Test runner
- Hook manager (husky, lefthook, pre-commit, simple-git-hooks, plain .git/hooks)
- CI system and its config
- Agent-instruction files (AGENTS.md, CLAUDE.md, .cursorrules, copilot-instructions, etc.)
- Directories containing skills, runbooks, prompts, or workflow docs

Evaluate using the tools this repo actually uses. Do not recommend swapping tools unless the current ones can't do the job.

## Step 2: Evaluate each checklist item

Assign one status per item:

- PASS: clearly in place and working
- PARTIAL: exists but incomplete, outdated, unenforced, or inconsistent
- MISSING: not present
- N/A: doesn't apply to this stack (say why)

Back every status with evidence: file paths, config keys, or short quoted snippets (under 15 words each). If there is no evidence, mark MISSING rather than guessing.

### Checklist

1. Shared agent instructions
   - Single root-level instruction file read by all agents and new engineers
   - Covers code style rules (export style, import structure, file organization, anti-patterns)
   - Requires validating external data at trust boundaries
   - Says which tests are worth writing
   - Defines a done gate: lint, typecheck, and relevant tests must pass before commit
   - Is current and specific. Spot-check 3 to 5 rules against the actual code and report whether the code follows them.
2. Workflow skills in the repo
   - Recurring workflows documented as step-by-step skills or runbooks beside the code
   - Coverage: commits, opening/stacking PRs, code review, getting a PR through CI, dependency changes, localization
   - Stack-specific workflows covered (data layer, router, design system, codegen, etc.)
   - Each skill has a trigger, ordered steps, and a definition of done
   - Skills are the single source of truth, not duplicated or contradictory
3. Conventions enforced as lint rules
   - Documented conventions have matching lint rules
   - Custom lint rules or plugins cover project-specific usage (data fetching, styling, tests, translations)
   - Anti-pattern rules exist for common agent mistakes: chained or needless type assertions, loosely typed parameters (unknown/any), conditional spreads of empty objects, plus any recurring low-quality patterns found in this codebase
   - Rules are errors, not warnings, with no blanket disables
4. Automated gates
   - Pre-commit hook formats, lints, and typechecks changed code
   - Pre-push hook lints or tests affected packages, including dependents
   - CI re-runs the same checks so hooks can't be bypassed
   - Hooks are reasonably fast
5. Oversight and structure
   - Architecture decisions documented
   - PR review required before merge (check branch protection or CODEOWNERS if visible)
   - Package and module boundaries are clear and enforced

## Step 3: Find undocumented conventions

Sample the code for patterns applied consistently but written down nowhere, and for patterns agents commonly get wrong (loose typing, unvalidated external data, inconsistent exports, duplicated logic). List the top 5 candidates to add to the instruction file or turn into lint rules.

## Step 4: Report

Produce:

1. A summary table: item, status, one-line evidence
2. A "gaps" section: each PARTIAL or MISSING item, what's wrong, and why it matters for agent reliability
3. A prioritized recommendation list (highest-leverage first), each with a concrete next step and rough effort (small/medium/large), phrased in terms of this repo's existing tools
4. The top 5 candidate conventions from Step 3

Be direct. Don't pad with praise. If something is fine, say PASS and move on.

After the report, offer to scaffold the missing pieces (starter instruction file, skill templates, lint rule stubs), but do not create anything until asked.
