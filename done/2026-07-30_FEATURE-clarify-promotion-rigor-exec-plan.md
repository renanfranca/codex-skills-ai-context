# Clarify validated promotion rigor

This ExecPlan is a living document. Keep `Progress`, `Decisions`, `Risks and Mitigations`, and `Lessons Learned` current as work advances.

## Purpose / Big Picture

The website currently labels a current promotion as `Promotion evidence`, but the label and compact explanation do not make the promotion qualification gates or recorded execution effort visible. Readers can also mistake `Complete current coverage` for comparable proof even though it means only that each declared case has one current nonbaseline `PASS`.

This change renames the public state to `Validated promotion` while preserving the internal key `promotion`. A reader can inspect a promoted skill and see the valid RED, three stable GREEN results, proportional regression and current fingerprint requirements, together with the archived report's executor and judge sessions, token telemetry, cache, duration, completeness and canonical report link.

## Scope

In scope:

- Rename the public `Promotion evidence` label to `Validated promotion` without changing the `promotion` key or priority.
- Define validated promotion and complete current coverage precisely in the shared legend, evidence panel and `website/README.md`.
- Add `promotionSummary` to the generated evidence model only for a current eligible passing promotion.
- Project the promotion report link, executed executor, judge and total sessions, total and cached input tokens, duration, runtime and usage completeness exclusively from the archived report.
- Explain qualification gates, deterministic zero session promotions, session semantics, executor and judge roles, and the difference between token workload and observed financial charge.
- Cover desktop Chromium and emulated Pixel 7 public journeys while keeping catalog cards compact.

Out of scope:

- Changing the evaluation runner, report schema, archived reports, `EVALUATIONS.md` contract or internal `promotion` key.
- Claiming universal correctness, mandatory model cost or observed financial charges.
- Editing `website/.generated/` directly.
- Adding dependencies, running model sessions, committing, pushing, publishing or deploying.

## Definitions

- **Validated promotion**: a current archived operation with status `PASS` and `promotion_eligible: true`. The runner grants that qualification only after a valid affected baseline RED, three stable candidate GREEN results for each affected case, proportional regression when the declared impact requires it, and a candidate fingerprint matching the current skill source.
- **Complete current coverage**: every case in the current `evals/suite.json` has at least one current nonbaseline `PASS`, possibly across several operations. It does not prove a prior RED, repetition, stability or promotion.
- **Session**: one isolated, ephemeral executor or judge invocation of `codex exec --json` started by the evaluation runner. It is not a message, conversational turn or complete promotion.
- **Executor**: the session that performs the evaluation task.
- **Judge**: a separate session that evaluates semantic criteria when deterministic checks cannot fully decide the result.
- **Recorded effort**: actual sessions, normalized tokens and elapsed duration stored in the selected archived promotion report. Deterministic qualifications can record zero sessions.
- **Telemetry completeness**: the archived report's own `runtime.complete` and `usage.complete` fields. Missing values remain unrecorded rather than being inferred.
- **Public checkpoint**: `npm run test:e2e` from `website/`, which builds the projection and exercises desktop Chromium and an emulated Pixel 7.

## Existing Context

`website/scripts/generate-content.mjs` normalizes canonical archived `report.json` files, derives current evidence from source fingerprints and renders disposable pages and `data.json`. `deriveEvidence` currently exposes the entire matching `promotionReport`, while `evidenceComponentData` sends only a boolean `promotion` to `website/.vitepress/theme/EvidenceStatus.vue`.

The evidence panel currently reports suite coverage, whether a matching promotion exists, historical report count and current operation links. Shared label definitions live in `evidenceStates`; panel presentation is in `EvidenceStatus.vue`, and responsive styling is in `website/.vitepress/theme/custom.css`.

`website/tests/site-content.test.mjs` exercises the generator command and generated public data. `website/e2e/site.spec.mjs` exercises built pages in both configured browser profiles. The relevant suite for every TDD cycle is `npm test`; the public checkpoint is `npm run test:e2e`.

Canonical documentation sources are `website/README.md`, public configuration `website/content-config.json`, and root `README.md`, `CODEX_CLI.md` and `EVALUATIONS.md`. The runner contract remains canonical in `EVALUATIONS.md` and is not changed by this website clarification.

## Desired End State

The public state name is `Validated promotion` everywhere while generated evidence retains `key: "promotion"`. Its definition describes an integrated qualification with valid RED, three stable GREEN results per affected case, proportional regression when required and a current source fingerprint. `Complete current coverage` explicitly promises only one current nonbaseline `PASS` per declared case.

For a current promotion, `evidence.promotionSummary` contains the report ID and public report link, executed executor, judge and total sessions, total and cached input tokens, duration in milliseconds, and the report's runtime and usage completeness fields. Historical promotions and states without a current promotion expose `promotionSummary: null`.

