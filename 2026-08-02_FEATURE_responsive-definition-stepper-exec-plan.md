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

### Milestone 4 - Stabilize the visual checkpoint in CI

#### Goal

Make every existing stepper screenshot observe a neutral component state, independently from the keyboard focus and pointer states exercised by the same journey. GitHub Actions run `30754059367` and the subsequent focused experiments provide the RED evidence: the visual assertion captures the component after interaction has activated hover and focus presentation, so the snapshot can retain those transient pixels even after computed CSS values appear neutral.

#### Changes

- [x] Update `website/e2e/site.spec.mjs` to scroll the stepper into view, move the pointer outside it, assert that no stage link matches `:hover`, and capture the existing light snapshot.
- [x] Switch theme, close mobile navigation when applicable, and capture the existing dark snapshot before any keyboard focus interaction.
- [x] Exercise Tab and Shift+Tab focus plus the visible outline only after both neutral snapshots, then continue the Judge-disabled and overflow scenarios.
- [x] Preserve HTML, CSS, dependencies, hover styling, public behavior, and the three already-neutral screenshot baselines; replace only the contaminated mobile light baseline with the verified neutral capture.
- [x] Do not use `blur()`, fixed waits, CSS polling, or pixel tolerance. Regenerate only the mobile project and accept no baseline diff beyond `evaluation-flow-mobile-linux.png`.
- [x] Leave canonical documentation unchanged because no page content, configuration, command, architecture, or evaluation semantics change.

#### Validation

- [x] Command: `npm test` passed all 37 tests.
- [x] Command: targeted `npm run test:e2e -- --grep "evaluation pages separate current evidence"` passed desktop and mobile without update mode.
- [x] Command: `npm run test:e2e` passed 32 tests with 2 expected skips.
- [x] Expected result: the targeted test and full suite pass in the same pinned Playwright container used by CI after the single authorized baseline correction.

#### Acceptance Criteria

- [x] Light and dark screenshots are captured before focus interaction and with no hovered stage link.
- [x] Keyboard focus and its visible outline remain covered independently after visual capture.
- [x] All four stepper snapshots and the complete public suite pass, with only the contaminated mobile light baseline changed.

### Milestone 5 - Align connector arrows with the stage nodes

#### Goal

Center every connector line and arrowhead on the same rendered axis as the numbered stage nodes in desktop and mobile layouts. Preserve the directional arrows because the correction is local and their meaning remains useful.

#### Changes

- Extend the existing Playwright journey in `website/e2e/site.spec.mjs` with a browser geometry assertion that compares each connector line and arrowhead axis with its stage node center in both projects.
- Update only the connector geometry in `website/.vitepress/theme/custom.css`; preserve markup, copy, colors, focus, hover, stage spacing, and result presentation.
- Regenerate and inspect the four stepper snapshots in the pinned container because the corrected connector pixels intentionally change in both themes and layouts.
- Leave canonical documentation unchanged because this repairs visual alignment without changing content, configuration, commands, architecture, or evaluation semantics.

#### Validation

- Command: `npm test` for every TDD cycle.
- RED checkpoint: targeted Playwright journey fails its rendered geometry assertion against the current connector positions.
- GREEN checkpoint: targeted Playwright journey passes geometry in desktop and mobile, then `npm run test:e2e` passes with the reviewed baselines.

#### Acceptance Criteria

- Desktop connector lines and arrowheads share the horizontal center axis of the numbered nodes.
- Mobile connector lines and arrowheads share the vertical center axis of the numbered nodes.
- Arrows remain visible and directional, with no new overflow or accessibility regression.

## Progress

