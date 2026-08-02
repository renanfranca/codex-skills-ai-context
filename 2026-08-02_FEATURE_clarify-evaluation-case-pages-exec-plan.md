# Clarify evaluation case pages without changing their contracts

This ExecPlan is a living document. Keep `Progress`, `Decisions`, `Risks and Mitigations`, and `Lessons Learned` up to date as work advances.

## Purpose / Big Picture

Readers of an active evaluation page should be able to distinguish the input supplied to a case, the executor when one exists, runner checks, case-declared requirements, oracle and judge behavior, the case decision, and the latest archived execution. The site will explain only facts already present in canonical `case.json`, prompt, fixture, and archived report data. It will not invent a case purpose, claim which mechanism is decisive, or change any evaluation contract or fingerprint.

## Scope

This work changes the active evaluation page projection in `website/scripts/generate-content.mjs`, its behavior tests in `website/tests/site-content.test.mjs`, its browser journeys and visual snapshots in `website/e2e/`, and the public description in `website/README.md`.

The following remain out of scope and must not change: any `case.json`, fixture, oracle, skill, evaluation runner, schema, archived report, fingerprint algorithm, root documentation, `website/content-config.json`, and generated files under `website/.generated/`. Public routes, historical evaluation pages, operation grouping, and the distinction between case results and complete operation results remain stable. This stage uses zero model sessions.

## Definitions

An active evaluation is a current case listed in a skill's `evals/suite.json`. A fixture is the initial repository state copied into the disposable executor workspace; it is input, not an evaluation result. The executor is an isolated model invocation that receives the prompt and starting workspace. Runner checks are automatic runner behavior plus requirements declared in `case.json`. An oracle is repository-controlled code kept outside the workspace visible to the executor and launched as another process against the workspace the executor leaves behind. A judge is a separate model invocation that applies declared semantic criteria. An observation is one case result recorded inside an operation, while an operation is the complete runner invocation and may contain multiple observations.

## Existing Context

`website/scripts/evaluation-catalog.mjs` reads the canonical case manifest, prompt, fixture paths, mechanical configuration, oracle commands, judge configuration, evidence, and related archived operations without modifying them. `website/scripts/generate-content.mjs` renders those models into disposable Markdown pages. Today the page uses the headings “Public prompt,” “Mechanical checks,” and “Result branches,” presents commands without their roles, omits disabled oracle and judge explanations, displays raw `no_action_acceptable`, and offers a static result list containing `SKIPPED` as though it could be the final observation result.

`website/tests/site-content.test.mjs` observes generated Markdown through the generator's public command. `website/e2e/site.spec.mjs` observes built pages through Playwright. The relevant suite is `npm test`; the public checkpoint is `npm run test:e2e`. Both run from `website/`.

## Desired End State

Every active page is ordered as provided input, executor when applicable, runner checks, case decision, and latest execution. Input is titled “Prompt and starting repository” when fixture files exist and “Prompt” otherwise, and the fixture explanation makes its initial-state role explicit. Runner checks distinguish the executor exit from workspace checks and oracle processes, show required and protected paths separately, label every command by role, and explain a nonzero expected command exit as that command's success condition.

Oracle and judge sections remain visible and say “Not used in this case” when disabled, but only applicable mechanisms appear in the flow. The oracle explanation states its ownership, isolation, and process boundary. Judge pages translate both boolean values of `no_action_acceptable` into plain language without assigning undeclared weight.

The case decision exposes only outcomes the runner can produce for the current path: deterministic cases show `PASS` and `FAIL`; executor cases without a judge add `ERROR`; executor cases with a judge add `ERROR` and `INCONCLUSIVE`. `SKIPPED` appears only as a possible judge state after an earlier failure and never as a final observation result. Routes, history, operation grouping, escaping of untrusted content, and the separation of case and operation results remain unchanged.

## Milestones

### Milestone 1: Specify and render the clarified active case definition

#### Goal

Use behavior-focused generator tests to specify all supplied-input, check-role, disabled-mechanism, judge-translation, decision-matrix, and escaping behavior, then make the smallest generator changes that satisfy them.

