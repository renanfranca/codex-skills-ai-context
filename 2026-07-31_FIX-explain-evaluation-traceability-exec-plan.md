# Explain evaluation traceability

This ExecPlan is a living document. Keep `Progress`, `Decisions`, `Risks`, and `Lessons Learned` up to date as work advances.

## Purpose / Big Picture

Correct the website's evaluation mental model so maintainers can distinguish executable cases from optional traceability metadata. A reader of the `refactor-design` skill and case pages should see that `suite.json` selects 12 executable cases, while `coverage.json` records many to many mappings that neither select nor score execution.

## Scope

In scope are canonical evaluation documentation, website content generation, evaluation page presentation and help, unit content tests, desktop and Pixel 7 browser coverage, and intentionally affected snapshots. Existing uncommitted website work is preserved and audited as partial implementation.

Out of scope are skill behavior, every `coverage.json`, runner selection and execution, fingerprints, evidence states, archived reports, routes, commits, pushes, deployments, and publication.

## Definitions

`suite.json` is the ordered manifest of executable case IDs. `coverage.json` is an optional skill level traceability manifest. A skill contract mapping links one skill contract to one case; a rubric family mapping links one rubric family to one case. Both relations are many to many, so the same `case_id` may appear several times. `dimension` is only a local technical mapping label and has no execution semantics. `coverage-contract` is a deterministic structural consistency check for the traceability manifest, not semantic proof of the skill.

## Existing Context

The website generator in `website/scripts/generate-content.mjs` reads skill evaluation metadata and projects Markdown into the disposable `website/.generated/` tree. `website/scripts/evaluation-glossary.mjs` supplies contextual definitions rendered by `website/.vitepress/theme/EvaluationHelp.vue`. Public behavior is exercised by `website/tests/site-content.test.mjs` and `website/e2e/site.spec.mjs`.

At plan creation, the worktree already contains uncommitted edits in those files, `website/README.md`, CSS, and the mobile snapshot. They appear to be an earlier partial attempt and must not be overwritten or assumed correct. Root `EVALUATIONS.md` has not yet received the requested canonical explanation.

## Desired End State

The `refactor-design` skill page always shows 12 executable cases, 11 skill contracts with 21 mappings, and 5 rubric families with 8 mappings, followed by an explicit statement that traceability cannot affect execution. Active case pages show filtered human readable contract and rubric family mappings, retain slugs for audit, rename Dimension to Mapping label, explain Kind and evidence mechanisms in visible copy and contextual help, omit all traceability UI when no `coverage.json` exists, and omit executor exit code for deterministic cases. The public data model keeps `evaluation.coverage`, adds `evaluation.rubricCoverage`, and adds `skill.traceability`.

## Milestones

### Milestone 1 - Specify and generate the traceability model

#### Goal

Prove and implement the public generated content and data model for suite versus coverage semantics.

#### Changes

- [x] Audit and complete behavior tests in `website/tests/site-content.test.mjs`.
- [x] Update `website/scripts/evaluation-catalog.mjs`, `website/scripts/generate-content.mjs`, and `website/scripts/evaluation-glossary.mjs`.
- [x] Update presentation support in `website/.vitepress/theme/EvaluationHelp.vue` and `website/.vitepress/theme/custom.css` only as required by the observable contract.
- [x] Do not edit `website/.generated/` directly.

#### Validation

- [x] Command: `cd website && npm test`
- [x] Expected result: all 37 generated content tests pass with the requested traceability counts, labels, omissions, and definitions.

#### Acceptance Criteria

- [x] Generated skill and case pages expose the requested visible behavior.
- [x] Public model fields preserve contract mappings and add rubric mappings and skill summary.

### Milestone 2 - Validate the user journey and reconcile documentation

#### Goal

Make the explanation usable with mouse, keyboard, desktop, and Pixel 7 layouts, then align canonical documentation.

#### Changes

- [x] Complete `website/e2e/site.spec.mjs` for visible content, help keyboard behavior, Escape, focus restoration, and overflow.
- [x] Update snapshots only when the covered region changes intentionally.
- [x] Add the canonical suite versus coverage explanation to root `EVALUATIONS.md`.
- [x] Align `website/README.md` with the final public projection.

#### Validation

- [x] Command: `cd website && npm run test:e2e`
- [x] Expected result: 31 desktop and Pixel 7 tests pass, with the one viewport specific test skipped as declared.

#### Acceptance Criteria

- [x] The explanation is readable without opening contextual help.
- [x] Contextual help is keyboard accessible and restores focus after Escape.
- [x] Canonical documentation matches the generated behavior.

### Milestone 3 - Design review and final validation

#### Goal

Review the green implementation for structural risk, apply only behavior preserving improvements, and validate the complete change.

