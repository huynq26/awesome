---
name: bug-fix
description: Drive systematic bug resolution from triage through root-cause investigation, minimal fix planning, test-first implementation, and verified closure with harness feedback.
---

# Bug Fix

Use this skill for defects, regressions, unexpected behavior, or any issue that requires root-cause investigation before a code change.

## Principles

- Reproduce the bug before touching code. No fix without a failing test or a confirmed repro.
- Keep fixes surgical. Change only what the root cause requires.
- Do not refactor, clean up, or improve adjacent code as part of a bug fix.
- End each stage by asking for user approval, unless the user explicitly waives it.
- Ask for clarification when the failure mode, scope, or expected behavior is uncertain.
- Surface every assumption explicitly. If something is unclear, stop and ask.
- Capture durable decisions in artifacts, not only chat.
- Ask for approval before changing harness instructions, conventions, patterns, or architecture rules.

## Harness

The harness is the living engineering system around bug resolution: tests plus repeatable process, checks, examples, conventions, architecture rules, and feedback loops.

It covers:
- Coverage: regression tests, unit, integration, smoke, and manual checks.
- Conventions: naming, layout, error messages, logs, and configuration.
- Patterns: dependency isolation, test doubles, state ownership, retries, and cancellation.
- Architecture: boundaries between UI, domain logic, platform APIs, network clients, and persistence.
- Validation: commands and manual checks that prove the bug is fixed and nothing regressed.
- Feedback: how bugs, failures, reviews, and friction update the harness to prevent recurrence.
- PR completion: how the Agent self-reviews, requests reviews, responds to feedback, and iterates.

Change the harness only from evidence: failure, repeated confusion, review finding, bug, user correction, or bottleneck.

## PR Completion Loop

Humans should be able to drive bug fixes primarily through prompts. The Agent should use standard project tools directly, including `gh`, local scripts, and repository-embedded skills, instead of asking humans to copy-paste CLI context.

For every meaningful bug-fix PR:
1. Review the local diff before external review.
2. Run documented validation commands.
3. Request specific agent reviews locally and in the cloud for non-trivial, risky, or feedback-sensitive fixes.
4. Respond to each human or agent finding with a code change, test or validation update, or documented reason for no change.
5. Re-run relevant validation after each feedback-driven change.
6. Iterate until required human feedback is resolved and agent reviewers are satisfied, or document remaining gaps with owner and next action.

A bug-fix PR is complete only when feedback is answered, validation is current, and reusable lessons are captured in the harness.

## Workspace

Use this layout per bug:
- `bugs/<number>-<slug>/assess.md`
- `bugs/<number>-<slug>/investigate.md`
- `bugs/<number>-<slug>/plan.md`
- `bugs/<number>-<slug>/validation.md`
- `bugs/<number>-<slug>/feedback.md` when a harness gap is found

Number bug folders in resolution order.

## Workflow

Each stage must create or update its artifact, resolve uncertainty before proceeding, and ask for approval before the next stage.

### 1. Assess

Create `bugs/<number>-<slug>/assess.md`.

Required content: symptom description, affected users or systems, severity (critical / high / medium / low), reproduction steps, frequency and conditions, known workarounds, and related issues or recent changes.

Gate: continue only when the bug is reproducible or its conditions are understood well enough to investigate.
Approval: ask the user to approve `assess.md` before investigating.

### 2. Investigate

Create `bugs/<number>-<slug>/investigate.md`.

Required sections: `Reproduction`, `Trace`, `Root Cause`, `Blast Radius`, `Open Questions`.

- **Reproduction**: exact steps or test that triggers the failure.
- **Trace**: path through code from trigger to failure - function calls, state changes, boundary crossings.
- **Root Cause**: the single point where behavior diverges from intent. State it as a falsifiable claim: *"X happens because Y"*.
- **Blast Radius**: other code paths, users, or systems affected by the same root cause.
- **Open Questions**: anything that could change the fix scope.

Gate: do not plan a fix while open questions could change the root cause or blast radius.
Approval: ask the user to approve `investigate.md` before planning.

### 3. Plan

Create `bugs/<number>-<slug>/plan.md`.

Required sections: `Fix Strategy`, `Test Plan`, `Risks`, `Out Of Scope`.

- **Fix Strategy**: the minimal code change that addresses the root cause. Name the files and functions. Explain why this change is sufficient and why broader changes are not needed.
- **Test Plan**: the failing test that reproduces the bug (write it before the fix), or a confirmed repro with a documented reason automated regression coverage is deferred; include regression coverage for the blast radius.
- **Risks**: ways the fix could introduce regressions. Note any areas to smoke-test manually.
- **Out Of Scope**: related issues, tech debt, or improvements explicitly excluded from this fix.

Gate: implement only after the plan names a minimal change and either a failing test or a confirmed repro with an explicit automation deferral reason.
Approval: ask the user to approve `plan.md` before implementing.

