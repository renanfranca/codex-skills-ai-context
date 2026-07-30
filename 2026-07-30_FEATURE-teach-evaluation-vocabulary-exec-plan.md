# Teach evaluation vocabulary in context

This ExecPlan is a living document. Keep `Progress`, `Decisions`, `Risks and Mitigations`, and `Lessons Learned` current as work advances.

## Purpose / Big Picture

Readers of the generated evaluation website should be able to understand every reported value without leaving the report or losing the technical vocabulary needed to audit it. Each execution fact will expose contextual help, while a broad “Learn how to read this report” panel will teach the complete vocabulary, including values absent from the current execution. Operation type, recorded result, and evidence status will remain visibly independent concepts.

## Scope

In scope are the website content generator, generated report data contracts, VitePress Vue components and styles, website unit and browser tests, visual snapshots, and documentation reconciliation. The glossary covers the report fields exposed by the website and the canonical closed taxonomies behind them.

Out of scope are changes to `run_skill_evals.py`, evaluation schemas, archive contents, dependencies, model sessions, commits, pushes, and publication. The public interface remains English. Generated files under `website/.generated/` and build artifacts are disposable projections and will not be edited directly.

## Definitions

An evaluation runner is the program `develop-skill-with-evals/scripts/run_skill_evals.py`; it coordinates gates, isolated workspaces, the executor, the judge, deterministic checks, and report archiving. An executor is the isolated model invocation that performs the evaluated task. A judge is an optional isolated model invocation that evaluates the executor result. A session is one isolated executor or judge invocation recorded by qualification. Evidence status describes evidence strength and currency. Operation type describes how evidence was produced. Recorded result describes what happened in that operation.

The central `evaluationGlossary` is the website-owned learning model containing human labels, concise descriptions, applicability, and canonical taxonomy entries for evidence states, operations, results, case kinds, observation roles, judge states and verdicts, failure categories, runtime, sessions, tokens, duration, and timestamps.

## Existing Context

`website/scripts/generate-content.mjs` reads skills and canonical archived reports, derives six evidence states, writes `data.json`, and renders Markdown pages containing HTML and Vue component invocations. Operation types currently appear as raw commands, execution facts are static definition lists, runtime is not consistently split by role, and missing versus explicit null failure categories are not distinguished.

`website/.vitepress/theme/EvidenceStatus.vue` provides the existing responsive evidence popover and promotion details. `website/.vitepress/theme/index.mjs` registers custom components, and `website/.vitepress/theme/custom.css` owns the complete visual system. `website/tests/site-content.test.mjs` observes generated data and Markdown through temporary repositories. `website/e2e/site.spec.mjs` exercises built public pages on desktop and a Pixel 7 project.

The canonical taxonomies are in `develop-skill-with-evals/references/eval-report.schema.json` and `eval-result.schema.json`; runner roles are represented by archived report observations and runner behavior. `EVALUATIONS.md` and `develop-skill-with-evals/references/eval-contract.md` provide the detailed evaluation contract.

## Desired End State

A report displays human operation names before technical commands, structured executor and judge runtime, executed sessions by role with the planned maximum available in help, formatted aggregate token telemetry, precise failure category semantics, and truthful judge states. Clicking or focusing a complete fact label opens an anchored desktop popover or scrollable mobile sheet. The broad guide presents the entire glossary in four groups and defines the evaluation runner. Both surfaces support keyboard operation, Escape, outside click, focus restoration, light and dark themes, and narrow screens without overflow.

Skill history and evaluation index rows keep their whole-row links while using the same human operation names. Promotion evidence uses a short qualification-focused session explanation. Automated tests fail when closed schema taxonomies or archived roles lack glossary definitions.

## Milestones

### Milestone 1 - Establish the glossary and structured report model

#### Goal

Make the canonical vocabulary and report semantics observable through generated `data.json` and Markdown.

#### Changes

