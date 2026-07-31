# Expose the evaluation catalog and auditable flows

This ExecPlan is a living document. Keep `Progress`, `Decisions`, `Risks and Mitigations`, `Documentation Impact`, validation evidence, and `Lessons Learned` current as work advances.

## Purpose / Big Picture

The public website currently presents each skill and its archived operations, but it does not present the evaluation cases declared by the skill as first class domain objects. A reader can inspect what happened in an operation but cannot start from a case to learn what it protects, how the runner evaluates it, whether current evidence exists, or which archived operations contain observations for it.

This change adds an evaluation catalog to every skill without removing existing content. A reader will be able to open an active evaluation, understand its current definition through an accessible flow, distinguish current evidence from the latest recorded result, and inspect every related operation. Cases found only in archived reports will remain available as explicitly historical evaluations. The existing `/evaluations/` route and report URLs remain stable but will use the accurate public label “Operations”.

The behavior is observable by building or serving `website/`, opening `/codex-skills/skills/refactor-design`, selecting `hidden-invocation-state`, and seeing its current evidence state, current definition, latest related operation, flow, verification details, and complete operation history.

## Scope

In scope:

- Discover active evaluation cases from each skill's current `evals/suite.json`.
- Read existing case manifests, public prompts, public fixture paths, optional coverage maps, and archived reports without modifying those sources.
- Project active evaluations, never executed evaluations, and archived only historical evaluations into generated website data and pages.
- Derive case level current evidence using both the current skill source fingerprint and current case fingerprint.
- Add responsive accessible flows for current evaluation definitions and the latest related operation.
- Preserve and clarify the distinction among an evaluation case, an observation, and an operation.
- Rename public operation labels and headings while preserving existing operation routes and a compatibility anchor for `#evaluation-history`.
- Add behavior tests, public browser journeys, visual coverage, styling, and canonical website documentation.

Out of scope:

- Changing any skill, suite, `case.json`, prompt, fixture, oracle, coverage map, evaluation runner, evaluation schema, or archived report.
- Adding editorial metadata to evaluation cases or introducing website maintained descriptions.
- Reconstructing an unavailable historical case definition from archived evidence.
- Adding Mermaid, another dependency, or an interactive operation selector.
- Editing `website/.generated/` or build artifacts directly.
- Consuming model sessions, committing, pushing, publishing, or deploying.

## Definitions

An **evaluation case** is the persistent contract identified by a `case_id` and currently declared by a skill's `evals/suite.json`.

An **active evaluation** is a case declared in the current suite and backed by a readable current case directory.

A **historical evaluation** is a `case_id` found in an archived observation but absent from the skill's current suite. Its archived facts remain inspectable, but the website does not claim to possess its former complete definition.

An **observation** is one case result inside an operation. It has a role such as `baseline`, `candidate`, `regression`, or `observation`, and may also have a repetition number.

An **operation** is one archived invocation of the evaluation runner. One operation can contain observations for several cases or several observations for the same case.

The **current skill fingerprint** is the canonical SHA-256 identity of the current skill directory using the same file, mode, ordering, Unicode, and byte framing rules as the evaluation runner.

The **current case fingerprint** is the canonical SHA-256 identity of the current case directory, including its manifest, prompt, fixture, oracle, and file modes.

A **compatible observation** belongs to a report whose evaluated or candidate skill fingerprint matches the current skill and whose archived case fingerprint matches the current case. Missing fingerprints never match by inference.

The **latest related operation** is the newest archived operation containing at least one observation for the case, ordered by recorded `started_at` and then operation ID for a deterministic tie break.

The **public checkpoint** is `npm run test:e2e` from `website/`. It builds the generated projection and exercises it in desktop Chromium and an emulated Pixel 7.

## Existing Context

`website/scripts/generate-content.mjs` discovers skills through top level `SKILL.md` files, normalizes `evaluation-reports/**/report.json`, derives skill level evidence, and writes disposable Markdown and `data.json` beneath `website/.generated/`. It reads current suite membership for coverage counts but does not read case definitions, prompts, fixtures, or coverage maps as website content.

