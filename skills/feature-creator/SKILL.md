---
name: feature-creator
description: Drive spec-first feature work from product brief through design, tasks, implementation, validation, feedback, and harness improvement.
---

# Feature Creator

Use this skill for new app features, feature planning, harness improvements, or changes that need a written spec before production code.

## Principles

- Start every feature with a written spec.
- Do not implement production code until the spec, design, and task list are accepted.
- End each stage by asking for user approval, unless the user explicitly waives approval.
- Ask for clarification when behavior, scope, risk, or expected output is uncertain.
- Keep features independently testable and changes surgical.
- Test every implementation task.
- Prefer test doubles, fake clients, fixture data, and replayed events for external dependencies.
- Keep secrets and sensitive user data out of source control.
- Capture durable decisions in artifacts, not only chat.
- Ask for approval before changing harness instructions, conventions, patterns, or architecture rules.

## Harness

The harness is the living engineering system around feature development: tests plus repeatable process, checks, examples, conventions, architecture rules, and feedback loops.

It covers:
- Coverage: unit, integration, end-to-end, smoke, fixtures, manual checks.
- Conventions: naming, layout, formatting, errors, logs, configuration, secrets.
- Patterns: dependency isolation, test doubles, state ownership, retries, cancellation, streaming, persistence.
- Architecture: boundaries between UI, domain logic, platform APIs, network clients, persistence.
- Validation: commands and manual checks that prove behavior.
- Feedback: how failures, bugs, reviews, user feedback, and friction update the harness.
- PR completion: how the Agent self-reviews, requests reviews, responds to feedback, and iterates.

Change the harness only from evidence: failure, repeated confusion, review finding, bug, user correction, or bottleneck.

## PR Completion Loop

Humans should be able to drive feature work primarily through prompts. The Agent should use standard project tools directly, including `gh`, local scripts, and repository-embedded skills, instead of asking humans to copy-paste CLI context.

For every meaningful PR:
1. Review the local diff before external review.
2. Run documented validation commands.
3. Request specific agent reviews locally and in the cloud for non-trivial, risky, or feedback-sensitive changes.
4. Respond to each human or agent finding with a code change, test or validation update, or documented reason for no change.
5. Re-run relevant validation after each feedback-driven change.
6. Iterate until required human feedback is resolved and agent reviewers are satisfied, or document remaining gaps with owner and next action.

A PR is complete only when feedback is answered, validation is current, and reusable lessons are captured in the harness.

## Workspace

Use this layout as the project grows:
- `specs/000-product-brief.md`
- `specs/<number>-<feature>/spec.md`
- `specs/<number>-<feature>/design.md`
- `specs/<number>-<feature>/tasks.md`
- `specs/<number>-<feature>/validation.md`
- `specs/<number>-<feature>/feedback.md` when needed
- `src/` for app code after specs are accepted
- `tests/` for harness and app tests

Number feature folders in dependency order. Later specs may depend on earlier accepted behavior but must not silently change it.

## Workflow

Each stage must create or update its artifact, include harness guidance, resolve behavior-changing uncertainty, and ask for approval before the next stage.

### 1. Product Brief

Create or update `specs/000-product-brief.md`.

Required content: target users, core problem, MVP scope, supported platforms, constraints (latency, offline, cost, accessibility), privacy expectations, and external services or APIs.

Gate: continue only when the brief can prioritize the first feature.
Approval: ask the user to approve `product-brief.md` before creating the first feature spec.

### 2. Feature Spec

Create `specs/<number>-<feature>/spec.md`.

Required sections: `Goal`, `Users`, `User Stories`, `Requirements`, `Acceptance Criteria`, `Out Of Scope`, `Open Questions`.

Requirements must include functional requirements (`FR-*`), non-functional requirements (`NFR-*`), and harness requirements (`HR-*`). Acceptance criteria should use observable Given/When/Then-style checks.

Gate: do not design or code while open questions could change user-visible behavior.
Approval: ask the user to approve `spec.md` before creating `design.md`.

### 3. Technical Design

Create `specs/<number>-<feature>/design.md`.

Required sections: `Summary`, `Interfaces`, `Boundaries`, `Patterns And Conventions`, `Data Flow`, `State And Errors`, `Test Strategy`, `Risks`.

Designs must cover test coverage, relevant conventions, required patterns, and architecture boundaries. UI must not directly own platform, network, or persistence details. External dependencies need test doubles or fixtures.

Coverage guide: unit for pure state, validation, formatting, and errors; integration for adapters with doubles; end-to-end for primary user paths; smoke for launch, missing credentials, unavailable services, and recovery.

Gate: continue only when the design includes doubles or fixtures for external dependencies.
Approval: ask the user to approve `design.md` before creating `tasks.md`.

### 4. Task Plan

Create `specs/<number>-<feature>/tasks.md`.