- [x] Repository and nested instructions read; workflow profile confirmed.
- [x] Required memory worktree and exact origin validated.
- [x] Milestone 1 started.
- [x] Milestone 1 completed: generator contract was RED, then `npm test` passed all 37 tests.
- [x] Milestone 2 started.
- [x] Milestone 2 completed: four snapshots were inspected and the complete public checkpoint passed 32 tests with 2 expected skips.
- [x] Milestone 3 started.
- [x] Milestone 3 completed.
- [x] Milestone 4 started after GitHub Actions run `30754059367` failed reproducibly.
- [x] Milestone 4 completed.
- [x] Milestone 4 exception gate resolved by separating neutral snapshots from interaction assertions instead of attempting transition teardown.
- [x] `npm test` passed all 37 tests after phase separation.
- [x] Targeted pinned-container checkpoint recorded the resolved block: desktop passed, but the neutral mobile light capture differed from its existing baseline by 84 pixels around node 01 even with no focused stage and `a:hover` count zero.
- [x] User authorized replacing only the contaminated mobile light baseline after reviewing why the visual and interaction checks remain necessary.
- [x] Targeted pinned-container checkpoint passed in desktop and mobile without update mode after the authorized baseline replacement.
- [x] Complete public checkpoint passed 32 tests with 2 expected mobile skips.
- [x] Post-GREEN `refactor-design` review completed with No action.
- [x] Canonical documentation reconciled with no prose or configuration changes required.
- [x] Ordered final validation completed after correcting the single Prettier formatting finding and restarting the sequence from `npm test`.
- [x] Milestone 5 started after visual inspection confirmed a small axis mismatch caused by independent fixed offsets for nodes, lines, and arrowheads.
- [x] Milestone 5 RED confirmed in the pinned browser: desktop and mobile connector lines were each 0.5 px off the rendered node center, before the assertion reached the independently offset arrowhead.
- [x] First GREEN attempt exposed the repository-wide `border-box` sizing invariant: treating the declared 50 px node as content-box moved both connector axes 1 px past center. Corrected the calculation to the rendered 25 px radius for the second attempt.
- [x] The next targeted run proved the connector line green and exposed the same `border-box` assumption inside the new arrow measurement. Corrected the test to add borders only for content-box pseudo-elements; the second CSS attempt itself remained unchanged.
- [x] Targeted Playwright geometry passed in desktop and mobile; all four reviewed snapshots were regenerated in the pinned container and passed again without update mode.
- [x] Complete public checkpoint passed 32 tests with 2 expected mobile skips.
- [x] Milestone 5 post-GREEN `refactor-design` review completed with No action.
- [x] Milestone 5 canonical documentation reconciled with no prose or configuration change required.
- [x] Milestone 5 ordered final validation completed after the single Prettier formatting correction and a full restart from `npm test`.
- [x] Milestone 5 completed.

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

- Decision: Normalize pointer state in the E2E journey and preserve strict screenshot thresholds.
  Rationale: Hover is intentional public behavior, while cursor position is incidental test state. Moving the cursor to a verified point outside the component and asserting no stage is hovered makes the captured state explicit without weakening comparison.
  Date/Author: 2026-08-02 / Codex

- Decision: Capture neutral snapshots before keyboard interaction and test focus afterward.
  Rationale: Playwright already disables finite animations for screenshots and waits for consecutive stable captures. The failure is phase contamination, not insufficient CSS waiting: separating visual and interaction assertions removes the teardown dependency without manual waits, CSS polling, tolerance, or unnecessary baseline changes.
  Date/Author: 2026-08-02 / Codex

- Decision: Replace only `evaluation-flow-mobile-linux.png` with its neutral capture.
  Rationale: Expected-versus-actual inspection proves the existing file contains the 84-pixel residual focus transition around node 01. The user explicitly authorized correcting this one contaminated contract while keeping the other three baselines unchanged.
  Date/Author: 2026-08-02 / Codex

- Decision: Classify the Milestone 4 post-GREEN design review as No action.
  Rationale: The change only orders existing public assertions into neutral visual and keyboard interaction phases. Pointer normalization is local to the single journey, introduces no production responsibility or hidden mutable state, and extracting a helper would add indirection without removing a demonstrated risk.
  Date/Author: 2026-08-02 / Codex