The current generated skill page labels a list of complete archived operations as “Evaluation history”. Report pages live at `/evaluations/<skill>/<operation-id>`, and the `/evaluations/` index also lists operations. These URLs are public interfaces and must remain valid.

The existing skill evidence model compares a report's evaluated or candidate source fingerprint with the current skill fingerprint. It intentionally gives stronger evidence precedence without hiding conflicting current results. Case level evidence must preserve that principle while adding case fingerprint compatibility.

`website/tests/site-content.test.mjs` exercises the content generator through temporary repositories and generated output. `website/e2e/site.spec.mjs` exercises public journeys, keyboard behavior, themes, viewport constraints, and approved visual snapshots. `website/.vitepress/theme/custom.css` owns the visual system, while `website/.vitepress/config.mjs` owns public navigation.

Only `refactor-design` currently declares `evals/coverage.json`. Other active suites must still receive useful pages based on their current suite, manifest, prompt, fixture, and reports. The archive already contains cases that are no longer active and an active case with no archived observation, so both states are real rather than hypothetical.

The initial relevant suite is green: `npm test` passed 24 tests on 2026-07-30 before implementation began.

## Desired End State

Each generated skill retains its existing properties and adds `evaluations` and `historicalEvaluations`.

Every active evaluation contains:

- skill ID, case ID, sentence case title, route, active state, and case kind;
- current case fingerprint;
- public prompt when the case kind uses an executor;
- public fixture paths without inferred summaries;
- mechanical expected exit code, required paths, protected changed paths, and commands;
- oracle applicability and declared commands;
- judge applicability, criteria, and `no_action_acceptable`;
- reverse coverage mappings when `coverage.json` declares them;
- current evidence state;
- the latest related operation;
- all related operations, with observations grouped under their one operation.

Every historical evaluation contains the skill ID, case ID, sentence case title, historical state, route, latest related operation, and all related operations. It does not expose a current prompt, flow, fixture, mechanical contract, oracle, judge contract, coverage claim, or current case fingerprint.

The case evidence state uses this priority:

1. **Validated promotion**: both fingerprints match, the operation is a promotion eligible `PASS`, and the case has passing `candidate` observations. A regression observation does not establish promotion for that case.
2. **Current pass**: both fingerprints match and at least one nonbaseline observation records `PASS`, without a qualifying promotion.
3. **No current pass**: compatible observations exist but no nonbaseline compatible observation records `PASS`.
4. **Historical runs**: related observations exist but none is compatible with both current fingerprints.
5. **Not evaluated yet**: no archived observation exists for the case.

The skill page presents active evaluation cards before historical evaluations and complete operation history. Cards show kind, evidence state, latest recorded result, related operation count, and applicable mechanisms. The operation history preserves all existing rows. New links use `#operation-history`; an empty compatibility target retains `#evaluation-history`.

An active evaluation page presents:

1. current evidence as the primary status and the latest recorded result as secondary information;
2. case identity and suite membership;
3. mapped skill contracts and limitations, or an explicit statement that no coverage map is declared;
4. an accessible linked flow for the current definition;
5. the public prompt and fixture file list;
6. mechanical, oracle, and judge verification details;
7. a flow and grouped observations for the latest related operation;
8. one history row per related operation, linking to the existing report page.

The current definition flow is semantic HTML styled with CSS. It links each stage to a detailed section, exposes visible keyboard focus, uses text in addition to color, and adapts to narrow screens without document overflow. Semantic cases show prompt and fixture, executor, mechanical checks, optional oracle, optional judge, and result branches. Deterministic cases omit executor and judge and show their deterministic inputs, checks, optional oracle, and result. Failure and skipped branches reflect actual runner ordering.

The latest operation flow aggregates only observations for the selected case. Stage summaries report counts rather than choosing one observation arbitrarily. The case result summary remains separate from the complete operation result, so a case `PASS` inside an operation `FAIL` is presented truthfully.