- [ ] Add behavior tests in `website/tests/site-content.test.mjs` for schema parity, unknown operations and roles, structured runtime, sessions, failure categories, judge states, and absent telemetry.
- [ ] Add one central website glossary source and validate its closed taxonomies against canonical schemas and archived reports.
- [ ] Normalize report data by role without removing compatibility fields.
- [ ] Render human operation labels across report, skill history, and evaluation history.
- [ ] Add the operation/result/evidence distinction before skill history.

#### Validation

- [ ] Command: `npm test`
- [ ] Expected result: all website generator and hook behavior tests pass.
- [ ] Command: `npm run test:e2e`
- [ ] Expected result: the existing public browser checkpoint remains green.

#### Acceptance Criteria

- [ ] Generated data contains the glossary and structured facts while preserving existing report fields.
- [ ] Unknown closed taxonomy values fail generation with a precise error.
- [ ] Generated pages consistently show human operation names and technical commands.

### Milestone 2 - Deliver responsive contextual learning

#### Goal

Make every execution fact and the complete report vocabulary learnable in place on desktop and mobile.

#### Changes

- [ ] Add a reusable responsive help component for anchored popovers, mobile sheets, and the broad guide.
- [ ] Render actionable full labels for every execution fact and add “Learn how to read this report” beside the section heading.
- [ ] Organize the complete guide into production, operations, execution facts, and observations.
- [ ] Add browser coverage for pointer, touch, keyboard, Escape, focus, themes, guide completeness, and overflow.
- [ ] Add the requested desktop and Pixel 7 visual snapshots.

#### Validation

- [ ] Command: `npm test`
- [ ] Expected result: generator contracts remain green.
- [ ] Command: `npm run test:e2e`
- [ ] Expected result: public journeys and visual snapshots pass in desktop and Pixel 7 projects.

#### Acceptance Criteria

- [ ] Desktop help is anchored and mobile help is a scrollable bottom sheet.
- [ ] The complete guide includes taxonomy values absent from the current report and defines the evaluation runner.
- [ ] Interactive controls are not nested inside links or dialogs.

### Milestone 3 - Review design, reconcile documentation, and validate

#### Goal

Confirm the green implementation has sound responsibilities, accurate canonical documentation, and clean final validation.

#### Changes

- [ ] Run the `refactor-design` review after unit and public checkpoints are green.
- [ ] Apply only demonstrated behavior-preserving structural improvements.
- [ ] Update `website/README.md` for the glossary and generated data contract.
- [ ] Inspect `website/content-config.json`, root `README.md`, `CODEX_CLI.md`, and `EVALUATIONS.md`; update only sources whose public contract changed and record concrete no-change reasons for the rest.

#### Validation

- [ ] Command from `website/`: `npm test`
- [ ] Command from `website/`: `npm run prettier:check`
- [ ] Command from `website/`: `npm run build`
- [ ] Command from `website/`: `npm run test:e2e`
- [x] Command from repository root: `git diff --check`
- [x] Command from memory worktree: `git diff --check`
- [ ] Expected result: every command exits zero in the stated order.

#### Acceptance Criteria

- [ ] Design review records its findings and any refactors.
- [ ] Every canonical documentation source has an update or explicit no-change justification.
- [ ] All final validation commands pass in order.

### Milestone 4 - Keep contextual popovers inside the viewport

#### Goal

Correct the desktop positioning defect discovered after the initial implementation. A contextual field popover must use its rendered dimensions to open above or below the triggering label, remain at least 16 pixels inside the viewport, expose internal scrolling when its content is taller than the available space, and reposition when the document or a scroll container moves.

#### Changes

- [ ] Add one browser behavior test in `website/e2e/site.spec.mjs` that opens the tallest field taxonomy near the viewport bottom, verifies the complete panel bounds, scrolls the page, and verifies the bounds again.
- [ ] Replace the assumed 360 pixel height in `website/.vitepress/theme/EvaluationHelp.vue` with positioning based on the rendered panel height and the available viewport.
- [ ] Reuse the established promotion popover positioning behavior where its semantics match, without coupling the two dialogs or changing mobile sheets and the broad guide.
- [ ] Inspect canonical documentation and visual snapshots; update them only if the public contract or approved appearance changes.