#### Changes

Edit `website/tests/site-content.test.mjs` first, one observable behavior at a time, and run the complete relevant suite to demonstrate RED and GREEN. Edit `website/scripts/generate-content.mjs` only after each expected RED. Do not edit `website/.generated/` directly.

#### Validation

Run `npm test` from `website/` after every test change and implementation change. The acceptance criterion is a green suite whose generated-page assertions cover cases with and without fixtures, deterministic and executor paths, oracle and judge use and nonuse, executor and command exit codes, expected command exit `1`, both `no_action_acceptable` values, the result matrix without `SKIPPED`, and escaping of untrusted content.

### Milestone 2: Verify public journeys and visual presentation

#### Goal

Exercise representative real cases through the built site and update only visual baselines affected by the intentional flow change.

#### Changes

Edit `website/e2e/site.spec.mjs` to cover `incomplete-profile-gate`, `hidden-invocation-state`, `red-suite-gate`, and `trigger-selection`. If screenshots change, update them only through the digest-pinned Playwright container command described by `website/README.md`.

#### Validation

Run `npm run test:e2e` from `website/`. Acceptance requires both desktop Chromium and the emulated Pixel 7 journey to pass, with active flows remaining keyboard accessible, responsive, and free of horizontal overflow.

### Milestone 3: Review design and reconcile documentation

#### Goal

After the relevant suite and public checkpoint are green and behavior is complete, perform the required post-GREEN design review, preserve behavior through any justified refactor, and document the implemented limit accurately.

#### Changes

Use `refactor-design` only after its entry gate is satisfied. Update `website/README.md` to say that the catalog explains available mechanisms but does not invent case-specific purposes or assertions absent from canonical contracts. Leave `website/content-config.json` and root documentation unchanged because neither defines evaluation-page explanatory semantics.

#### Validation

After any material refactor, rerun `npm test` and `npm run test:e2e`. Confirm the documentation describes the final generated behavior and that `.generated/` remains disposable output.

### Milestone 4: Complete ordered validation

#### Goal

Demonstrate that behavior, formatting, production generation, browser journeys, and whitespace checks all pass in the required order.

#### Validation

From `website/`, run `npm test`, `npm run prettier:check`, `npm run build`, and `npm run test:e2e`, in that order. From the repository root, then run `git diff --check`. Every command must exit successfully.

## Progress

- [x] Repository and nested instructions loaded.
- [x] Memory worktree and exact origin validated.
- [x] ExecPlan created before implementation.
- [x] Milestone 1 started.
- [x] Milestone 1 completed.
- [x] Milestone 2 started.
- [x] Milestone 2 completed.
- [x] Milestone 3 started.
- [x] Milestone 3 completed.
- [x] Milestone 4 started.
- [x] Milestone 4 completed.

## Decisions

- Decision: Preserve the data-loading contract and change only the active-page explanation and presentation.
  Rationale: Every requested fact already exists in the current evaluation model, so changing cases, schemas, reports, or fingerprints would expand scope and invalidate evidence unnecessarily.
  Date/Author: 2026-08-02 / Codex

- Decision: Treat `SKIPPED` only as a judge state and derive final decision options from case kind and judge applicability.
  Rationale: This matches the runner semantics described by the authorized plan and prevents the page from presenting an intermediate mechanism state as an observation result.
  Date/Author: 2026-08-02 / Codex

- Decision: Do not describe mechanism weight or a decisive mechanism per case.
  Rationale: The current canonical contract does not declare such meaning, and the website must not manufacture it.
  Date/Author: 2026-08-02 / Codex

- Decision: Derive both the flow statuses and decision explanations from one local outcome representation.
  Rationale: The post-GREEN design review classified the duplicated result matrix as a design risk because the two presentations could drift while remaining individually valid markup.
  Date/Author: 2026-08-02 / Codex

## Risks and Mitigations

- Risk: Editorial wording could imply guarantees absent from the executable contract.
  Mitigation: Phrase explanations in terms of runner behavior and declared fields only, and assert representative generated text in behavior tests.

