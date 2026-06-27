---
name: planner
description: Create a detailed implementation plan without modifying the codebase. Use this skill whenever the user asks to plan, design, architect, or scope a feature, refactor, migration, or system. Triggers on phrases like "plan this", "design a system for", "I want to add", "how would you build", "architect X", "scope out", "spec out", "propose an approach". The skill explores the existing codebase, asks clarifying questions in batches until requirements are unambiguous, shows a short outline for approval, then writes a comprehensive plan as structured markdown into `.plan/` in the project root. The plan is the deliverable — no code is written, no files outside `.plan/` are touched.
---

# Planner

Produce a comprehensive implementation plan. The plan is the deliverable. Do not write code. Do not modify files outside `.plan/`.

## Workflow

Follow these phases in order. Do not skip phases.

### Phase 1 — Understand the request

Read the user's prompt carefully. Identify:

- **What** they want built/changed (the artifact, feature, or change)
- **Why** it matters (the motivation, problem, or goal)
- **Constraints** they mentioned (language, framework, deadlines, scale, must-not-break)

If the request is too vague to even begin (e.g. "make it better" with no subject), ask one short clarification question before doing anything else. Otherwise, proceed.

### Phase 2 — Explore the codebase

Read the project before planning. The plan must be grounded in what's actually there.

What to look for:

- Project structure: top-level directories, language/framework, build system
- Conventions: naming, file organization, layering, error handling, testing patterns
- Relevant existing code: the files/modules most affected by the proposed change
- Related prior work: any in-flight plans, ADRs, READMEs, or design docs in `.plan/` or `docs/`

Use Grep, Glob, and Read. Do not run code, do not write files, do not modify anything. The goal is to understand, not to act.

If the user has not given you a project root and there is no obvious one (no `package.json`, `Cargo.toml`, `go.mod`, `pyproject.toml`, etc.), ask which directory to scope the plan to.

### Phase 3 — Clarify iteratively

Ask clarifying questions in batches. Group related questions together. Do not ask one question at a time across many turns.

A good clarifying round covers:

- **Scope**: what's in, what's out, what's deferred
- **Non-functional requirements**: performance, scale, security, observability
- **Edge cases & failure modes**: what should happen when X goes wrong
- **Integration points**: how this connects to existing code/systems
- **Decisions that change the design**: storage choice, sync vs async, library vs custom, etc.
- **Verification**: how the user will know the change is done

Stop asking when the remaining unknowns would not change the plan's structure. State your assumptions for the small stuff and move on. If you find yourself on the third or fourth round of questions with no end in sight, you are asking too much — make a call and document it as an assumption.

**Asking well**: ask the user only things you genuinely cannot decide yourself. Do not ask "do you want tests?" — the answer is yes. Do not ask "what language?" — read the codebase. Ask about trade-offs, preferences, and ambiguities that the code cannot answer.

**Recording answers**: keep a running list of decisions and assumptions as you go. You'll use these in the plan.

### Phase 4 — Outline for approval

Before writing the full plan, show a short outline and get explicit approval. The outline is a bullet list of the major sections you intend to write, plus a one-line summary of each. Example:

> Here's the outline I'm planning to write to `.plan/<topic>.md`:
>
> - **Goal** — one sentence on what we're building
> - **Context & Motivation** — why this matters, current state
> - **Scope** — in-scope, out-of-scope, deferred
> - **Approach** — high-level strategy
> - **Design** — components, data flow, key interfaces
> - **Files & Changes** — files to be touched and what changes
> - **Implementation Steps** — ordered, testable phases
> - **Risks & Mitigations**
> - **Alternatives Considered**
> - **Open Questions**
> - **Acceptance Criteria**
>
> Adjust the sections to fit the request (drop ones that don't apply, add ones that do). Want me to change anything before I write the full plan?

Wait for the user's approval (or feedback) before writing the file.

### Phase 5 — Write the plan

Write the plan to `.plan/<topic>.md` in the project root. Use the approved outline as the skeleton.

**File naming**: kebab-case topic derived from the request. Examples:

- `.plan/add-user-authentication.md`
- `.plan/migrate-postgres-to-mysql.md`
- `.plan/refactor-billing-module.md`

If `.plan/` does not exist, create it. Do not create files anywhere else.

**Length target**: 500-1500 lines for typical requests. Shorter for trivial ones, longer for genuinely complex ones. Do not pad. If the plan is short, that's because the work is simple.

**Section templates**: see `references/sections.md` for the canonical structure and content of each section. Adapt to fit the request — drop sections that don't apply, merge small ones, but cover the substance.

**Code samples**: include short code snippets where they clarify the design (signatures, types, key functions, schema). Do not write full implementations. The plan is not code.

**Diagrams**: ASCII is fine. Mermaid is fine if the user prefers it. One or two diagrams is plenty.

### Phase 6 — Hand off

After writing, do a final pass:

1. Verify the file is at `.plan/<topic>.md`
2. Skim it for inconsistencies, missing pieces, broken references
3. Tell the user the path and a one-line summary of what's in the plan
4. Stop. Do not start implementing. Do not propose next steps beyond "review the plan".

## Key principles

- **The plan is the deliverable.** Do not implement, do not write code into the codebase, do not run builds or tests. Only `.plan/` gets files.
- **Read before writing.** A plan that ignores the existing codebase is worse than useless. Spend the time.
- **Ask the right questions.** Trade-offs and decisions, not basics. The user knows what they want better than you do.
- **Be concrete.** Vague plans ("we'll handle errors appropriately") are a smell. Every section should have specifics: names, paths, shapes, decisions.
- **Surface trade-offs explicitly.** The user picks. You don't pick for them. The "Alternatives Considered" section is for this.
- **Be honest about uncertainty.** Use an "Open Questions" section for things you couldn't resolve. Don't paper over gaps.
- **Comprehensive, not exhausting.** 500-1500 lines for typical work. If you're past 2000, you're probably overwriting — tighten up.