The evidence panel presents a promotion only when that summary exists. It includes compact `Qualification gates` and `Recorded effort` sections, defines a session and the executor and judge roles, warns that a session is not a message, turn or whole promotion, states that deterministic checks consume zero sessions, warns that tokens measure recorded workload rather than observed financial cost, and links to the selected report. Nonpromotion panels retain their current compact facts and clearly say that complete coverage is not promotion qualification.

## Milestones

### Milestone 1 - Add the promotion summary contract

#### Goal

Expose faithful archived promotion effort through the generator's public output.

#### Changes

- Add behavior assertions to `website/tests/site-content.test.mjs` for current and historical promotions, executor and judge sessions, total and cached input tokens, duration, missing telemetry and states without promotion.
- Update `website/scripts/generate-content.mjs` to normalize the required report fields and derive `promotionSummary` without inventing missing values.
- Rename the shared public state and clarify both promotion and complete coverage descriptions.
- No documentation is completed in this milestone because the generated contract must first become green.

#### Validation

- Command: `npm test`
- Expected result before implementation: the new public contract fails because `promotionSummary` and the new label are absent.
- Expected result after implementation: the full relevant suite passes.

#### Acceptance Criteria

- A current eligible `PASS` promotion exposes the exact archived effort and public report link.
- Historical or absent promotions expose no summary.
- Missing telemetry is explicitly represented as unrecorded.

### Milestone 2 - Explain qualification and effort in the public panel

#### Goal

Let desktop and mobile readers understand what was qualified and what execution effort was recorded.

#### Changes

- Pass `promotionSummary` through `evidenceComponentData` in `website/scripts/generate-content.mjs`.
- Update `website/.vitepress/theme/EvidenceStatus.vue` with qualification, effort, session and token explanations plus the report link.
- Update `website/.vitepress/theme/custom.css` for readable compact panel sections without expanding catalog cards.
- Update `website/e2e/site.spec.mjs` for the new label and promoted panel behavior in desktop Chromium and emulated Pixel 7.
- Keep generated projections untouched by hand.

#### Validation

- Command: `npm test`
- Command: `npm run test:e2e`
- Expected result: generator tests and both browser profiles pass.

#### Acceptance Criteria

- Promoted panels show qualification gates, recorded effort, definitions and the evidence report link.
- Nonpromotion panels do not claim qualification or recorded promotion effort.
- The public panel remains usable without horizontal overflow in both profiles.

### Milestone 3 - Review design and reconcile documentation

#### Goal

Review the completed green implementation for structural risks and align canonical public wording.

#### Changes

- Apply `refactor-design` only after `npm test` and `npm run test:e2e` are green.
- Update `website/README.md` with the new public definition, effort fields, session semantics, zero session deterministic checks and financial caveat.
- Inspect `website/content-config.json`, root `README.md`, `CODEX_CLI.md` and `EVALUATIONS.md`; update only when the final public contract requires it, otherwise record a concrete no change justification.

#### Validation

- Command: `npm test`
- Command: `npm run test:e2e`
- Expected result: behavior remains green after any justified behavior preserving refactor and documentation change.

#### Acceptance Criteria

- No material structural risk remains within the changed data flow.
- Every canonical documentation source is updated or has a recorded reason to remain unchanged.

### Milestone 4 - Complete ordered final validation

#### Goal

Prove the final source, formatting, build, public behavior and whitespace integrity in both worktrees.

#### Changes

- Record exact command outcomes here.
- Make source changes only if validation reveals a defect, then restart the applicable gate.

#### Validation

- From `website/`, in order: `npm test`
- From `website/`, in order: `npm run prettier:check`
- From `website/`, in order: `npm run build`
- From `website/`, in order: `npm run test:e2e`
- From the main repository: `git diff --check`
- From the memory worktree: `git -C _temporary/codex-skills-ai-context diff --check`

#### Acceptance Criteria

- Every command exits successfully in the required order.
- Every milestone acceptance criterion is satisfied.

## Progress

- [x] Repository and website instructions read.
- [x] Memory worktree and exact origin validated.
- [x] `execplan-tdd`, `implement-execplan`, behavior TDD and `refactor-design` instructions loaded.
- [x] ExecPlan created before test or production edits.
- [x] Milestone 1 started.
- [x] Milestone 1 completed.
- [x] Milestone 2 started.
- [x] Milestone 2 completed.
- [x] Milestone 3 started.
- [x] Milestone 3 completed.
- [x] Milestone 4 started.
- [x] Milestone 4 completed.

## Decisions

- Decision: Preserve the internal state key `promotion` and change only the public label and definition.
  Rationale: Existing generated consumers and CSS variants rely on the stable state key; the requested change is semantic clarification, not a data migration.
  Date/Author: 2026-07-30 / Codex