The public `/evaluations/` route, report routes, and archived evidence remain unchanged. Navigation, headings, and links call that archive “Operations”.

## Milestones

### Milestone 1 - Build the evaluation catalog model

#### Goal

Generate a complete factual model for active and historical evaluations while preserving every existing generated property and route.

#### Changes

- Extend `website/tests/site-content.test.mjs` one behavior at a time for active discovery, never executed cases, archived only cases, malformed suite declarations, current source and case fingerprint matching, baseline exclusion, regression limits, promotion precedence, related operation grouping, latest operation selection, and missing archived fields.
- Add `website/scripts/evaluation-catalog.mjs` as the canonical website module for loading current case definitions, reversing optional coverage mappings, calculating case evidence, and grouping observations by operation.
- Extend `website/scripts/generate-content.mjs` to preserve the observation fields required by the catalog, attach `evaluations` and `historicalEvaluations` to discovered skills, write the new model to `data.json`, and generate evaluation case routes.
- Extend `website/scripts/evaluation-glossary.mjs` with the closed case evidence vocabulary and validate that every generated case evidence state has a definition.
- Keep unknown archived report shapes explicit as `Not recorded`; do not infer missing compatibility, runtime, duration, or verification facts.
- Do not edit canonical documentation during the RED and GREEN cycles. Record documentation impact in this plan and reconcile it after the behavior and design gates are green.

#### Validation

From `website/`, run `npm test` for every RED and GREEN. Each new test must fail for the missing public generator behavior before production code is added. At milestone completion, run `npm test` and `npm run test:e2e`.

#### Acceptance Criteria

- Current suite cases appear even without archived observations.
- Archived only cases are separated from active cases.
- Current evidence requires exact source and case compatibility.
- One operation containing several observations appears once with all observations retained in report order.
- Existing skill, report, evidence, glossary, route, and compatibility data remains available.
- The relevant suite and public checkpoint are green.

### Milestone 2 - Deliver pages, linked flows, and operation terminology

#### Goal

Make every evaluation understandable from its skill page and fully traceable to existing operation evidence on desktop and mobile.

#### Changes

- Extend behavior assertions in `website/tests/site-content.test.mjs` for active and historical Markdown pages, current and latest flows, escaped source content, truthful case versus operation results, legacy anchors, and preserved report links.
- Extend `website/scripts/generate-content.mjs` renderers for evaluation cards, historical evaluation cards, active detail pages, reduced historical pages, definition flows, latest operation flows, verification details, and grouped operation history.
- Update `website/.vitepress/config.mjs` so the existing archive navigation label is “Operations” without changing its route.
- Update `website/.vitepress/theme/custom.css` with evaluation card, evidence summary, flow, branch, mechanism, operation summary, coverage, responsive, focus, light theme, dark theme, and reduced motion styles.
- Extend `website/e2e/site.spec.mjs` with journeys from `refactor-design` to `hidden-invocation-state`, an active never evaluated case, a historical evaluation, flow anchors, related operation navigation, operation terminology, keyboard focus, themes, and viewport overflow.
- Add one approved evaluation flow screenshot expectation that runs in both Playwright projects and therefore records desktop and Pixel 7 output. Update existing snapshots only when the navigation label creates an intentional visible difference.
- Keep the flow static and linked. Do not add a Vue component, client side operation selector, Mermaid, or another dependency.
- Do not edit `website/.generated/`; use the established generation and build commands.

#### Validation

Run `npm test` for every RED and GREEN. Run `npm run test:e2e` after at most two successful behavior cycles and at milestone completion. The public checkpoint must exercise both desktop and mobile projects.

#### Acceptance Criteria

- A reader can reach every active evaluation from its skill page.
- `hidden-invocation-state` displays historical current evidence, a latest case `PASS`, and its complete operation `FAIL` without conflating them.
- Flow stages are understandable without color, reachable by keyboard, linked to visible sections, and free of horizontal document overflow.
- A never executed active evaluation says `Not evaluated yet`.
- A historical evaluation never presents the current definition as its historical definition.
- `/evaluations/` and every report URL still work and are labeled as operations.
- The relevant suite and public checkpoint are green.