#### Validation

- [ ] Command: `npm test`
- [ ] Expected result: all generator and hook behavior tests remain green during the browser RED/GREEN cycle.
- [ ] Command: `npm run test:e2e`
- [ ] Expected result: every desktop and Pixel 7 journey passes, including the new geometry assertions.

#### Acceptance Criteria

- [ ] The contextual popover has `top >= 16` and `bottom <= viewport height - 16` before and after page scroll.
- [ ] Tall content scrolls inside the panel rather than extending below the visible viewport.
- [ ] Escape, outside dismissal, focus restoration, mobile sheets, the broad guide, themes, and existing snapshots remain unchanged.

### Milestone 5 - Preserve and diagnose CI visual differences

#### Goal

Make environment-specific Playwright failures observable before changing screenshot policy or production styling. The GitHub Actions checkpoint must retain the expected, actual, and diff images plus traces whenever Playwright fails, so the final correction can distinguish harmless glyph rasterization from a real layout defect.

#### Changes

- [x] Record GitHub Actions run `30550339855` as the public RED: all semantic and viewport assertions passed, while the desktop execution facts snapshot differed by 60 of 921,600 pixels and the mobile vocabulary guide differed by 3,196 of 345,668 pixels.
- [x] Give the public path checkpoint a stable step identifier in `.github/workflows/deploy-website.yml`.
- [x] Add a failure-only `actions/upload-artifact@v6` step that retains `website/test-results` for seven days under a run- and attempt-specific artifact name.
- [x] After the user pushes the instrumentation, download the artifact through the GitHub connector and classify the diff before changing snapshots, fonts, components, or styles.
- [x] Apply at most a one percent per-snapshot pixel budget only when differences are confined to glyph rasterization and element geometry is identical. Preserve exact comparison for the evidence mark snapshots.
- [x] Confirm the artifact shows neither missing fonts nor layout changes, so no additional behavior assertion or production correction is required.
- [x] Record GitHub Actions run `30554147675` as a subsequent public RED: after the first desktop snapshot passed, the previously unreached desktop guide snapshot exposed 99 stable antialiasing pixels with unchanged geometry.
- [x] Apply the one percent budget to both page screenshot expectations independent of device profile, while preserving exact evidence mark snapshots.

#### Validation

- [x] Command from `website/`: `npm test`
- [x] Command from `website/`: `npm run prettier:check`
- [x] Command from `website/`: `npm run build`
- [x] Command from `website/`: `npm run test:e2e`
- [x] Command from repository root: `git diff --check`
- [x] Command from memory worktree: `git diff --check`
- [ ] Expected result: every local command exits zero, then a user-pushed GitHub Actions run retains Playwright diagnostics if the visual checkpoint still fails.

#### Acceptance Criteria

- [x] A Playwright failure in GitHub Actions produces a downloadable artifact containing the failure images and trace.
- [x] No screenshot baseline, tolerance, font, component, or style changes before the retained diff is inspected.
- [ ] The final visual correction follows the recorded classification and the public checkpoint passes in GitHub Actions.

## Progress

- [x] Repository and nested instructions read.
- [x] Required memory worktree and exact origin validated.
- [x] Workflow profile confirmed complete.
- [x] Milestone 1 started.
- [x] Milestone 1 completed: `npm test` passed 24 tests and the first public checkpoint passed 26 browser tests.
- [x] Milestone 2 started.
- [x] Milestone 2 completed: responsive contextual help, complete guide, and four desktop/mobile snapshots are green.
- [x] Milestone 3 started.
- [x] Milestone 3 completed: design review, documentation reconciliation, and every final validation command passed.
- [x] Milestone 4 started after reproducing a desktop popover extending 328 pixels below a 720 pixel viewport.
- [x] Milestone 4 completed: the new geometry journey is green and the complete public checkpoint passes 27 tests with one intentional mobile skip.
- [x] Milestone 5 started after GitHub Actions run `30550339855` exposed stable visual differences without retaining its Playwright artifacts.
- [x] Milestone 5 instrumentation completed and locally validated.
- [x] Milestone 5 artifact inspected and final correction classified as environment-specific glyph and icon rasterization with unchanged geometry.
- [x] Milestone 5 final correction completed and locally validated.
- [x] Milestone 5 device-specific policy disproved by run `30554147675`; the failure surfaced sequentially only after the preceding desktop expectation passed.
- [x] Milestone 5 corrected page-snapshot policy completed and locally validated.
- [ ] Milestone 5 final correction completed and green in GitHub Actions.

