---
name: feature-creator
description: Create spec-driven feature plans and a self-improving development harness before implementation. Use when Codex needs to turn an app idea or feature request into a product brief, feature spec, technical design, task list, validation plan, test coverage strategy, coding conventions, design patterns, architecture guardrails, and feedback loops that improve the process or instructions; especially when the user wants to develop a Windows app, speech translation app, or any feature through specification-first workflow before writing production code.
---

# Feature Creator

Use this skill to drive spec-driven development before writing production app code.

## Harness Definition

In this skill, a harness is the living engineering system around feature development.

It is not only tests. It is the repeatable process, instructions, checks, examples, and feedback loops that make future development safer and clearer.

It must describe:

- Test coverage: unit, integration, end-to-end, smoke, fixtures, and manual checks.
- Coding conventions: naming, file layout, formatting, error handling, logging, and secret handling.
- Design patterns: dependency isolation, test doubles, state ownership, retry behavior, and cancellation.
- Architecture: boundaries between UI, domain logic, platform APIs, network clients, and persistence.
- Validation: commands and manual checks that prove the feature works.
- Feedback loop: how failures, bugs, review findings, and user feedback update the harness.
- Self-improvement: how repeated harness feedback improves specs, tasks, conventions, patterns, architecture, and this skill's instructions.

Keep the spec-driven workflow as the main process. Add harness guidance to each spec, design, task plan, and validation note.

The harness should evolve from evidence. Do not change the process because of taste alone; change it because a failure, repeated confusion, review finding, bug, user correction, or development bottleneck shows the current instruction is incomplete or misleading.

## Ground Rules

- Start every feature with a written spec.
- Do not implement production code until the relevant spec, design, and task list are accepted.
- Keep each feature small enough to test independently.
- Write or update tests for every implementation task.
- Prefer fake microphones, fake API clients, fixture audio, and replayed events during development.
- Keep API keys and raw user audio out of source control.
- List open questions instead of guessing when behavior is unclear.
- Capture coding convention, design pattern, and architecture decisions before implementation.
- At every workflow stage, ask for clarification when the expected output is uncertain.
- Ask for user approval before moving from one workflow stage to the next.
- Ask for user approval before changing harness instructions, conventions, patterns, or architecture rules.

## Workspace Layout

Use this layout as the project grows:

```text
specs/
  000-product-brief.md
  001-audio-capture/
    spec.md
    design.md
    tasks.md
    validation.md
  002-english-translation/
    spec.md
    design.md
    tasks.md
    validation.md
src/
  app code goes here after specs are accepted
tests/
  harness and app tests
```

Number `specs/` folders in dependency order. Later specs may depend on earlier accepted behavior, but should not silently change it.

## Spec-Driven Loop

Stage rule: each stage ends with an approval request. If the output is uncertain, incomplete, or based on an assumption that could change behavior, ask for clarification before asking for approval.

### 1. Product Brief

Create or update `specs/000-product-brief.md`.

Answer:

- Who uses this app?
- What language or languages can they speak?
- Is the output English text only, or English audio too?
- Is push-to-talk acceptable for the first version?
- What is more important for the first version: accuracy, latency, or offline support?
- What privacy expectations apply to recorded audio and transcripts?

Gate: continue only when the product brief has enough detail to prioritize the first feature.

Approval: ask the user to approve `specs/000-product-brief.md` before creating the first feature spec.

### 2. Feature Spec

Create `specs/<number>-<feature>/spec.md`.

Use this template:

```md
# Feature Spec: <name>

## Goal

<One paragraph describing the user-visible outcome.>

## Users

- <Primary user>

## User Stories

- As a user, I can <action> so that <benefit>.

## Functional Requirements

- FR-001: <specific behavior>
- FR-002: <specific behavior>

## Non-Functional Requirements

- NFR-001: <latency, reliability, privacy, accessibility, or platform requirement>

## Harness Requirements

- HR-001: <test coverage, fixture, convention, pattern, or architecture requirement>

## Acceptance Criteria

- Given <state>, when <action>, then <observable result>.

## Out Of Scope

- <Anything intentionally deferred>

## Open Questions

- <Question that must be resolved before design or implementation>
```

Gate: do not write design or code while open questions would change user-visible behavior.

Approval: ask the user to approve `spec.md` before creating `design.md`.

### 3. Technical Design

Create `specs/<number>-<feature>/design.md`.

Use this template:

```md
# Technical Design: <name>

## Summary

<Short implementation approach.>

## Interfaces

- `<InterfaceName>`: <responsibility>

## Architecture Boundaries

- UI: <responsibility>
- Domain: <responsibility>
- Platform: <responsibility>
- Network: <responsibility>
- Persistence: <responsibility>

## Design Patterns

- <Pattern and why it fits>

## Coding Conventions

- <Convention this feature must follow>

## Data Flow

1. <Step>
2. <Step>
3. <Step>

## State And Errors

- <State or error case>

## Test Strategy

- Unit: <what is isolated>
- Integration: <what is connected>
- End-to-end: <critical user path, if applicable>
- Fixtures: <audio, API, event, or data fixtures>
- Manual: <what needs human verification>

## Risks

- <Risk and mitigation>
```

For the Windows live translation app, prefer designs that isolate:

- `MicrophoneCapture`: microphone permissions, start, stop, audio frames, and level state.
- `TranslationClient`: one completed speech turn to English text.
- `RealtimeTranscriptClient`: optional partial transcript stream.
- `TranscriptStore`: ordered turns, timestamps, retry state, and clear history.

Gate: continue only when the design has test doubles for microphone and network dependencies.

Approval: ask the user to approve `design.md` before creating `tasks.md`.

### Harness Guidance

Every design must include:

- A test coverage plan for the feature.
- A coding convention note when the feature introduces new files, names, errors, logs, or configuration.
- A design pattern note when the feature introduces adapters, retries, cancellation, streaming, persistence, or state transitions.
- An architecture boundary note that prevents UI code from owning microphone, network, translation, or persistence details.

For the translation app:

- Unit tests should cover pure state transitions, validation, ordering, retry, and transcript formatting.
- Integration tests should cover adapters with fake microphone input and fake translation clients.
- End-to-end tests should cover the main listening-to-English-output path once an app shell exists.
- Smoke tests should cover launch, missing microphone, missing API key, network failure, retry, and clear transcript.
- Fixture tests should use short audio files, fake API responses, and replayed realtime events.

### 4. Task Plan

Create `specs/<number>-<feature>/tasks.md`.

Use this template:

```md
# Tasks: <name>

## Implementation Tasks

- [ ] T-001: Add failing test for <behavior>.
- [ ] T-002: Implement <smallest useful code change>.
- [ ] T-003: Add error state for <failure mode>.
- [ ] T-004: Update docs or examples.

## Verification

- [ ] Unit tests pass.
- [ ] Integration tests pass or are explicitly deferred with a reason.
- [ ] End-to-end tests pass or are explicitly deferred with a reason.
- [ ] Formatting and lint checks pass when tooling exists.
- [ ] Coding convention and architecture notes are followed.
- [ ] Manual smoke test is documented.
```

Map every acceptance criterion to at least one automated test or manual check.

Gate: start implementation only after the task list covers the feature spec.

Approval: ask the user to approve `tasks.md` before starting implementation.

Each task must say what kind of harness coverage it creates or updates:

- Unit test
- Integration test
- End-to-end test
- Fixture
- Manual smoke check
- Coding convention
- Design pattern
- Architecture boundary

### 5. Implementation

For each task:

1. Write or update the test first.
2. Implement the smallest code change that satisfies it.
3. Run the relevant tests.
4. Run formatting, linting, or static checks when tooling exists.
5. Update `tasks.md` as tasks are completed.
6. Add new open questions to the spec instead of guessing when behavior is unclear.

Stop and return to the spec when a task reveals a product decision, privacy decision, platform decision, or API behavior that was not captured earlier.

Approval: ask the user to approve the completed implementation and task updates before final validation.

### 6. Validation

Create or update `specs/<number>-<feature>/validation.md`.

Use this template:

```md
# Validation: <name>

## Automated Checks

- <command>: <result>
- <coverage or harness check>: <result>

## Manual Smoke Test

1. <Step>
2. <Expected result>

## Known Gaps

- <Gap, owner, and next action>
```

Gate: a feature is done only when validation proves the acceptance criteria or clearly documents what remains.

Approval: ask the user to approve `validation.md` before marking the feature done.

Validation must mention:

- Unit test result.
- Integration test result or deferral reason.
- End-to-end test result or deferral reason.
- Manual smoke test result.
- Formatting, linting, or convention check result when tooling exists.
- Any architecture or design-pattern gaps found during implementation.

### 7. Harness Feedback Loop

Create or update `specs/<number>-<feature>/feedback.md` when validation, review, bugs, or user testing reveal harness gaps.

Use this template:

```md
# Harness Feedback: <name>

## Signal

- Source: <test failure, smoke test, bug report, code review, user feedback, production log, or developer friction>
- Observation: <what happened>
- Impact: <why it matters>

## Root Cause

- Spec gap: <missing or unclear requirement>
- Test gap: <missing unit, integration, end-to-end, fixture, or smoke coverage>
- Convention gap: <unclear or missing coding convention>
- Pattern gap: <wrong or missing design pattern>
- Architecture gap: <boundary violation or missing layer responsibility>

## Harness Update

- Spec change: <what to update in spec.md>
- Design change: <what to update in design.md>
- Task change: <what to add or reorder in tasks.md>
- Validation change: <what to add to validation.md>
- Test/fixture change: <what coverage to add>
- Process change: <what workflow, convention, pattern, or architecture guidance should change>
- Skill change: <whether this SKILL.md should be updated>

## Follow-Up

- Owner: <person or role>
- Due: <date or milestone>
- Done when: <observable completion signal>
```

Apply feedback in this order:

1. Capture the signal in `feedback.md`.
2. Decide whether it is a spec gap, test gap, convention gap, pattern gap, architecture gap, or implementation bug.
3. Update the earliest artifact that should have prevented the issue.
4. Add or update the smallest test, fixture, smoke check, convention, or architecture note that catches it next time.
5. Update `tasks.md` and `validation.md` so the loop is visible and repeatable.

Do not bury feedback only in chat or commit messages. Durable feedback belongs in the feature's spec artifacts.

Approval: ask the user to approve feedback-driven updates before changing implementation behavior.

### 8. Harness Self-Improvement

Use self-improvement when feedback shows the harness itself is weak, not only when product code is wrong.

Self-improvement signals include:

- The same kind of bug appears in multiple features.
- Tests pass but manual smoke testing repeatedly catches missed behavior.
- A spec template leaves the same question unanswered more than once.
- A design pattern causes avoidable complexity or fragile tests.
- A coding convention is unclear, ignored, or repeatedly corrected in review.
- Architecture boundaries are repeatedly violated.
- The user corrects the workflow or expected artifact shape.
- Codex has to ask the same clarification question repeatedly across stages.

When a self-improvement signal appears:

1. Record it in `feedback.md`.
2. Identify the weakest instruction that allowed the issue.
3. Propose a harness update before editing instructions.
4. Ask the user to approve the harness update.
5. Apply the smallest durable change to the right artifact.
6. Add a validation check, prompt update, or definition-of-done item when useful.

Choose the update target by scope:

- Feature-specific lesson: update that feature's `spec.md`, `design.md`, `tasks.md`, `validation.md`, or `feedback.md`.
- Project-wide lesson: update product brief, coding conventions, architecture docs, or a shared spec template.
- Skill-level lesson: update `skills/feature-creator/SKILL.md` when the process or instruction should apply to future features.

Do not let the skill rewrite itself silently. Skill-level self-improvement requires explicit user approval.

## First Specs For The Translation App

Start with these specs before choosing the Windows UI framework:

1. `000-product-brief`: define MVP boundaries and privacy expectations.
2. `001-audio-capture`: record or stream microphone input behind a testable adapter.
3. `002-english-translation`: translate a completed speech turn into English text.
4. `003-transcript-ui`: show current, completed, failed, retried, and cleared transcript states.
5. `004-realtime-mode`: optional low-latency captions after the turn-based MVP works.

## Harness Standards For The Translation App

Use these standards unless a feature spec explicitly changes them.

### Test Coverage

- Unit: domain state, transcript ordering, retries, errors, clearing, and settings validation.
- Integration: microphone adapter with fixture audio, translation client with fake responses, transcript store with real local persistence when added.
- End-to-end: launch app, start listening, receive English text, retry failed translation, clear transcript.
- Smoke: missing microphone, missing API key, network unavailable, permission denied, app restart.
- Fixtures: short audio clips, fake translation payloads, replayed realtime events, and error responses.

### Coding Conventions

- Keep platform code behind adapters instead of calling Windows APIs directly from UI code.
- Keep network code behind clients instead of mixing API calls into domain or UI state.
- Use clear names that describe user behavior: `StartListening`, `StopListening`, `TranslateTurn`, `RetryTurn`, `ClearTranscript`.
- Treat cancellation, retry, and error messages as first-class behavior.
- Redact API keys and avoid logging raw audio or full transcripts unless the product brief explicitly allows it.

### Design Patterns

- Use adapter interfaces for microphone, translation, realtime transcription, and persistence.
- Use test doubles for all microphone and network dependencies.
- Keep UI state separate from domain state so tests can verify behavior without launching the app.
- Model transcript turns explicitly: pending, completed, failed, retrying, and cleared.
- Prefer append-only event handling for realtime streams, then derive display state from ordered turns.

### Architecture