### Milestone 3 - Review design, reconcile documentation, and validate

#### Goal

Confirm the completed behavior has sound responsibilities, accurate canonical documentation, and a fully green repository profile.

#### Changes

- Confirm every milestone behavior is complete, `npm test` is green, `npm run test:e2e` is green, and no behavior remains pending.
- Load `refactor-design`, read its complete design review rubric, inspect only changed files and adjacent responsibilities, classify findings, and apply only behavior preserving improvements supported by existing behavior tests.
- If design review discovers missing behavior, stop the review, return to behavior TDD, restore the relevant suite and checkpoint to GREEN, and restart the design gate.
- Update `website/README.md` with the new canonical content sources, generated data additions, case evidence states, routes, active versus historical behavior, flow behavior, and evaluation versus observation versus operation terminology.
- Update the opening website description in root `README.md` so it describes declared evaluations as well as archived operation evidence.
- Inspect `website/content-config.json`, `CODEX_CLI.md`, and `EVALUATIONS.md` and record their final update or concrete no change justification under `Documentation Impact`.
- Confirm that no canonical skill or evaluation source changed and that `website/.generated/` remained a disposable projection.

#### Validation

After design and documentation are complete, run the required final validation from `website/` in this exact order:

1. `npm test`
2. `npm run prettier:check`
3. `npm run build`
4. `npm run test:e2e`

Then run `git diff --check` from `/home/renanfranca/.codex/skills` and `git -C _temporary/codex-skills-ai-context diff --check` from the same repository root.

#### Acceptance Criteria

- Design review findings and any refactors are recorded in this plan.
- Every canonical documentation source has an update or an explicit accurate no change reason.
- All final validation commands pass in the required order.
- No skill fingerprint or case fingerprint changed.
- No commit, push, publication, or deployment occurred.

## Progress

- [x] Milestone 1 started
- [x] Milestone 1 completed
- [x] Milestone 2 started
- [x] Milestone 2 completed
- [x] Milestone 3 started
- [x] Post GREEN design review completed
- [x] Documentation reconciled
- [x] Final validation completed
- [x] Milestone 3 completed

## Decisions

- Decision: Use only current canonical evaluation sources and archived reports; add no editorial metadata.
  Rationale: Adding metadata to `case.json` or another file inside a skill would change skill and case fingerprints, making existing evidence historical for an editorial change.
  Date/Author: 2026-07-30 / Renan and Codex

- Decision: Make current evidence the primary status and the latest recorded result secondary.
  Rationale: A historical `PASS` is an execution fact, not proof about the current skill and case.
  Date/Author: 2026-07-30 / Renan and Codex

- Decision: Require both source and case fingerprint compatibility.
  Rationale: Source compatibility alone cannot prove that the current case definition produced the archived result, while case compatibility alone cannot prove that the current skill was evaluated.
  Date/Author: 2026-07-30 / Renan and Codex

- Decision: Show active and historical evaluations in separate skill page sections.
  Rationale: Active cases need current definitions and current evidence; archived only cases need durable history without invented definitions.
  Date/Author: 2026-07-30 / Renan and Codex

- Decision: Keep one history row per operation and group all case observations under it.
  Rationale: Promotion and diagnostic operations can contain baseline, candidate, regression, and repeated observations for one case. Repeating the operation row would distort the domain model.
  Date/Author: 2026-07-30 / Renan and Codex

- Decision: Use linked semantic HTML and CSS flows, with only the latest related operation represented.
  Rationale: This satisfies the teaching and auditability goals with keyboard and mobile support while avoiding a new client side application, Mermaid, and dependency cost.
  Date/Author: 2026-07-30 / Renan and Codex

- Decision: Rename the existing archive to “Operations” while preserving routes.
  Rationale: The current archive contains operations, not evaluation definitions. Stable URLs preserve compatibility while accurate terminology removes the ambiguity.
  Date/Author: 2026-07-30 / Renan and Codex

