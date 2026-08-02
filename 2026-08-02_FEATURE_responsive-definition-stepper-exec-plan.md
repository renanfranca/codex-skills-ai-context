# Build the responsive evaluation definition stepper

This ExecPlan is a living document. Keep `Progress`, `Decisions`, `Risks and Mitigations`, and `Lessons Learned` current as work advances.

## Purpose / Big Picture

Active evaluation pages currently show the definition path as a compact card flow that shares styling with the latest operation flow. This change gives the current definition its own accessible stepper: horizontal across one row on desktop, vertical on narrow screens, with explicit numbered nodes, connecting arrows, plain language descriptions, and a distinct result terminal. A reader can observe the result on any active evaluation page and can navigate every stage by keyboard without horizontal overflow.

## Scope

In scope are the generated markup for the current definition flow, its canonical website CSS, generator tests, Playwright behavior checks, and light and dark visual snapshots. The stable `.definition-flow` hook remains. The latest operation flow retains its existing markup and cards. Generated files under `website/.generated/` are disposable projections and will not be edited directly.

Out of scope are Vue or JavaScript state, progress animation, new dependencies, changes to evaluation schemas, a redesign of the latest operation flow, and the separate 320 px navigation issue. No commit, push, publication, or deployment is authorized.

## Definitions

A definition stepper is the static navigation that connects an evaluation's declared public input to its verification mechanisms and possible results. It describes configuration, not live execution progress. A stage is one rendered list item. `--flow-stages` is a CSS custom property containing the actual stage count so the layout supports three through six stages without schema changes. The result terminal is the final stage and displays PASS, FAIL, ERROR, and SKIPPED as text chips so meaning does not rely on color.

## Existing Context

`website/scripts/generate-content.mjs` is the canonical generator for evaluation pages. Its `renderEvaluationFlow` currently emits `.evaluation-flow.definition-flow` links with only an ordinal and title, while `renderEvaluationPage` labels the section “Current definition flow.” `website/.vitepress/theme/custom.css` styles both current definition and latest operation flows through shared `.evaluation-flow` rules. `website/tests/site-content.test.mjs` checks generated public content. `website/e2e/site.spec.mjs` checks the built public site and owns Playwright screenshots. Website commands run from `website/`.

## Desired End State

The section is titled “How this evaluation runs” and introduces the static definition with the approved sentence. Each applicable stage has an explicit `aria-hidden` numbered node, link, title, and description. The list has only stepper-specific classes plus stable `.definition-flow`, carries the real stage count in `--flow-stages`, and marks the last item `.definition-step-result`. Desktop displays one unwrapped column per stage with gradient connectors and arrows. Below 640 px it becomes a vertical timeline. The result node and four status chips are visually and textually distinct. Focus is clearly visible, reduced motion is respected, native decimal markers are hidden, and neither viewport overflows horizontally.

## Milestones

### Milestone 1 - Specify and generate the public stepper contract

#### Goal

Protect the generated public content before changing production markup, then implement the smallest generator change that satisfies it.

#### Changes

- [x] Update `website/tests/site-content.test.mjs` with observable assertions for title, introduction, exclusive classes, descriptions, real stage count, result terminal, statuses, and Judge omission.
- [x] Update `website/scripts/generate-content.mjs` to emit the new stepper contract without changing evaluation schemas or latest operation markup.
- [x] Leave canonical documentation unchanged during this milestone because the page generator remains the public source of the presentation contract.

#### Validation

- [x] Command: `npm test`
- [x] Expected result: the new assertions first fail against old markup, then the full suite passes after generator implementation.

#### Acceptance Criteria

- [x] A complete behavioral evaluation renders six linked stages with the approved descriptions and status terminal.
- [x] A Judge-disabled evaluation omits Judge and reports its lower real stage count.

### Milestone 2 - Implement responsive presentation and public checks

#### Goal

Render the generated contract as the refined desktop stepper and mobile timeline, with accessible keyboard behavior and durable visual coverage in both themes.

#### Changes

- [x] Replace only definition-flow styling in `website/.vitepress/theme/custom.css` with dedicated stepper rules while preserving operation-flow cards.
- [x] Expand `website/e2e/site.spec.mjs` to check markers, counts, descriptions, result text, Judge omission, keyboard focus, and overflow.
- [x] Refresh desktop and mobile light snapshots and add equivalent dark snapshots for the complete six-stage scenario.
- [x] Do not edit `website/.generated/` directly; allow website commands to regenerate it.

#### Validation

- [x] Command: `npm test`
- [x] Command: `npm run test:e2e`
- [x] Expected result: unit/content tests and the public Playwright checkpoint pass, including all four stepper snapshots.

#### Acceptance Criteria

- [x] Desktop remains one row with visible nodes, connectors, arrows, centered copy, and no overflow.
- [x] Mobile becomes a vertical timeline with downward arrows, preserved descriptions, distinct result terminal, and no overflow.
- [x] Light and dark snapshots cover the complete six-stage evaluation.

### Milestone 3 - Review design, reconcile documentation, and validate

#### Goal

Review the green implementation for structural risk, reconcile all declared canonical documentation sources, and run the repository's required validation sequence.

#### Changes

- [x] Apply `refactor-design` only after `npm test` and `npm run test:e2e` are green and all behavior is complete.
- [x] Inspect `website/README.md`, `website/content-config.json`, and root documentation; update only if their canonical facts changed and otherwise record concrete no-change reasons.
- [x] Record exact validation evidence and lessons in this plan.

#### Validation