- Decision: Expose archived completeness as separate `runtimeComplete` and `usageComplete` values.
  Rationale: The report records these facts independently. Combining them into one inferred boolean would hide which telemetry is incomplete and violate the requirement to display only archived values.
  Date/Author: 2026-07-30 / Codex

- Decision: Use the generated report route rather than a direct GitHub URL in `promotionSummary`.
  Rationale: The website's report page is the public evidence path already used by current result links and it links onward to the canonical archived report.
  Date/Author: 2026-07-30 / Codex

- Decision: Preserve a legacy scalar `sessions.executed` only as the total, with executor and judge left unrecorded.
  Rationale: Assigning an undivided legacy total to either role would invent a fact that the archived report does not contain.
  Date/Author: 2026-07-30 / Codex

- Decision: Store qualification gate labels in the central `promotion` state and pass them to the Vue panel.
  Rationale: The post GREEN design review classified separate hardcoded gate lists in the generator and component as a duplication risk that could make the definition diverge.
  Date/Author: 2026-07-30 / Codex

## Risks and Mitigations

- Risk: Older reports store `sessions.executed` as a scalar while current reports store role counts.
  Mitigation: Normalize scalar values as total only and prove through a behavior test that missing role values remain unrecorded.

- Risk: Token values can be mistaken for price or observed billing.
  Mitigation: Label them as recorded workload and state explicitly that no observed financial cost follows from the token totals.

- Risk: The detailed promoted panel can become unwieldy on mobile.
  Mitigation: Keep cards unchanged, use compact fact grids and verify the complete panel in the Pixel 7 journey with overflow checks.

- Risk: A stale promotion could expose current qualification details.
  Mitigation: Derive `promotionSummary` only from `promotionReport`, which is already limited to a matching current fingerprint, `PASS` and `promotion_eligible: true`.

## Validation Strategy

1. Add one generator behavior at a time and run the complete `npm test` suite for RED and GREEN.
2. Run `npm run test:e2e` no later than two completed TDD cycles and at milestone completion.
3. Exercise both promoted and nonpromoted public panels in desktop Chromium and emulated Pixel 7.
4. Complete the post GREEN design review before documentation reconciliation.
5. Run all final commands in the website profile's declared order, followed by `git diff --check` in both worktrees.

Validation evidence before the final ordered gate:

- `npm test` demonstrated the planned RED when `Validated promotion` and `promotionSummary` were absent, then passed with 21 tests after the initial model implementation.
- `npm run test:e2e` demonstrated RED because the promoted panel lacked `Qualification gates`, then passed 24 browser journeys across desktop Chromium and Pixel 7 after the UI implementation.
- The design review found an observable legacy session attribution defect. `npm test` demonstrated RED with executor incorrectly reported as `4`, then passed 22 tests after preserving only the recorded total.
- After centralizing qualification gates, `npm test` passed 22 tests and `npm run test:e2e` passed all 24 browser journeys.
- Sandbox executions later blocked test subprocesses with `EPERM`; the same commands passed outside the sandbox with explicit approval. No source change was made in response to that environment failure.
- Final ordered validation completed on 2026-07-30: `npm test` passed 22 tests; `npm run prettier:check` reported all matched files formatted; `npm run build` validated 53 reports and built 10 skills and 53 report pages; `npm run test:e2e` passed all 24 desktop and Pixel 7 journeys.
- `git diff --check` passed in the main repository and `git -C _temporary/codex-skills-ai-context diff --check` passed in the memory worktree.

## Documentation Impact

- `website/README.md`: updated the evidence definitions and added archived effort, session, deterministic and financial interpretation.
- `website/content-config.json`: inspected and unchanged because it controls catalog inclusion and the base path, not evidence semantics.
- Root `README.md`: inspected and unchanged because it introduces the repository without using the renamed website status.
- `CODEX_CLI.md`: inspected and unchanged because it documents runner commands and already distinguishes sessions and observed charges; it does not use the renamed website status.
- `EVALUATIONS.md`: inspected and unchanged as the detailed runner contract. It already defines sessions, deterministic zero session cases, qualification gates, telemetry and financial estimates; no runner semantic changed.
- `website/.generated/`: disposable projection regenerated by repository commands, never edited directly.

## Rollout and Recovery

The source change is rendered during the normal VitePress build and requires no migration. Recovery is a normal source revert of the generator, Vue component, CSS, tests and README; regenerated output is disposable. No deployment, commit or publication is part of this task.

## Lessons Learned

- Current promotion reports record runtime completeness and token usage completeness separately, so the public summary must preserve both instead of collapsing them.
- Legacy scalar session totals do not contain enough information to assign executor or judge roles.
- The website test suite depends on child Node, Python and Git processes; a sandbox `EPERM` can hide the underlying test details until the file is run directly or the suite is repeated at an approved boundary.