#### Changes

- [x] Run the `refactor-design` workflow after unit and public checkpoints are green.
- [x] Reconcile all canonical documentation sources after any refactor.
- [x] Record final evidence and remaining risks here.

#### Validation

- [x] Command: `cd website && npm test` passed with 37 tests.
- [x] Command: `cd website && npm run prettier:check` passed after formatting the two changed JavaScript files.
- [x] Command: `cd website && npm run build` validated 53 archived reports, generated 10 skills, and completed the VitePress build.
- [x] Command: `cd website && npm run test:e2e` passed 31 tests with one declared viewport specific skip.
- [x] Command: `git diff --check` passed with no output.
- [x] Command: `git -C _temporary/codex-skills-ai-context diff --check` passed with no output.

#### Acceptance Criteria

- [x] Every command passes in the required order.
- [x] No skill or `coverage.json` changed.
- [x] No commit, push, deploy, or publication occurred.

## Progress

- [x] Repository and nested instructions loaded.
- [x] Required memory worktree and exact origin verified.
- [x] Existing uncommitted changes identified and preserved.
- [x] Milestone 1 started.
- [x] Milestone 1 completed.
- [x] Milestone 2 started.
- [x] Milestone 2 completed.
- [x] Milestone 3 started.
- [x] Milestone 3 completed.

## Decisions

- Decision: Treat the existing website diff as partial user owned work and audit it in place.
  Rationale: Repository instructions prohibit discarding unrelated or preexisting edits, and the diff overlaps the requested behavior.
  Date/Author: 2026-07-31 / Codex

- Decision: Preserve `evaluation.coverage` and add separate rubric coverage and skill summary fields.
  Rationale: This maintains the existing public model while representing both traceability axes explicitly.
  Date/Author: 2026-07-31 / Codex

- Decision: Humanize slugs through one exported catalog transformation and retain the exact identifier beside each label in code formatting.
  Rationale: The post GREEN design review found duplicated slug conversion in the catalog and renderer, which could make labels diverge over time.
  Date/Author: 2026-07-31 / Codex

- Decision: Update both evaluation flow snapshots.
  Rationale: Browser inspection showed intentional wrapping that keeps stage labels within their cards; the previous desktop snapshot captured overlapping labels and the existing mobile change covered the corresponding responsive projection.
  Date/Author: 2026-07-31 / Codex

## Risks and Mitigations

- Risk: Existing partial tests may already be green, obscuring original RED evidence.
  Mitigation: Each newly specified model and page behavior was observed failing against the partial implementation before the minimum production change restored the full suite.

- Risk: Humanized labels may hide exact identifiers needed for audits.
  Mitigation: Render readable names together with original slugs in code formatting and assert both.

- Risk: Help popovers or long slugs may overflow on Pixel 7.
  Mitigation: Exercise the real page in both configured browser projects and assert document width.

- Risk: Generated output may be mistaken for a canonical source.
  Mitigation: Edit generator and documentation sources only, and let npm commands regenerate the disposable projection.

## Validation Strategy

Use `npm test` for every behavior TDD cycle. Once no milestone behavior remains, run `npm run test:e2e` as the public checkpoint. After the post GREEN design review and documentation reconciliation, run `npm test`, `npm run prettier:check`, `npm run build`, and `npm run test:e2e` in exactly that order, then `git diff --check` in both worktrees.

## Documentation Impact

`EVALUATIONS.md` now defines selection versus traceability, many to many mappings, repeated `case_id`, local `dimension`, and the limited role of `coverage-contract`. `website/README.md` now describes the visible projection and public `coverage`, `rubricCoverage`, and `traceability` fields. Applicable public configuration consists of `suite.json`, `coverage.json`, and case manifests; none changed because this task explains their existing meaning. Root `README.md` and `CODEX_CLI.md` remain accurate because neither defines this website presentation or changes runner commands.

## Rollout and Recovery

This is a static documentation website change. Rollout occurs through the repository's normal site publication after review, which is outside this task. Recovery is a normal revert of the website and documentation diff; no data or runtime migration exists.

## Lessons Learned

- The target worktree arrived with a substantial partial implementation but without the required ExecPlan, so verification must establish which requested behaviors are still missing rather than blindly repeating edits.
- A `coverage.json` mapping needs both a human readable presentation and its exact slug; using one shared transformation avoids divergence between page titles, contract labels, families, and mapping labels.
- The old desktop flow snapshot allowed adjacent stage labels to overlap. The intentional responsive wrapping is clearer and required updating the desktop snapshot alongside the already changed mobile snapshot.
- The first final formatting check failed on two changed JavaScript files. Running the repository formatter on only those files and restarting the full validation sequence produced a clean final run.