### 4. Implement

For each task in the plan:
1. Write or update the failing test first and confirm it fails for the right reason, or document the confirmed repro and automation deferral before changing code.
2. Implement the smallest code change that makes the failing test pass or fixes the confirmed repro.
3. Run all relevant tests, formatting, linting, or static checks when tooling exists.
4. Confirm no previously passing tests are now failing.
5. Update `plan.md` with progress.
6. Add open questions to `investigate.md` instead of guessing.
7. Before closing the bug, review the local diff and request specific local and cloud agent reviews for non-trivial or risky changes.
8. Address feedback in small follow-up changes and re-run relevant validation.

Return to `investigate.md` when implementation reveals a deeper or different root cause than planned.
Approval: ask the user to approve the completed implementation before verifying.

### 5. Verify

Create or update `bugs/<number>-<slug>/validation.md`.

Required sections: `Regression Test`, `Automated Checks`, `Manual Smoke`, `Review Loop`, `Known Gaps`.

- **Regression Test**: confirm the failing test from the plan now passes, or confirm the documented repro no longer fails when automated regression coverage was deferred.
- **Automated Checks**: unit, integration, end-to-end results or deferrals; formatting and lint.
- **Manual Smoke**: steps taken to confirm the fix in a running environment.
- **Review Loop**: local self-review result; local and cloud agent review requests; human and agent findings addressed or open.
- **Known Gaps**: anything not verified, with owner and next action.

Gate: the bug is closed only when the original reproduction no longer fails, regression coverage passes or is explicitly deferred, no previously passing checks now fail, and validation clearly documents any remaining gaps.
Approval: ask the user to approve `validation.md` before closing the bug.

### 6. Harness Feedback

Create or update `bugs/<number>-<slug>/feedback.md` when the bug reveals a harness gap - a missing test, a convention that allowed the defect, a boundary violation, or an architecture weakness.

Required sections: `Signal`, `Root Cause Classification`, `Harness Update`, `Follow-Up`.

Classify root cause as: spec gap, test gap, convention gap, pattern gap, architecture gap, or implementation bug.

Then:
- Update the earliest artifact that should have prevented the bug.
- Add the smallest check that catches it next time.
- Update `plan.md` and `validation.md`.

Do not bury durable feedback only in chat or commit messages.
Approval: ask the user to approve `feedback.md` and any feedback-driven behavior changes before applying them.

### 7. Harness Self-Improvement

Use self-improvement when the harness is weak: repeated bugs of the same class, smoke tests catching what automated tests miss, template gaps, fragile patterns, unclear conventions, repeated review corrections, or user corrections to workflow or artifact shape.

Process: record the signal in `feedback.md`, identify the weakest instruction, propose the smallest harness update, ask for approval, then update the right artifact.

Scope: bug-specific lessons update bug artifacts; project-wide lessons update conventions, architecture docs, or project context; skill-level lessons update this file only with explicit user approval.

## Reusable Prompts

- Assess: `Assess bug <slug>. Document symptom, severity, reproduction steps, and workarounds in bugs/<number>-<slug>/assess.md. Ask for approval before investigating.`
- Investigate: `Investigate bug <slug> from bugs/<number>-<slug>/assess.md. Trace to root cause, measure blast radius, surface open questions. Ask for approval before planning.`
- Plan: `Plan fix for bug <slug> from bugs/<number>-<slug>/investigate.md. Name the minimal change, failing test or confirmed repro with automation deferral, risks, and out-of-scope items. Ask for approval before implementing.`
- Implement: `Implement fix from bugs/<number>-<slug>/plan.md. Write the failing test first, or document the confirmed repro and automation deferral, apply the minimal change, run all checks, and ask for approval before closing.`
- Verify: `Verify fix for bug <slug>. Confirm the original repro no longer fails, regression coverage passes or is deferred with reason, run automated checks, smoke test manually, resolve review findings, and complete the Agent PR loop. Update bugs/<number>-<slug>/validation.md.`
- Feedback: `Apply harness feedback from bug <slug>. Classify root cause, update the earliest artifact that should have caught it, add the smallest preventive check, and ask for approval before behavior changes.`

## Definition Of Done

- Bug has an assess artifact with reproduction steps and severity.
- Root cause is stated as a falsifiable claim in `investigate.md`.
- Fix is the minimal change that addresses the root cause, documented in `plan.md`.
- A regression test written before the fix now passes, or automation deferral is justified with a confirmed repro and manual verification.
- No previously passing tests are now failing.
- Manual smoke confirms the fix in a running environment.
- Meaningful bug-fix PRs are locally self-reviewed, local and cloud agent reviews are requested when appropriate, and feedback is resolved or documented.
- Harness gaps revealed by the bug are captured in `feedback.md` and fed back into the earliest preventive artifact.
- Repeated bugs of the same class trigger a harness self-improvement cycle.