- UI layer: renders controls, transcript state, loading state, and recoverable errors.
- Domain layer: owns listening sessions, turn state, retry rules, and transcript ordering.
- Platform layer: owns Windows microphone permissions and audio capture.
- Network layer: owns translation and realtime API clients.
- Persistence layer: owns local settings, transcript retention, and any cached fixtures.
- Test harness: owns fake adapters, fixture audio, replayed event streams, and smoke-test scripts.

### Feedback Loop

- If a unit test fails repeatedly, update the design pattern or domain-state rules before adding more implementation patches.
- If integration tests require real services too early, add fake clients, fixtures, or replay files.
- If end-to-end tests are flaky, record the cause in `feedback.md` and separate product failures from timing, environment, or fixture problems.
- If manual smoke testing finds a missed behavior, add it to acceptance criteria and create automated coverage when practical.
- If code review finds repeated style or layering issues, promote the lesson into coding conventions or architecture boundaries.
- If user feedback changes expected behavior, update the feature spec before changing code.
- If the same feedback repeats across features, propose a harness self-improvement update.

## API Notes For Future Specs

Current OpenAI docs describe two useful paths:

- Turn-based translation: the Audio API translations endpoint translates supported audio files into English and currently uses `whisper-1`.
- Realtime prototype: Realtime transcription sessions can stream transcript deltas and completed transcription events; completed turns can then be translated to English.

Use these as design inputs, not as implementation commitments. The feature spec should still decide whether the MVP requires turn-based translation, realtime captions, or both.

References:

- https://platform.openai.com/docs/guides/speech-to-text
- https://platform.openai.com/docs/guides/realtime-transcription

## Reusable Prompts

### Create A Product Brief

```text
Create specs/000-product-brief.md for a Windows app that listens to spoken language and translates it to English text.
Ask only for decisions that materially affect the MVP.
Ask for approval before moving to feature specs.
Do not design or implement code yet.
```

### Create A Feature Spec

```text
Create specs/<number>-<feature>/spec.md using the feature-creator skill.
Focus only on <feature>.
List open questions instead of guessing.
Include harness requirements for tests, conventions, patterns, and architecture.
Ask for clarification if requirements or acceptance criteria are uncertain.
Ask for approval before creating the design.
Do not write implementation code.
```

### Create A Design

```text
Create specs/<number>-<feature>/design.md from the accepted spec.
Include interfaces, architecture boundaries, design patterns, coding conventions, data flow, state/errors, tests, and risks.
Use fake clients or fixtures for microphone and network dependencies.
Ask for clarification if architecture boundaries or harness coverage are uncertain.
Ask for approval before creating tasks.
Do not write implementation code.
```

### Create Tasks

```text
Create specs/<number>-<feature>/tasks.md from the accepted spec and design.
Map every acceptance criterion to an automated test or manual validation step.
Include test coverage, convention, pattern, and architecture tasks when needed.
Keep tasks small and ordered.
Ask for approval before implementation.
```

### Implement A Task

```text
Implement task <task id> from specs/<number>-<feature>/tasks.md.
Write or update tests first.
Keep the change scoped to this task.
Update validation notes with commands run, results, and any harness gaps.
Ask for approval before marking the task or feature complete.
```

### Apply Harness Feedback

```text
Create or update specs/<number>-<feature>/feedback.md from the latest validation result, bug, review finding, or user feedback.
Classify the signal as a spec gap, test gap, convention gap, pattern gap, architecture gap, or implementation bug.
Update the earliest spec artifact that should have prevented it.
Add the smallest follow-up task or validation check needed to catch it next time.
Ask for clarification if the feedback changes expected behavior.
Ask for approval before applying feedback-driven behavior changes.
Do not write unrelated production code.
```

### Improve The Harness

```text
Review specs/<number>-<feature>/feedback.md and identify whether the harness itself needs improvement.
Classify the improvement as feature-specific, project-wide, or skill-level.
Propose the smallest instruction, convention, pattern, architecture, prompt, or validation change that would prevent the issue next time.
Ask for approval before changing process instructions or SKILL.md.
After approval, update the right artifact and note why the harness changed.
```

## Definition Of Done

The feature workflow is ready when:

- The product brief explains the user, MVP boundaries, and privacy expectations.
- Each feature has a spec, design, tasks, and validation file.
- Every implementation task has a path from acceptance criteria to tests or manual validation.
- Test coverage, coding conventions, design patterns, and architecture boundaries are captured before code changes.
- Feedback from failed tests, reviews, bugs, and user testing is captured in feature artifacts and turned into harness updates.
- Repeated feedback is used to improve the harness process, project instructions, or this skill after user approval.
- Each workflow stage was approved before the next stage began, or the user explicitly waived approval.
- Production code starts only after the relevant spec, design, and task list are accepted.