- [x] Command: `npm test` passed 37 tests.
- [x] Command: `npm run prettier:check` reported all matched files use Prettier style.
- [x] Command: `npm run build` validated 53 reports, generated 10 skills and 53 reports, and completed the VitePress build.
- [x] Command: `npm run test:e2e` passed 32 tests with 2 expected skips across desktop Chromium and Pixel 7 projects.
- [x] Expected result: every command passed in the required order.

#### Acceptance Criteria

- [x] Design review finds no unresolved defect or material structural risk.
- [x] Every canonical documentation source has an explicit disposition.
- [x] The complete final validation sequence is green.

## Progress

- [x] Repository and nested instructions read; workflow profile confirmed.
- [x] Required memory worktree and exact origin validated.
- [x] Milestone 1 started.
- [x] Milestone 1 completed: generator contract was RED, then `npm test` passed all 37 tests.
- [x] Milestone 2 started.
- [x] Milestone 2 completed: four snapshots were inspected and the complete public checkpoint passed 32 tests with 2 expected skips.
- [x] Milestone 3 started.
- [x] Milestone 3 completed.

## Decisions

- Decision: Use CSS and static generated HTML only.
  Rationale: The flow explains a declaration and has no interactive execution state; runtime state or animation would communicate a false progress model.
  Date/Author: 2026-08-02 / Codex

- Decision: Preserve `.definition-flow` but remove `.evaluation-flow` from this component.
  Rationale: Existing consumers retain a stable selector while definition and operation flows no longer share an accidental presentation contract.
  Date/Author: 2026-08-02 / Codex

- Decision: Keep Oracle as canonical vocabulary and explain it in plain language.
  Rationale: Renaming the mechanism would conflict with repository configuration; a description improves comprehension without changing the domain model.
  Date/Author: 2026-08-02 / Codex

- Decision: Apply the visible outline to both `:focus` and `:focus-visible`.
  Rationale: Chromium exposed `outline: none` for programmatic focus when the rule depended only on `:focus-visible`. A visible outline for every focused stage is a stronger cross-input accessibility contract, while the E2E test returns focus through a keyboard Tab before asserting it.
  Date/Author: 2026-08-02 / Codex

- Decision: Classify the post-GREEN design review as No action.
  Rationale: The changed generator data is local, ordered, and explicit; the CSS now has separate definition and operation boundaries; and no temporal coupling, hidden state, fragile mapping, duplicated transformation, or mixed policy creates a concrete risk. New helpers or constants would add indirection without strengthening an invariant.
  Date/Author: 2026-08-02 / Codex

## Risks and Mitigations

- Risk: VitePress list styling may reveal decimal markers.
  Mitigation: Use a scoped `.vp-doc ol.definition-stepper` reset with enough specificity and verify computed marker content through Playwright.

- Risk: Six desktop stages may overflow or wrap at intermediate widths.
  Mitigation: Use `--flow-stages` with a true grid column per stage, constrain copy, switch below 640 px, and assert document width in E2E.

- Risk: Dark theme gradients, halos, and chips may lose contrast.
  Mitigation: Derive colors with existing brand variables and `color-mix()`, then retain dark desktop and mobile snapshots.

- Risk: Snapshot updates could mask unrelated visual regressions.
  Mitigation: Scope screenshots to `.definition-flow`, inspect new images, and keep the scenario fixed to the complete six-stage flow.

## Validation Strategy

Use `npm test` for every RED and GREEN cycle. After generator and CSS behavior are complete, run `npm run test:e2e` as the public checkpoint. Inspect targeted screenshots in light and dark themes. After the post-GREEN design review and documentation reconciliation, run exactly `npm test`, `npm run prettier:check`, `npm run build`, and `npm run test:e2e` from `website/`.

## Documentation Impact

- `website/README.md`: inspected and left unchanged. It already states that active pages show a linked current definition flow, that semantic and deterministic stages vary by applicability, and that the flow uses static semantic HTML and CSS with visible focus, responsive stacking, and reduced motion. The new stepper changes presentation without invalidating any of those facts or changing commands or architecture.
- `website/content-config.json`: inspected and left unchanged. It contains only the site base path and disabled compatibility skills. The stage count is derived from existing case definitions and adds no public configuration key or schema.
- Root `README.md`: inspected and left unchanged because repository purpose, website entry points, and contribution workflow are unchanged.
- Root `CODEX_CLI.md`: inspected and left unchanged because skill discovery, CLI operation, and user workflows are unaffected.
- Root `EVALUATIONS.md`: inspected and left unchanged because Prompt, Fixture, Executor, Mechanical checks, Oracle, Judge, and result semantics are unchanged; only their generated website navigation presentation changed.
- `website/scripts/generate-content.mjs` and `website/.vitepress/theme/custom.css` are the canonical code sources for generated page markup and presentation. `website/.generated/` will remain an unedited projection.

## Rollout and Recovery

The static site build is the rollout artifact. There is no data migration or runtime dependency. Recovery consists of reverting the generator, CSS, tests, and snapshots together, then regenerating the site through normal website commands. No generated projection should be hand restored.

## Lessons Learned

- The current definition and latest operation flows share `.evaluation-flow` even though they represent different concepts; preserving `.definition-flow` while separating their styling is the narrowest compatible boundary.
- The first public RED failed on the computed native list style (`decimal`), proving that the VitePress reset needed a dedicated high-specificity rule rather than relying on the old shared flow styles.
- A mobile dark-theme screenshot must close VitePress's navigation drawer after using its appearance switch; otherwise the drawer can cover a correctly rendered target and produce a misleading snapshot.
