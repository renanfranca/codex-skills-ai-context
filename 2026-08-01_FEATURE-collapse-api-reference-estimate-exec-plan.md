# Collapse API reference estimate details

This ExecPlan is a living document. Keep `Progress`, `Decisions`, `Risks and Mitigations`, and `Lessons Learned` up to date as work advances.

## Purpose / Big Picture

Make archived operation reports easier to scan without removing their detailed token cost evidence. A reader should see execution facts, token usage, observations, report limitations, and the canonical source as before, while the verbose API reference estimate begins closed and opens only when requested.

## Scope

In scope are the generated report presentation, website behavior tests, desktop and Pixel 7 browser coverage, styles only if the existing disclosure style is insufficient, and canonical website documentation. The `API reference estimate` heading remains visible. When an estimate exists, its reference values, recorded calculation, long context assessment, and estimate limitations move into one native disclosure closed by default. When no estimate exists, the compact `Not recorded` state remains visible without an empty disclosure.

Out of scope are token usage totals, normalized usage event behavior, observations, report level limitations, canonical report links, evaluation report JSON, schemas, archived evidence, runner behavior, public routes, generated data fields, model backed evaluation, commits, pushes, deployment, and direct edits to `website/.generated/`.

Safety boundary: This task is limited to authorized, defensive maintenance of this repository. Do not provide offensive guidance or policy bypassing instructions.

## Definitions

An API reference estimate applies an archived dated API price table to recorded token usage; it is not an observed charge or invoice. A disclosure is the native HTML `details` and `summary` interaction, which supports pointer and keyboard activation without custom JavaScript. `website/.generated/` is a disposable projection created from canonical sources and must not be edited directly.

## Existing Context

`website/scripts/generate-content.mjs` renders report pages in this order: execution facts, token usage, API reference estimate, observations, report limitations, and canonical source link. Normalized usage events already use a closed disclosure, but every recorded API estimate field is expanded. `website/tests/site-content.test.mjs` protects generated report markup, and `website/e2e/site.spec.mjs` exercises the published reader journey on desktop Chromium and Pixel 7. `website/README.md` documents the report evidence presentation.

## Desired End State

Every report with archived pricing evidence shows the `API reference estimate` heading followed by a closed disclosure labeled `View API reference estimate details`. Opening it reveals the complete existing estimate content without changing values or wording. Token usage remains visible, and observations, report limitations, and the canonical report link remain outside the disclosure and visible before it is opened. Reports without an archived estimate show the current compact missing state directly.

## Milestones

### Milestone 1 - Add progressive disclosure to API estimates

#### Goal

Deliver one observable report behavior: recorded API reference estimate details start closed and remain fully accessible on demand without hiding unrelated evidence.

#### Changes

- Add behavior assertions to `website/tests/site-content.test.mjs` for a closed native disclosure, preserved estimate content, visible unrelated sections, and the no estimate state.
- Update `website/scripts/generate-content.mjs` to wrap only recorded API reference estimate details in the disclosure.
- Update `website/e2e/site.spec.mjs` to exercise initial hidden state and pointer or keyboard expansion on desktop and Pixel 7 while verifying observations remain visible.
- Reuse `website/.vitepress/theme/custom.css` disclosure styling unless browser validation proves a scoped spacing or focus adjustment is necessary.
- Update `website/README.md` to describe the collapsed by default estimate presentation. Leave root documentation and public VitePress configuration unchanged with explicit justification if inspection confirms they do not describe this display detail.

#### Validation

- Command: `cd /home/renanfranca/.codex/skills/website && npm test`
- Expected RED: the full suite fails only because generated reports do not yet contain the closed API estimate disclosure.
- Expected GREEN: the full suite passes after the minimum renderer change.
- Command: `cd /home/renanfranca/.codex/skills/website && npm run test:e2e`
- Expected result: desktop Chromium and Pixel 7 confirm the disclosure starts closed, opens on demand, and leaves observations visible.
- Final commands, in order: `npm test`, `npm run prettier:check`, `npm run build`, `npm run test:e2e`.
- Patch check: `cd /home/renanfranca/.codex/skills && git diff --check` and `cd /home/renanfranca/.codex/skills/_temporary/codex-skills-ai-context && git diff --check`.

#### Acceptance Criteria

- Recorded API estimate details are hidden until the reader expands the native disclosure.
- Expansion reveals the unchanged reference value, calculation, long context assessment, and estimate limitations.
- Token usage, observations, report limitations, and the canonical source are not captured by the disclosure.
- Reports without pricing evidence do not present an empty disclosure.
- The relevant suite, public checkpoint, documentation reconciliation, and final validation all pass.

## Progress