## Decisions

- Decision: Use one website-owned glossary that is imported by the generator and serialized into generated page data.
  Rationale: Contextual help, the complete guide, operation labels, and evidence states must not drift, while the evaluation schemas remain runner-owned canonical contracts.
  Date/Author: 2026-07-30 / Codex

- Decision: Preserve current generated report properties and add structured role-specific properties.
  Rationale: Consumers of `data.json` should not break merely because the UI becomes more precise.
  Date/Author: 2026-07-30 / Codex

- Decision: Keep history rows as single anchors and place operation help in section prose.
  Rationale: This preserves a large navigation target and avoids invalid nested interactive elements.
  Date/Author: 2026-07-30 / Codex

- Decision: Derive judge applicability from the declared judge runtime or archived observation state, while preserving missing telemetry as unrecorded.
  Rationale: A runtime inherited from the executor does not mean the judge was applicable, and a missing execution flag must not be inferred from a verdict.
  Date/Author: 2026-07-30 / Codex

- Decision: Centralize archived session normalization after the post GREEN design review.
  Rationale: `sessionsByRole` and promotion effort project the same canonical counts and must not evolve through duplicated transformations.
  Date/Author: 2026-07-30 / Codex

- Decision: Position desktop contextual help in two layout phases.
  Rationale: The final width changes line wrapping and therefore panel height. Applying width first, awaiting Vue layout, and only then calculating top uses the rendered dimensions instead of an estimate.
  Date/Author: 2026-07-30 / Codex

- Decision: Preserve the prior top during scroll driven remeasurement.
  Rationale: Removing top during the first layout phase can cause a visible one frame jump even when the final position is correct.
  Date/Author: 2026-07-30 / Codex

- Decision: Retain Playwright failure artifacts before changing visual comparison policy.
  Rationale: The failed workflow did not upload its expected, actual, and diff PNGs. Stable pixel counts alone cannot prove whether the difference is harmless font rasterization or an observable layout defect.
  Date/Author: 2026-07-30 / Codex

- Decision: Keep commit and push outside agent execution for the CI diagnosis.
  Rationale: The user chose to review and publish the local instrumentation, preserving the existing no-commit and no-push boundary.
  Date/Author: 2026-07-30 / Codex

- Decision: Allow a one percent pixel budget for both page screenshot expectations in every device profile, while preserving exact evidence mark snapshots.
  Rationale: Artifact `playwright-test-results-30553266562-1` first showed changed glyph and icon edges in the desktop execution facts page and mobile guide. Run `30554147675` then reached the desktop guide after the preceding expectation passed and exposed another 99 stable antialiasing pixels. The source of nondeterminism is the page-wide rasterization environment, not a particular device profile. Panel bounds, dividers, positions, wrapping, solid fills, and semantic geometry assertions remain unchanged.
  Date/Author: 2026-07-30 / Codex

## Risks and Mitigations

- Risk: Canonical reports vary across archive generations and may omit newer telemetry.
  Mitigation: Preserve absence as `Not recorded`, distinguish it from explicit null, and avoid inferred zeroes.

- Risk: A help control inside generated Markdown could conflict with VitePress hydration or nested links.
  Mitigation: Register stable Vue components and render them only in non-link fact and heading contexts.

- Risk: Glossary parity tests could accidentally validate against a duplicated list rather than schemas.
  Mitigation: Load the canonical JSON schemas in tests and generator startup, and compare exact closed sets.

