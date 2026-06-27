# Plan Section Templates

This is the canonical structure for sections in a plan. Adapt to the request — drop sections that don't apply, merge tiny ones, but cover the substance. The numbered list below is the default order.

---

## 1. Title & Metadata

The first thing in the file. A clear title, plus a small metadata block.

```markdown
# <Plan Title>

| Field    | Value                          |
| -------- | ------------------------------ |
| Status   | Draft / Approved / Implemented |
| Date     | YYYY-MM-DD                     |
| Scope    | One-line scope                 |
| Owner    | <user name or TBD>             |
```

Status starts as "Draft" — the user updates it as the plan moves through review.

---

## 2. Goal

One paragraph. State what we're building/changing and what success looks like.

**Good**:

> Add JWT-based authentication to the public API so that requests can be authenticated without server-side session storage. Success: every API endpoint accepts a Bearer token; expired and malformed tokens return 401 with a structured error body.

**Bad**:

> Improve security.

---

## 3. Context & Motivation

Why this matters. What's the current state? What's broken or missing? Why now?

Cover:

- The problem this plan solves
- Current state of the relevant code/systems
- Why this is the right time to do it
- Any related prior work (link to other plans, ADRs, issues)

This is the "if a new engineer joined tomorrow, would they understand why this work is happening" section.

---

## 4. Scope

Be ruthless. Scope creep kills plans.

- **In scope**: what this plan delivers
- **Out of scope**: what we're explicitly not delivering (and where it lives if known)
- **Deferred**: things we considered but decided to punt, with the reason

Example:

> **In scope**: JWT auth on `/api/*` endpoints, token refresh endpoint, logout.
> **Out of scope**: admin-impersonation, OAuth/social login, password reset (tracked separately).
> **Deferred**: rate limiting on auth endpoints (covered by future work in `<link>`).

---

## 5. Approach

High-level strategy. Two to four paragraphs. The "if you only read one section, read this" section.

Cover:

- The chosen path and why
- How the pieces fit together at a high level
- Key design decisions (with one-line rationale each — full detail goes in Design)
- What this approach optimizes for (simplicity, perf, extensibility, time-to-ship)

Do not include code here. That's what the Design section is for.

---

## 6. Design

The detailed design. This is the meat of the plan.

Sub-sections (use whatever applies):

- **Architecture / Components**: the major components and how they relate. A diagram here is worth a thousand words.
- **Data model**: tables, types, schemas. Show DDL or type definitions.
- **APIs / Interfaces**: function signatures, HTTP routes, message formats. Show signatures, not implementations.
- **Data flow / Sequence**: how a request flows through the system. Sequence diagram for non-trivial flows.
- **State management**: where state lives, how it's read/written, consistency model.
- **Error handling**: how errors are detected, classified, and reported.
- **Security**: auth, authz, secrets, input validation, threat model if non-trivial.
- **Performance & scale**: expected load, latency budgets, caching, backpressure.
- **Observability**: logs, metrics, traces — what we'd add to know if this is working.
- **Testing strategy**: unit/integration/e2e approach, what fixtures/mocks we'll need.

Skip subsections that don't apply. Do not force every plan to have all of them.

---

## 7. Files & Changes

A table or list of files that will be touched and what changes. This is the implementation surface.

| File                       | Change                                            |
| -------------------------- | ------------------------------------------------- |
| `src/auth/jwt.ts`          | New file: JWT validation middleware               |
| `src/routes/api/users.ts`  | Add auth middleware to existing routes            |
| `migrations/0007_auth.sql` | New migration: `users.refresh_token_hash` column  |
| `package.json`             | Add `jsonwebtoken` dependency                     |

For each file, the change should be one line. The implementation steps in the next section break it down further.

---

## 8. Implementation Steps

Ordered, testable phases. Each step is a small, verifiable unit of work.

```markdown
### Step 1: Database migration

Add `users.refresh_token_hash` and `users.refresh_token_expires_at` columns.

- Migration file: `migrations/0007_auth.sql`
- Test: migration runs forward and backward cleanly
- Done when: `users` table has the new columns, existing rows have NULL values

### Step 2: Token issuance service

Implement `issueTokenPair(userId)` in `src/auth/tokens.ts`.

- Returns `{ accessToken, refreshToken, expiresIn }`
- Test: unit tests cover happy path, expiration, and signing-key rotation
- Done when: function passes tests, no other code calls it yet
```

Each step should have:

- **What** is being built
- **Where** (file paths)
- **How to verify** (test or check)
- **Done when** (the exit criterion)

A step is too big if it touches more than ~3 files or has more than one "Done when".

---

## 9. Risks & Mitigations

What could go wrong. Be honest.

| Risk                                          | Likelihood | Impact | Mitigation                                     |
| --------------------------------------------- | ---------- | ------ | ---------------------------------------------- |
| Existing API consumers break on auth rollout  | High       | High   | Versioned endpoints, feature flag for rollout  |
| JWT signing key compromise                    | Low        | High   | Key rotation strategy, kid in header, audit log |
| Token storage on client vulnerable to XSS      | Medium     | Medium | Use httpOnly cookies for refresh tokens         |

If a risk has no mitigation, that's a signal the plan isn't done. Either mitigate it, or document the acceptance of the risk in the plan.

---

## 10. Alternatives Considered

The paths we didn't take, and why. Short paragraphs.

> **Session-based auth (server-side sessions)**: simpler conceptually but requires sticky sessions or shared session storage. Rejected because we're already running multiple stateless API replicas and don't want to add Redis as a hard dependency for this feature.

Be specific about why. "It's more complex" is not a reason. "It adds a new infra dependency we're not ready to operate" is.

---

## 11. Open Questions

Things you couldn't resolve during planning. The user should answer these before implementation starts.

> - Do we want short-lived access tokens (15 min) with refresh, or long-lived (24 hr) without refresh? Plan assumes the former.
> - Should logout invalidate refresh tokens server-side, or rely on token expiration?
> - What's the policy on concurrent sessions — does logging in on device B invalidate device A's tokens?

If there are no open questions, say so explicitly: "None at draft time."

---

## 12. Acceptance Criteria

How we know the plan is fully implemented and working. This is the test plan for the plan.

Bulleted list, testable:

> - All `/api/*` endpoints reject requests without a valid Bearer token with 401
> - Refresh endpoint issues new access tokens until the refresh token expires (7 days)
> - Logout invalidates the refresh token server-side
> - Migration runs cleanly on a database with 1M existing user rows
> - p99 latency on `/api/users/me` stays under 50ms with auth middleware
> - Load test: 1000 RPS sustained with <1% error rate

If a criterion isn't measurable, rewrite it until it is.

---

## Optional sections

Use these when they apply; skip otherwise.

- **Migration / Rollout plan**: when the change is risky, document the rollout strategy (feature flag, canary, staged rollout, rollback procedure)
- **Cost / Resource impact**: when the change has infra cost (new service, more storage, more compute)
- **Open questions for stakeholders**: when the plan needs sign-off from people who aren't the reader
- **Glossary**: when the domain has jargon that needs defining
- **References**: links to RFCs, prior art, related issues, design docs