- [x] Root and website instructions read; complete website workflow profile confirmed.
- [x] Memory worktree and exact origin validated before plan creation.
- [x] Required `execplan-tdd`, `implement-execplan`, and behavior TDD instructions read.
- [x] Milestone 1 started.
- [x] Behavior reached expected RED: `npm test` ran 37 tests and failed only the updated report presentation test because the API estimate disclosure was absent.
- [x] Renderer reached GREEN: `npm test` passed all 37 tests.
- [x] Public browser checkpoint passed: `npm run test:e2e` passed 31 tests across desktop Chromium and Pixel 7 with one expected skip.
- [x] Post GREEN design review completed with no production refactor: the renderer adds one local native disclosure, introduces no state or duplicated transformation, and reuses established styling and public tests.
- [x] Canonical documentation reconciled in `website/README.md`; public configuration and root documentation remain accurate for the reasons recorded below.
- [x] Final validation completed: 37 unit tests passed, Prettier matched all files, the build validated 53 reports and one comparison, and 31 browser tests passed with one expected skip.
- [x] Milestone 1 completed.

## Decisions

- Decision: Collapse only recorded API reference estimate content, not every section that follows it.
  Rationale: Observations are primary evaluation evidence, and report limitations and canonical source provenance must not become accidentally subordinate to token cost details.
  Date/Author: 2026-08-01 / Codex

- Decision: Keep the section heading visible and use native `details` and `summary` with the label `View API reference estimate details`.
  Rationale: The estimate remains discoverable and receives accessible pointer and keyboard behavior without new state management.
  Date/Author: 2026-08-01 / Codex

- Decision: Use zero model sessions.
  Rationale: The requested presentation change is deterministic and completely observable through generated markup, unit tests, and browser journeys.
  Date/Author: 2026-08-01 / Codex

- Decision: Retain the implementation after the post GREEN design review.
  Rationale: The disclosure boundary is local to the existing estimate renderer, adds no hidden state or independent policy, and reuses the established `evidence-details` contract. Extraction or a custom Vue component would add indirection without removing a demonstrated risk.
  Date/Author: 2026-08-01 / Codex

## Risks and Mitigations

- Risk: A disclosure boundary could accidentally include observations or report provenance.
  Mitigation: Assert generated markup ordering and browser visibility before expansion.

- Risk: Existing level three estimate headings could appear in page navigation while their content is closed.
  Mitigation: Inspect the built page outline and keep hidden subsections out of misleading navigation if necessary while preserving semantic grouping inside the disclosure.

- Risk: Styling could obscure focus or expansion state on mobile.
  Mitigation: Reuse the established disclosure component styling and validate keyboard focus, desktop, and Pixel 7 behavior through the public checkpoint.

- Risk: A missing estimate could produce a useless expansion control.
  Mitigation: Preserve the compact `Not recorded` branch without a disclosure.

## Validation Strategy

Use generated public report markup as the fast behavior observation point. Run the entire website unit suite for RED and GREEN, then run the full desktop and Pixel 7 browser suite as the public checkpoint. After all behavior is green, apply the repository's post GREEN design review, reconcile canonical documentation, and run the declared final commands in order. No skill evaluation runner or model session is appropriate.

## Documentation Impact

`website/README.md` is canonical for report generation and presentation and now states that recorded API reference calculation details begin collapsed while missing estimates remain compact and visible. `website/.vitepress/config.mjs` remains unchanged because the feature changes neither routes, deployment base, navigation configuration, nor public metadata. The root `README.md` remains unchanged because it is a repository entry point and does not describe report sections. `EVALUATIONS.md` and `CODEX_CLI.md` remain unchanged because they define estimate semantics and persistence rather than website disclosure state. Archived reports remain unchanged because presentation is derived from their canonical JSON.

## Rollout and Recovery

There is no deployment in this task. The repository diff is the rollout artifact and remains uncommitted. The GitHub Pages workflow will regenerate the site from canonical sources after a later authorized commit reaches `main`. Recovery is a normal patch reversal of renderer, tests, and documentation; no archived evidence or data migration needs recovery.

## Lessons Learned

- The report already uses progressive disclosure for normalized usage events, so the requested interaction extends an established public pattern rather than introducing a new one.
- The production build preserves the estimate subsection headings inside the native disclosure, while the report's existing generated outline remains empty; the change therefore introduces no new misleading outline navigation.
- Final validation completed in the declared order. `git diff --check` passed in both worktrees, no CSS change was necessary, and no archived report or disposable generated page is part of the repository diff.
- The browser container's dependency installation reported three existing audit findings, two moderate and one high, but the task changed no dependency manifest or lockfile and all required browser tests passed.