- Decision: Keep the public interface in English.
  Rationale: The existing website, glossary, reports, navigation, and source contracts are English, and this feature does not introduce localization.
  Date/Author: 2026-07-30 / Codex

- Decision: Aggregate different observation statuses in the latest recorded case result instead of selecting the last observation.
  Rationale: One operation may contain several observations for one case, and report order does not make one result representative of the case. A single shared summary now drives cards, status panels, and the latest operation flow.
  Date/Author: 2026-07-31 / Codex

- Decision: Pass the already validated suite case snapshot from skill evidence derivation into the evaluation catalog.
  Rationale: Reading and validating `suite.json` twice introduced duplicated policy and a time of check/time of use risk without adding useful freshness semantics.
  Date/Author: 2026-07-31 / Codex

## Risks and Mitigations

- Risk: The website could present a case result as current when either the skill or case changed.
  Mitigation: Require exact equality for both fingerprints and test mismatched, absent, candidate, evaluated, and baseline variants.

- Risk: A promotion regression observation could be mislabeled as a validated promotion for that case.
  Mitigation: Require passing `candidate` observations in a promotion eligible passing operation; regression observations may establish only a current pass.

- Risk: A complete operation can fail while one case passes, or one operation can contain several different case results.
  Mitigation: Render case observation summaries and complete operation status as separate labeled facts and exercise the real `hidden-invocation-state` example.

- Risk: Historical cases could be shown with the current definition of a reused identifier.
  Mitigation: Build active definitions only from the current suite and current case directory. Historical pages never claim a current definition represents archived execution.

- Risk: Arbitrary prompt, criteria, command output, or archived evidence could become executable markup.
  Mitigation: Reuse and extend the generator's escaping contract and add hostile text fixtures to behavior tests.

- Risk: Listing fixture or oracle paths could follow unexpected paths outside the intended case directory.
  Mitigation: Use repository relative paths, reject resolved paths outside the case directory where content is read, and link only canonical repository paths. Do not inline complete oracle source.

- Risk: The generated flow could be readable visually but inaccessible to keyboard or mobile users.
  Mitigation: Use semantic ordered structures and links, visible focus, text labels, responsive stacking, reduced motion support, browser keyboard journeys, and overflow assertions.

- Risk: Adding catalog responsibilities could make the existing generator harder to maintain.
  Mitigation: Put source loading, evidence derivation, and operation grouping in `evaluation-catalog.mjs`; keep rendering and output coordination in `generate-content.mjs`; run post GREEN design review before final validation.

- Risk: Navigation and screenshot changes could create unrelated visual churn.
  Mitigation: Preserve routes and established design tokens, add only one new flow snapshot per browser project, and update existing snapshots only for intentional label changes.

- Risk: Tests or generated builds could modify disposable files and obscure the canonical diff.
  Mitigation: Never edit `.generated` directly, inspect `git status` before completion, and report generated or cache artifacts separately from authored changes.

## Validation Strategy

Behavior TDD uses `website/tests/site-content.test.mjs` through the content generator command as the highest useful stable observation point. Each cycle adds one user observable behavior, runs the complete `npm test` suite for an expected RED, adds the minimum implementation, and reruns the complete suite for GREEN.

The public path uses `website/e2e/site.spec.mjs` against a built VitePress site. Run `npm run test:e2e` after at most two behavior cycles and at every milestone boundary. Browser coverage includes desktop Chromium and Pixel 7, keyboard navigation, themes, viewport bounds, report navigation, and visual flow appearance.

No lower level test should be added merely because `evaluation-catalog.mjs` exists. Tests remain organized around generated data, generated pages, and public journeys. If an intentionally stable reusable API emerges, record the decision before adding a lower level test.

After behavior and public checkpoints are green, run the post GREEN design review. Reconcile canonical documentation only after behavior and design stabilize. Run final validation in the repository declared order and record exact results here.

## Documentation Impact