- Risk: Broad guide content can overflow mobile viewports.
  Mitigation: Use a bounded bottom sheet with internal scrolling, wrapping technical values, and Pixel 7 assertions plus snapshots.

- Risk: A fixed popover can remain inaccessible when its top position and maximum height are calculated independently.
  Mitigation: Measure the rendered panel, derive placement and available height from one viewport calculation, and repeat it on scroll and resize.

- Risk: A blanket visual tolerance could hide a real regression in panel geometry.
  Mitigation: Inspect retained failure images and traces first. Permit a one percent budget only on the two page snapshots and only when the changed pixels follow glyph edges while element bounds remain identical.

- Risk: A failed step elsewhere in the build could trigger an irrelevant or empty Playwright artifact upload.
  Mitigation: Condition the upload on the named public path checkpoint outcome and warn rather than obscure the original failure when no files are present.

## Validation Strategy

Each behavior begins with one failing public generator or browser test. Every TDD cycle runs `npm test`; the public `npm run test:e2e` checkpoint runs at least every two cycles and at each milestone boundary. After all behavior and the public checkpoint are green, the design review runs, documentation is reconciled, then the exact final validation sequence runs once from a clean green state.

Final validation evidence on 2026-07-30:

1. `npm test` passed all 24 tests.
2. `npm run prettier:check` reported that every matched file uses Prettier style.
3. `npm run build` validated 53 archived reports, generated 10 skills and 53 report pages, and completed the VitePress build. The existing nonblocking chunk size advisory remained.
4. `npm run test:e2e` passed all 26 tests across desktop Chromium and Pixel 7.
5. `git diff --check` passed in the main repository.
6. `git -C _temporary/codex-skills-ai-context diff --check` passed in the memory worktree.

Milestone 4 repeated the complete final sequence on 2026-07-30:

1. `npm test` passed all 24 tests.
2. `npm run prettier:check` passed.
3. `npm run build` validated 53 reports, regenerated the disposable site projection, and completed successfully. The existing chunk size advisory remained nonblocking.
4. `npm run test:e2e` passed 27 tests across desktop and Pixel 7; the desktop-only geometry journey was intentionally skipped in the mobile project.
5. `git diff --check` passed in the main repository.
6. `git -C _temporary/codex-skills-ai-context diff --check` passed in the memory worktree.

Milestone 5 instrumentation validation on 2026-07-30:

1. `npm test` passed all 24 tests.
2. `npm run prettier:check` passed.
3. `npm run build` validated 53 reports, regenerated the disposable site projection, and completed successfully. The existing chunk size advisory remained nonblocking.
4. `npm run test:e2e` passed 27 tests across desktop and Pixel 7; the desktop-only geometry journey was intentionally skipped in the mobile project.
5. The post GREEN design review classified the isolated failure-only upload step as `No action`: its checkpoint outcome, artifact path, retention, and warning policy are explicit at the workflow boundary, with no new shared state or production responsibility.
6. `git diff --check` passed in the main repository.
7. `git -C _temporary/codex-skills-ai-context diff --check` passed in the memory worktree.

Milestone 5 final correction validation on 2026-07-30:

1. The first `npm run prettier:check` identified only formatting in the changed Playwright expectation. Prettier formatted that file, and the complete validation sequence restarted from the beginning.
2. `npm test` passed all 24 tests.
3. `npm run prettier:check` passed.
4. `npm run build` validated 53 reports, regenerated the disposable site projection, and completed successfully. The existing chunk size advisory remained nonblocking.
5. `npm run test:e2e` passed 27 tests across desktop and Pixel 7; the desktop-only geometry journey was intentionally skipped in the mobile project.
6. The post GREEN design review classified the device-specific screenshot options as `No action`: they remain local to the two affected page snapshots, preserve exact comparison in the opposite profiles and for evidence mark snapshots, and introduce no production responsibility or shared state.
7. `git diff --check` passed in the main repository.
8. `git -C _temporary/codex-skills-ai-context diff --check` passed in the memory worktree.

Milestone 5 corrected page-snapshot policy validation on 2026-07-30:

1. `npm test` passed all 24 tests.
2. `npm run prettier:check` passed.
3. `npm run build` validated 53 reports, regenerated the disposable site projection, and completed successfully. The existing chunk size advisory remained nonblocking.
4. `npm run test:e2e` passed 27 tests across desktop and Pixel 7; the desktop-only geometry journey was intentionally skipped in the mobile project.
5. The post GREEN design review classified the two unconditional page-snapshot budgets as `No action`: the policy follows the page-wide rendering boundary, remains explicit at each expectation, and leaves evidence mark comparisons exact.
6. `git diff --check` passed in the main repository.
7. `git -C _temporary/codex-skills-ai-context diff --check` passed in the memory worktree.

## Documentation Impact

`website/README.md` now documents the central glossary, independent concepts, structured generated data, precise missing and judge semantics, and responsive learning surfaces. Milestone 4 requires no further README change because it repairs the existing promise that desktop help is an anchored popover and does not add configuration or user workflow. Milestone 5 changes only the applicable public workflow configuration by retaining failure diagnostics; it does not change the website's user behavior, contributor commands, generated data, or public contract, so no prose documentation changes are required. `website/content-config.json` remains accurate without changes because the base route and disabled skill selection did not change. Root `README.md` remains accurate because repository navigation and the website workflow entry point did not change. `CODEX_CLI.md` remains accurate because no CLI or runner operation changed. `EVALUATIONS.md` remains the detailed canonical contract and already defines executor, judge, sessions, results, persistence, and token semantics; the website translates that contract without changing it. `develop-skill-with-evals/references/eval-report.schema.json` and `eval-result.schema.json` remain unchanged canonical schemas consumed for parity validation.

## Rollout and Recovery

The site is statically generated. Rollout consists only of later publishing by an authorized user, outside this plan. Recovery is a normal source revert followed by regeneration; archived evaluation evidence and runner behavior are untouched.

## Lessons Learned

- The existing website already has a responsive evidence popover whose focus and mobile behavior can inform the reusable help surface, but its promotion copy currently mixes qualification facts with a partial vocabulary lesson.
- Canonical judge runtime can contain inherited values even when judging is not required, so the public `Not used` state must depend on applicability rather than the mere presence of a runtime object.
- The post GREEN design review classified duplicated session normalization as a design risk and centralized it. Other possible extractions in the generator were local transformations with no demonstrated independent risk.
- A 1280 by 720 browser reproduction placed the failure category popover at top 360 and bottom 1048. Its fixed position did not change after page scroll because contextual help used an assumed 360 pixel panel height and registered no scroll repositioning.
- The first measured positioning correction still overflowed because the panel was measured before its final 400 pixel width changed line wrapping. A two phase layout and border box maximum height are both required to keep the outer panel within the viewport.
- The corrected desktop snapshot was inspected before replacement. It shows the executor model help fully visible above the lower fact rows; mobile snapshots and the broad guide did not change.
- GitHub Actions run `30550339855` used Ubuntu 24.04 while the committed Linux snapshots were produced in an Ubuntu 22.04 environment. Its final desktop difference was 0.0065 percent and its mobile difference was 0.9246 percent, but the workflow retained no images, so the cause remains deliberately unclassified.
- GitHub Actions run `30553266562` retained artifact `playwright-test-results-30553266562-1`. Inspection of the expected, actual, and diff PNGs confirmed identical layout geometry: the mobile diff follows glyph antialiasing edges and the desktop diff is limited to 60 pixels around contextual help icons.
- GitHub Actions run `30554147675` retained artifact `playwright-test-results-30554147675-1`. Its desktop guide expected, actual, and diff PNGs show 99 stable changed pixels along glyph edges with identical layout. The earlier run could not reveal this snapshot because the same test stopped at its preceding failed expectation.
- A sequence of screenshot expectations can hide later environment differences because Playwright stops the test at the first failed expectation. Visual stability policy should follow the rendering boundary being compared rather than only the device and snapshot combinations observed in the first failing run.