- Decision: Keep the directional arrowheads and align them through rendered geometry.
  Rationale: Numbering already conveys order, but arrows reinforce direction and the defect is local rather than structurally difficult. Browser geometry protects the user-visible invariant without coupling the test to particular CSS declarations.
  Date/Author: 2026-08-02 / Codex

- Decision: Use the targeted Playwright journey as the RED observation point while still running `npm test` in every cycle.
  Rationale: The required Node suite does not render CSS pseudo-elements. A static source assertion would test implementation spelling instead of the visible alignment, while Playwright can compare the rendered node, connector, and arrow axes in both declared projects.
  Date/Author: 2026-08-02 / Codex

- Decision: Classify the Milestone 5 post-GREEN design review as No action.
  Rationale: The corrected offsets are local to one component and the rendered geometry assertion now protects their relationship. Introducing separate custom properties for node radius, connector half-width, and arrow half-width would add arithmetic indirection without removing the need for orientation-specific positioning or strengthening the observable invariant.
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

- Risk: Pointer state from earlier navigation can activate hover styling during a screenshot.
  Mitigation: After scrolling the target into view, choose a viewport corner outside its bounding box, move the pointer there, assert that no stage link matches `:hover`, and retain zero visual-difference tolerance.

- Risk: Focus interaction in the same journey can contaminate a later neutral snapshot even after attempted teardown.
  Mitigation: Capture both theme snapshots before pressing Tab or Shift+Tab. Verify focus and outline afterward without `blur()` or transition-settling logic.

- Risk: The existing mobile light baseline itself contains the 84-pixel residual focus transition around node 01.
  Mitigation: User authorized replacing only that file. Run update mode against the targeted mobile project, then verify the repository diff before accepting it and rerun without update mode.

- Risk: Correcting connector geometry changes a small number of pixels in all four visual contracts.
  Mitigation: Update snapshots only in the pinned Playwright container, inspect every image, and retain strict zero-tolerance comparisons afterward.

- Risk: Magic offsets can drift again when node or connector dimensions change.
  Mitigation: Assert the rendered axis relationship in desktop and mobile rather than duplicating CSS values in a static test.

## Validation Strategy

Use `npm test` for every RED and GREEN cycle. After generator and CSS behavior are complete, run `npm run test:e2e` as the public checkpoint. Inspect targeted screenshots in light and dark themes. After the post-GREEN design review and documentation reconciliation, run exactly `npm test`, `npm run prettier:check`, `npm run build`, and `npm run test:e2e` from `website/`.

Milestone 4 final validation on 2026-08-02 completed in the required order: `npm test` passed 37 tests; `npm run prettier:check` reported all matched files use Prettier style; `npm run build` validated 53 reports, generated 10 skills and 53 reports, and completed the VitePress build; `npm run test:e2e` passed 32 tests with 2 expected skips. The first ordered attempt stopped when Prettier requested one mechanical line-wrap change in `site.spec.mjs`; after applying that exact formatting, the complete sequence restarted from the first command and passed.

Milestone 5 final validation on 2026-08-02 completed in the same required order: `npm test` passed 37 tests; `npm run prettier:check` reported all matched files use Prettier style; `npm run build` validated 53 reports, generated 10 skills and 53 reports, and completed the VitePress build; `npm run test:e2e` passed 32 tests with 2 expected skips. The first ordered attempt stopped when Prettier requested one mechanical line-wrap change in the geometry assertion. After that exact formatting correction, the complete sequence restarted from `npm test` and passed.

## Documentation Impact