- `website/README.md`: updated with canonical suite, case, prompt, fixture, optional coverage, and report sources; evaluation, observation, and operation terminology; case evidence states; active and historical routes; generated model responsibilities; linked flow behavior; result aggregation; compatibility anchors; and Operations terminology.
- `website/content-config.json`: unchanged. The feature adds no base path, disabled skill, or public catalog override, so its two existing responsibilities remain complete.
- Root `README.md`: updated so the public website description names declared evaluation cases and archived operation evidence.
- `CODEX_CLI.md`: unchanged. The feature adds no CLI command, flag, source selection, runtime, installation, archive maintenance, or maintainer recipe.
- `EVALUATIONS.md`: unchanged. It already defines case anatomy, runner sequencing, fingerprints, observations, operations, results, durable evidence, and promotion; the website only projects that canonical contract.
- `website/.generated/`: unchanged by hand. Website commands regenerated it as a disposable projection during validation.

## Rollout and Recovery

The repository's existing GitHub Actions workflow will build and publish the static artifact only after changes reach `main`; this task does not authorize publishing. Existing routes remain valid, so rollout requires no redirect, migration, archive rewrite, or runtime feature flag.

If the new catalog blocks a build, revert the authored website changes and regenerate the disposable projection. Archived reports and canonical evaluation sources are read only in this work, so recovery does not require restoring evidence or rerunning evaluations. If one malformed current case is discovered, do not silently omit it; record the source defect and stop until the canonical case is corrected under separate authority.

## Lessons Learned

- Current website skill evidence uses the full skill tree fingerprint, so editorial changes inside an evaluation case would make archived source evidence historical even when evaluated behavior did not change.
- Case fingerprints already preserved in reports make a truthful evaluation centric index possible without changing the runner or archive.
- Real archived data contains both archived only cases and a current never observed case, so both must be modeled as ordinary states rather than exceptional fallbacks.
- `hidden-invocation-state` demonstrates why case result and operation result must remain separate: its latest matching case observations pass inside complete operations that fail because other cases fail.
- Only `refactor-design` currently declares a coverage map. Coverage explanation must therefore be additive and optional rather than required for an evaluation page.
- The preexisting website test helper declared suite IDs without creating their case manifests. The catalog correctly rejected that inconsistent canonical source, so the helper now builds minimal complete deterministic cases instead of weakening production validation.
- Milestone 1 validation on 2026-07-31: `npm test` passed 34 tests; `npm run test:e2e` rebuilt the real 53 report archive and passed 27 browser tests with 1 existing skip across desktop and mobile.
- Markdown inside a raw HTML `<pre><code>` block is reparsed after blank lines by the VitePress pipeline, which produced invalid Vue markup for a real multiline prompt. Fenced Markdown generated with a fence longer than any source backtick safely preserves arbitrary public prompts.
- Milestone 2 validation on 2026-07-31: `npm test` passed 35 tests; `npm run test:e2e` passed 29 browser tests with 1 existing desktop-only skip. New approved flow snapshots cover desktop and Pixel 7, and the existing desktop help snapshot was updated only for the intentional “Operations” navigation label.
- Post GREEN design review classified arbitrary latest observation selection as a defect and the duplicated suite read as a design risk. The defect returned through behavior TDD before the review restarted; the duplicate read was removed as a behavior preserving refactor. The final post-refactor checkpoint passed 29 browser tests with 1 existing skip.
- Final validation on 2026-07-31 passed in the required order: `npm test` passed 36 tests; `npm run prettier:check` reported every matched file formatted; `npm run build` validated all 53 archived reports and built every route; `npm run test:e2e` passed 29 browser tests with 1 existing desktop-only skip. Both repository and memory worktree `git diff --check` commands passed, and no `SKILL.md` or canonical `evals/` source changed.
- The build continues to emit the preexisting Vite chunk size advisory, and the isolated Playwright install reports 2 moderate and 1 high npm audit findings. Neither warning blocked the declared validation, and changing dependencies is outside this feature's scope.