Required sections: `Implementation Tasks` and `Verification`.

Each task must be small, map to acceptance criteria, and name its harness coverage type: unit, integration, end-to-end, fixture, manual smoke, convention, pattern, or boundary.

Verification must cover unit, integration, and end-to-end results or deferrals; formatting/lint when available; conventions and architecture; manual smoke; local self-review; and review feedback follow-up.

Gate: implement only after the task list covers the feature spec.
Approval: ask the user to approve `tasks.md` before starting implementation.

### 5. Implementation

For each task:
1. Write or update the test first.
2. Implement the smallest code change that satisfies it.
3. Run relevant tests, formatting, linting, or static checks when tooling exists.
4. Update `tasks.md`.
5. Add open questions to the spec instead of guessing.
6. Before PR handoff, review the local diff and request specific agent reviews for non-trivial or risky changes.
7. Address feedback in small follow-up changes and re-run relevant validation.

Return to the spec when implementation reveals an uncaptured product, privacy, platform, or API decision.
Approval: ask the user to approve the completed implementation before final validation.

### 6. Validation

Create or update `specs/<number>-<feature>/validation.md`.

Required sections: `Automated Checks`, `Manual Smoke Test`, `Review Loop`, `Known Gaps`.

Validation must mention unit, integration, and end-to-end results or deferrals; manual smoke result; formatting/convention checks; local self-review; agent review requests; review feedback addressed or open; and architecture or pattern gaps.

Gate: a feature is done only when validation proves acceptance criteria or clearly documents remaining gaps.
Approval: ask the user to approve `validation.md` before marking the feature done.

### 7. Harness Feedback

Create or update `specs/<number>-<feature>/feedback.md` when validation, review, bugs, user testing, logs, or developer friction reveal a harness gap.

Required sections: `Signal`, `Root Cause`, `Harness Update`, `Follow-Up`.

Classify root cause as spec gap, test gap, convention gap, pattern gap, architecture gap, or implementation bug. Then update the earliest artifact that should have prevented it, add the smallest check that catches it next time, and update `tasks.md` and `validation.md`.

Do not bury durable feedback only in chat or commit messages.
Approval: ask the user to approve `feedback.md` and any feedback-driven behavior changes before applying them.

### 8. Harness Self-Improvement

Use self-improvement when the harness itself is weak: repeated bugs, smoke tests catching what automated tests miss, template gaps, fragile patterns, unclear conventions, repeated review corrections, repeated boundary violations, or user corrections to workflow or artifact shape.

Process: record the signal in `feedback.md`, identify the weakest instruction, propose the smallest harness update, ask for approval, then update the right artifact.

Scope: feature-specific lessons update feature artifacts; project-wide lessons update the brief, conventions, architecture docs, or project context; skill-level lessons update this file only with explicit user approval.

## Project Context

Ask for approval before creating or updating `docs/project-context.md`. Use it for project-specific spec order, domain interfaces, harness standards, external service notes, and feedback loop rules. Reference it from specs and designs; keep this skill generic.

## Reusable Prompts

- Product brief: `Create specs/000-product-brief.md for <app idea>. Ask only for MVP decisions. Ask for approval before feature specs.`
- Feature spec: `Create specs/<number>-<feature>/spec.md using feature-creator. Include requirements, acceptance criteria, harness requirements, out-of-scope items, and open questions. Ask for approval before design.`
- Design: `Create specs/<number>-<feature>/design.md from the accepted spec. Include interfaces, boundaries, patterns, conventions, data flow, state/errors, tests, fixtures/doubles, and risks. Ask for approval before tasks.`
- Tasks: `Create specs/<number>-<feature>/tasks.md from the accepted spec and design. Map each acceptance criterion to automated or manual validation. Keep tasks small. Ask for approval before implementation.`
- Implement: `Implement task <task id> from specs/<number>-<feature>/tasks.md. Test first, keep changes scoped, update tasks and validation, and ask for approval before marking complete.`
- Feedback: `Apply harness feedback from <signal>. Classify root cause, update the earliest artifact that should have prevented it, add the smallest check to catch it next time, and ask for approval before behavior changes.`
- Improve harness: `Improve the harness from specs/<number>-<feature>/feedback.md. Classify scope, propose the smallest update, and ask for approval before changing process instructions.`

## Definition Of Done

- Product brief explains users, MVP boundaries, constraints, services, and privacy expectations.
- Each feature has spec, design, tasks, validation, and feedback when needed.
- Every task traces from acceptance criteria to tests or manual validation.
- Coverage, conventions, patterns, and architecture boundaries are captured before code changes.
- Tests, review findings, bugs, user feedback, and friction become harness updates.
- PRs are locally self-reviewed, agent reviews are requested when appropriate, and feedback is resolved or documented.
- Repeated feedback improves the harness after user approval.
- Production code starts only after spec, design, and task list are accepted.