- `website/README.md`: inspected and left unchanged. It already states that active pages show a linked current definition flow, that semantic and deterministic stages vary by applicability, and that the flow uses static semantic HTML and CSS with visible focus, responsive stacking, and reduced motion. The new stepper changes presentation without invalidating any of those facts or changing commands or architecture.
- `website/content-config.json`: inspected and left unchanged. It contains only the site base path and disabled compatibility skills. The stage count is derived from existing case definitions and adds no public configuration key or schema.
- Root `README.md`: inspected and left unchanged because repository purpose, website entry points, and contribution workflow are unchanged.
- Root `CODEX_CLI.md`: inspected and left unchanged because skill discovery, CLI operation, and user workflows are unaffected.
- Root `EVALUATIONS.md`: inspected and left unchanged because Prompt, Fixture, Executor, Mechanical checks, Oracle, Judge, and result semantics are unchanged; only their generated website navigation presentation changed.
- `website/scripts/generate-content.mjs` and `website/.vitepress/theme/custom.css` are the canonical code sources for generated page markup and presentation. `website/.generated/` will remain an unedited projection.
- CI stabilization follow-up: all sources above remain unchanged because only the ordering and state isolation of existing Playwright assertions change.
- Arrow alignment follow-up: all documentation and configuration sources remain unchanged because connector position is a visual correction inside the already documented static responsive flow.

## Rollout and Recovery

The static site build is the rollout artifact. There is no data migration or runtime dependency. Recovery consists of reverting the generator, CSS, tests, and snapshots together, then regenerating the site through normal website commands. No generated projection should be hand restored.

## Lessons Learned

- The current definition and latest operation flows share `.evaluation-flow` even though they represent different concepts; preserving `.definition-flow` while separating their styling is the narrowest compatible boundary.
- The first public RED failed on the computed native list style (`decimal`), proving that the VitePress reset needed a dedicated high-specificity rule rather than relying on the old shared flow styles.
- A mobile dark-theme screenshot must close VitePress's navigation drawer after using its appearance switch; otherwise the drawer can cover a correctly rendered target and produce a misleading snapshot.
- A locator screenshot preserves page hover state. Navigation can leave the desktop pointer over a later target even after keyboard focus is blurred, so deterministic visual tests must normalize both focus and pointer state.
- Moving the pointer to `(0, 0)` activated the first mobile node after Playwright scrolled the locator into view. Moving it to the viewport's lower-right corner removed that hover source, but the first node still differed by 84 pixels because its focus transition had not settled. Both experimental edits were removed after the second failed GREEN attempt.
- After resuming, polling only `transform: none` still left the same 84-pixel mobile difference around node 01. The screenshot proves `box-shadow` can remain interpolated after transform reaches its neutral computed value. That experiment was also removed when the public checkpoint failed.
- Polling until ordinary nodes had equal computed `transform` and `box-shadow` values also left the same 84-pixel difference. Computed CSS equality does not guarantee that Chromium has discarded a rasterized transition frame before Playwright disables animations. Snapshot capture and focus verification should be ordered as independent assertions instead of coupled through teardown.
- Separating snapshots from keyboard interaction produced a neutral mobile light image, but it proved the existing `evaluation-flow-mobile-linux.png` baseline contains the residual node 01 focus transition. Expected versus actual inspection localizes all 84 differing pixels to the first node's larger halo and vertical shift. Desktop passed unchanged. Preserving that contaminated baseline and requiring a neutral capture are contradictory constraints.
- Correcting only the contaminated mobile light baseline made the targeted desktop and mobile journey pass without update mode, and the complete pinned-container suite stayed green. The other three stepper baselines remained byte-for-byte unchanged in the Git diff.
- The repository's universal `border-box` sizing means the declared 50 px node already includes its border, so its rendered axis is 25 px rather than 26 px. Browser geometry exposed both the original half-pixel line mismatch and an initially incorrect test calculation that counted pseudo-element borders twice.
- A 2 px connector centered on the 25 px node axis and an 8 px arrowhead positioned from the same axis render cleanly in all four inspected light, dark, desktop, and mobile snapshots.