- Risk: A flow update could break responsive connectors, keyboard focus, or snapshots.
  Mitigation: Preserve the existing semantic list structure and CSS classes, exercise desktop and mobile E2E journeys, and regenerate snapshots only in the pinned container.

- Risk: Untrusted prompt or judge criteria could become executable markup while wording is reorganized.
  Mitigation: Keep all canonical content behind existing escaping functions and retain explicit escaping assertions.

- Risk: Case result and complete operation result could be conflated while sections are reordered.
  Mitigation: Preserve the latest operation renderer and its separate labels and cover the distinction in E2E.

## Validation Strategy

The behavior loop uses the generator CLI as the highest useful stable observation point and runs the complete `npm test` suite for every RED and GREEN. The public checkpoint builds canonical projections and exercises real repository cases in the fixed Playwright environment. After the public checkpoint is green, design review is constrained to changed rendering responsibilities. Final validation runs the exact ordered command sequence declared above and records concrete results here.

The fresh baseline was `npm test`: 37 tests passed on 2026-08-02 before behavior changes. Behavior assertions then produced expected RED results for supplied input, runner checks and command roles, mechanism applicability, judge translation, the decision matrix, and the deterministic no fixture wording before each implementation restored GREEN.

Final validation completed in the required order after formatting correction: `npm test` passed 37 tests; `npm run prettier:check` reported all files formatted; `npm run build` validated 53 reports, generated 10 skills and 53 report pages, and built VitePress successfully; `npm run test:e2e` passed 32 desktop and mobile journeys with 2 expected skips; `git diff --check` produced no output.

## Documentation Impact

`website/README.md` is canonical for evaluation page behavior and now describes the clarified sections, disabled mechanism labels, result semantics, and current limitation against invented case specific explanations.

`website/content-config.json` remains unchanged because it contains catalog inclusion decisions only and has no evaluation-page rendering or explanation contract.

Root `README.md`, `CODEX_CLI.md`, and `EVALUATIONS.md` remain unchanged because this work changes only the website projection and does not change evaluation authoring, runner operation, schemas, promotion, or repository-wide user workflows.

`website/.generated/` is not a canonical source and will not be edited directly. Build and browser commands may regenerate it as disposable output.

## Deferred important follow-up: case-specific evaluation explanations

A future, separately authorized stage may declare each case's purpose, expected behavior, the role of each fixture, concrete oracle assertions, and the limits of each mechanism. That work requires an explicit architectural decision between canonical case metadata and an executable protocol of named checks.

It could affect fingerprints, current evidence, the runner, schemas, generated reports, and compatibility with historical files. Editorial explanations that are not executable can drift away from actual checks. A prior decision prohibits editorial metadata, so any future work must explicitly discuss whether to preserve, refine, or reverse that decision before implementation.

This first stage neither authorizes nor anticipates either architecture. It only clarifies mechanisms and facts already present in today's canonical contract.

## Rollout and Recovery

The change is a static website projection and requires no data migration. Normal GitHub Pages deployment will rebuild generated pages from canonical sources. Recovery consists of reverting the website source, test, documentation, and intentional snapshot changes; canonical evaluation cases and archived evidence remain untouched throughout.

## Lessons Learned

The existing generator already separates current case definitions from archived operations and exposes all fields needed for this clarification. The gap is explanatory semantics in the rendered page, not missing evaluation data.

The first public checkpoint reached all changed journeys and failed only at the four intentionally changed evaluation-flow snapshots. During snapshot regeneration, the new exact heading assertion exposed VitePress's appended permalink text in the accessible name; the page was correct, so the assertion must use the heading's stable leading name rather than exact equality.

After correcting that observation point, `npm test` passed all 37 tests and `npm run test:e2e` passed 32 desktop and mobile journeys with 2 expected project skips. The four flow snapshots are the only visual baselines changed.

The post-GREEN review found one design risk and one content defect. Consolidating the decision outcome representation removed the drift risk. A new RED assertion exposed the deterministic no fixture contradiction; the wording now states accurately that neither an executor prompt nor a starting repository is used. The restored gates passed 37 tests and 32 browser journeys with 2 expected skips.
