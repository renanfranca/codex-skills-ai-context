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

- [x] Add behavior tests in `website/tests/site-content.test.mjs` for schema parity, unknown operations and roles, structured runtime, sessions, failure categories, judge states, and absent telemetry.
- [x] Add one central website glossary source and validate its closed taxonomies against canonical schemas and archived reports.
- [x] Normalize report data by role without removing compatibility fields.
- [x] Render human operation labels across report, skill history, and evaluation history.
- [x] Add the operation/result/evidence distinction before skill history.

#### Validation

- [x] Command: `npm test`
- [x] Expected result: all website generator and hook behavior tests pass.
- [x] Command: `npm run test:e2e`
- [x] Expected result: the existing public browser checkpoint remains green.

#### Acceptance Criteria

- [x] Generated data contains the glossary and structured facts while preserving existing report fields.
- [x] Unknown closed taxonomy values fail generation with a precise error.
- [x] Generated pages consistently show human operation names and technical commands.

### Milestone 2 - Deliver responsive contextual learning

#### Goal

Make every execution fact and the complete report vocabulary learnable in place on desktop and mobile.

#### Changes

- [x] Add a reusable responsive help component for anchored popovers, mobile sheets, and the broad guide.
- [x] Render actionable full labels for every execution fact and add “Learn how to read this report” beside the section heading.
- [x] Organize the complete guide into production, operations, execution facts, and observations.
- [x] Add browser coverage for pointer, touch, keyboard, Escape, focus, themes, guide completeness, and overflow.
- [x] Add the requested desktop and Pixel 7 visual snapshots.

#### Validation

- [x] Command: `npm test`
- [x] Expected result: generator contracts remain green.
- [x] Command: `npm run test:e2e`
- [x] Expected result: public journeys and visual snapshots pass in desktop and Pixel 7 projects.

#### Acceptance Criteria

- [x] Desktop help is anchored and mobile help is a scrollable bottom sheet.
- [x] The complete guide includes taxonomy values absent from the current report and defines the evaluation runner.
- [x] Interactive controls are not nested inside links or dialogs.

### Milestone 3 - Review design, reconcile documentation, and validate

#### Goal

Confirm the green implementation has sound responsibilities, accurate canonical documentation, and clean final validation.

#### Changes

- [x] Run the `refactor-design` review after unit and public checkpoints are green.
- [x] Apply only demonstrated behavior-preserving structural improvements.
- [x] Update `website/README.md` for the glossary and generated data contract.
- [x] Inspect `website/content-config.json`, root `README.md`, `CODEX_CLI.md`, and `EVALUATIONS.md`; update only sources whose public contract changed and record concrete no-change reasons for the rest.

#### Validation

- [x] Command from `website/`: `npm test`
- [x] Command from `website/`: `npm run prettier:check`
- [x] Command from `website/`: `npm run build`
- [x] Command from `website/`: `npm run test:e2e`
- [x] Command from repository root: `git diff --check`
- [x] Command from memory worktree: `git diff --check`
- [x] Expected result: every command exits zero in the stated order.

#### Acceptance Criteria

- [x] Design review records its findings and any refactors.
- [x] Every canonical documentation source has an update or explicit no-change justification.
- [x] All final validation commands pass in order.

### Milestone 4 - Keep contextual popovers inside the viewport

#### Goal

Correct the desktop positioning defect discovered after the initial implementation. A contextual field popover must use its rendered dimensions to open above or below the triggering label, remain at least 16 pixels inside the viewport, expose internal scrolling when its content is taller than the available space, and reposition when the document or a scroll container moves.

#### Changes

- [x] Add one browser behavior test in `website/e2e/site.spec.mjs` that opens the tallest field taxonomy near the viewport bottom, verifies the complete panel bounds, scrolls the page, and verifies the bounds again.
- [x] Replace the assumed 360 pixel height in `website/.vitepress/theme/EvaluationHelp.vue` with positioning based on the rendered panel height and the available viewport.
- [x] Reuse the established promotion popover positioning behavior where its semantics match, without coupling the two dialogs or changing mobile sheets and the broad guide.
- [x] Inspect canonical documentation and visual snapshots; update them only if the public contract or approved appearance changes.

#### Validation

- [x] Command: `npm test`
- [x] Expected result: all generator and hook behavior tests remain green during the browser RED/GREEN cycle.
- [x] Command: `npm run test:e2e`
- [x] Expected result: every desktop and Pixel 7 journey passes, including the new geometry assertions.

#### Acceptance Criteria

- [x] The contextual popover has `top >= 16` and `bottom <= viewport height - 16` before and after page scroll.
- [x] Tall content scrolls inside the panel rather than extending below the visible viewport.
- [x] Escape, outside dismissal, focus restoration, mobile sheets, the broad guide, themes, and existing snapshots remain unchanged.

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
- [x] Expected result: every local command exits zero, then a user-pushed GitHub Actions run retains Playwright diagnostics if the visual checkpoint still fails.

#### Acceptance Criteria

- [x] A Playwright failure in GitHub Actions produces a downloadable artifact containing the failure images and trace.
- [x] No screenshot baseline, tolerance, font, component, or style changes before the retained diff is inspected.
- [x] The final visual correction follows the recorded classification and the public checkpoint passes in GitHub Actions.

### Milestone 6 - Restore code contrast in both themes

#### Goal

Make inline code and every Markdown code block readable without changing the established dark block appearance. The archived `execplan-tdd` promotion report `20260729T181620.575049Z-cc3d76bb204a` must expose at least a 4.5:1 computed contrast ratio for its operation, operation identifier, and an unhighlighted diff context or comment line in both light and dark themes.

#### Changes

- [x] Expand the public code inspection journey in `website/e2e/site.spec.mjs` to measure computed foreground and background colors in the named report, while retaining expansion and overflow assertions for diffs and fragments.
- [x] Update only `website/.vitepress/theme/custom.css`: give light theme inline code a dark foreground, preserve the current light foreground in dark theme, give plain code block text and language identifiers a light foreground, and make light theme Shiki spans use the tokens intended for dark backgrounds.
- [x] Apply the correction through global VitePress Markdown selectors so diffs, fragments, and other generated report blocks share the behavior without editing archived reports, generated projections, or components.
- [x] Inspect canonical documentation and existing visual snapshots; no update is required because the change repairs the existing readability contract, changes no configuration or contributor workflow, and the contrast journey replaces a new raster baseline.

#### Validation

- [x] Command from `website/`: `npm test`
- [x] Expected result: all generator and hook behavior tests remain green during the browser RED/GREEN cycle.
- [x] Command from `website/`: `npm run test:e2e`
- [x] Expected result: the contrast journey and all existing desktop and Pixel 7 journeys pass without new snapshots.
- [x] Final sequence from `website/`: `npm test`, `npm run prettier:check`, `npm run build`, `npm run test:e2e`
- [x] Command from repository root: `git diff --check`
- [x] Command from memory worktree: `git diff --check`

#### Acceptance Criteria

- [x] `validate-change`, the inline report identifier, and a diff context or comment line each have computed contrast of at least 4.5:1 in light and dark themes.
- [x] Code blocks retain their dark background in light mode and use dark-background Shiki tokens.
- [x] Diffs and code fragments remain expandable and do not create document overflow.
- [x] No archived report, generated projection, component, or screenshot baseline changes.

### Milestone 7 - Restore the complete model session definition

#### Goal

Restore the model session semantics lost in commit `52f6416c` and make the central glossary the actual source for the promotion panel, the `Executed sessions` contextual help, and the complete vocabulary guide. All three surfaces must explain the isolated ephemeral runner invocation, executor and optional judge responsibilities, excluded concepts, and zero-session deterministic checks.

#### Changes

- [x] Expand `website/tests/site-content.test.mjs` so serialized glossary behavior requires canonical executor, judge, and model session definitions without removing or renaming any existing property.
- [x] Restore detailed promotion panel expectations and extend the vocabulary browser journey in `website/e2e/site.spec.mjs`, including inline code markup, one guide entry, keyboard navigation, and overflow.
- [x] Extend `website/scripts/evaluation-glossary.mjs` with canonical structured descriptions while preserving public `description` strings.
- [x] Add one small shared structured-description renderer and use it from `website/.vitepress/theme/EvidenceStatus.vue` and `website/.vitepress/theme/EvaluationHelp.vue`.
- [x] Remove the duplicate `Executed sessions` guide entry while keeping the canonical model session entry under “How an evaluation is produced”.
- [x] Restore the complete explanation in `website/README.md`; leave `website/content-config.json`, root documentation, schemas, generated projections, and archived reports unchanged.
- [x] Update only the existing desktop and mobile vocabulary guide snapshots after confirming their diffs contain only the restored explanation.

#### Validation

- [x] Command from `website/` for every cycle: `npm test`
- [x] Focused public RED and GREEN: `npx playwright test e2e/site.spec.mjs --grep "a validated promotion explains qualification and recorded effort|report vocabulary is learnable in context and in the complete guide"`
- [x] Public checkpoint from `website/`: `npm run test:e2e`
- [x] Final sequence from `website/`: `npm test`, `npm run prettier:check`, `npm run build`, `npm run test:e2e`
- [x] Command from repository root: `git diff --check`
- [x] Command from memory worktree: `git diff --check`

#### Acceptance Criteria

- [x] The serialized glossary additively defines executor, judge, and model session, including `codex exec --json`, runner ownership, role responsibilities, excluded concepts, and zero sessions for deterministic checks.
- [x] The promotion panel, `Executed sessions` help, and complete guide render the same full canonical definition, with `codex exec --json` in a `<code>` element.
- [x] The guide contains exactly one model session entry, remains keyboard navigable, and creates no document overflow.
- [x] Only the two existing vocabulary guide snapshots change, and their visual differences contain only the restored explanation.
- [x] No generated projection, archived report, schema, public configuration, or unrelated component changes.

### Milestone 8 - Keep the public workflow inside the Playwright container contract

#### Goal

Restore the public GitHub Actions checkpoint after the visual environment was made hermetic. The workflow must use the Python already present in the digest-pinned Playwright container instead of installing a separate host toolchain that is incompatible with container execution.

#### Changes

- [x] Update `.github/workflows/deploy-website.yml` to replace `actions/setup-python@v6` with an explicit `python3 --version` verification step inside the container.
- [x] Update `website/tests/visual-environment.test.mjs` so deterministic tests reject reintroducing `actions/setup-python` and require the container Python verification.
- [x] Leave website user behavior, generated content, visual baselines, and archived reports unchanged.

#### Validation

- [x] Public GitHub Actions run `30573015827` for commit `e15bdb8b6f2e1b585d39a040a0c8edf76c7d9461` completed successfully on 2026-07-30.
- [x] The run passed `Verify container Python`, deterministic website tests, website build, public path checkpoints, and GitHub Pages deploy.

#### Acceptance Criteria

- [x] The workflow stays in the same pinned Playwright container for Python-dependent archive validation.
- [x] Public path checkpoints pass in GitHub Actions.
- [x] No local-only Python setup remains in the public website workflow.

### Milestone 9 - Restore detailed token telemetry

#### Goal

Restore detailed usage telemetry that was collapsed by the vocabulary implementation. The promotion panel and generated report pages must preserve recorded input, cached input, output, reasoning output, total token, event, and API reference estimate facts without inventing missing data.

#### Changes

- [x] Expand `website/tests/site-content.test.mjs` to require detailed aggregate telemetry, normalized usage events, API reference estimate status, missing legacy telemetry semantics, and compatibility fields.
- [x] Update `website/scripts/generate-content.mjs` to project archived usage decomposition, normalized usage events, and API reference estimate details into generated report data and Markdown.
- [x] Add `website/scripts/telemetry-format.mjs` and reuse it from generated content and `website/.vitepress/theme/EvidenceStatus.vue`.
- [x] Extend `website/scripts/evaluation-glossary.mjs` with cached input token, reasoning output token, normalized usage event, and API reference estimate definitions.
- [x] Update the promotion panel in `website/.vitepress/theme/EvidenceStatus.vue` to show the detailed effort fields.
- [x] Extend `website/e2e/site.spec.mjs` and update only the existing desktop execution-facts snapshot after confirming the visual difference is the restored telemetry content.
- [x] Update `website/README.md`; leave root documentation, schemas, generated projections, archived reports, and public configuration unchanged.

#### Validation

- [x] Local validation for commit `bb07451395d77ca8e3d592d194f7a4f8da5ff3a5` passed before publication: `npm test`, `npm run prettier:check`, `npm run build`, `npm run test:e2e`, repository `git diff --check`, and memory worktree `git diff --check`.
- [x] Public GitHub Actions run `30588390616` for commit `bb07451395d77ca8e3d592d194f7a4f8da5ff3a5` completed successfully on 2026-07-30.
- [x] The run passed deterministic website tests, website build, public path checkpoints, skipped failure artifact upload because Playwright passed, and deployed GitHub Pages.

#### Acceptance Criteria

- [x] Token totals show recorded workload while cached input remains a subset of input and reasoning output remains a subset of output.
- [x] Legacy or absent telemetry renders as `Not recorded` instead of inferred zeroes or reconstructed costs.
- [x] API reference estimates remain explicitly labeled as reference estimates, not observed charges or invoices.
- [x] Promotion panel and report pages share formatting behavior for estimate values and statuses.
- [x] The final public checkpoint and deploy are green in GitHub Actions.

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
- [x] Milestone 5 final correction completed and green in GitHub Actions in run `30556148297`.
- [x] Milestone 6 started after measuring approximately 1.18:1 contrast for light theme inline code and 2.47:1 for unhighlighted block lines.
- [x] Milestone 6 completed: computed contrast, expansion, overflow, both themes, and the complete local validation sequence are green.
- [x] Milestone 7 started after confirming commit `52f6416c` replaced the complete model session explanation with a shorter qualification-specific sentence.
- [x] Milestone 7 completed: the complete canonical model session definition now comes from the serialized glossary and is rendered consistently in all three public surfaces.
- [x] Milestone 8 started after the hermetic visual environment reached the public workflow but exposed that `actions/setup-python@v6` did not fit the containerized job.
- [x] Milestone 8 completed: the workflow now verifies the container Python and public run `30573015827` passed build, checkpoints, and deploy.
- [x] Milestone 9 started after the restored vocabulary still left detailed token usage and API reference estimate facts collapsed.
- [x] Milestone 9 completed: detailed token telemetry is restored across generated report data, report Markdown, promotion help, glossary definitions, README documentation, local validation, and public run `30588390616`.
- [x] ExecPlan finalized on 2026-07-31 after confirming `main` equals `origin/main`, the main worktree is clean, the memory worktree is clean before final edits, and the latest public workflow is green.

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

- Decision: Preserve dark code block surfaces in both themes and change only their foreground token selection.
  Rationale: The dark block surface is part of the approved visual system. Selecting Shiki dark-background tokens in light mode and defining explicit fallback text colors corrects readability globally without changing archived content or generated markup.
  Date/Author: 2026-07-30 / Codex

- Decision: Keep the post GREEN implementation unchanged after design review.
  Rationale: The review classified the CSS token override and the local computed-contrast test helper as `No action`. Each stays at its natural boundary, introduces no mutable state or duplicated production transformation, and extracting either would add topology without removing a demonstrated risk.
  Date/Author: 2026-07-30 / Codex

- Decision: Represent the model session explanation as a compatible plain `description` plus frozen typed segments, and reuse the same glossary entry for `modelSession` and `fields.sessions`.
  Rationale: Existing `data.json` consumers retain the public string and all prior properties, while Vue can render only the technical command as inline code without reconstructing prose or maintaining surface-specific copies.
  Date/Author: 2026-07-30 / Codex

- Decision: Consolidate the three public model session assertions in one browser-test behavior helper after GREEN.
  Rationale: The post GREEN design review classified their repetition as a maintainability opportunity because the same contract could drift between the promotion panel, contextual help, and guide. The helper changes no production topology or observable behavior.
  Date/Author: 2026-07-30 / Codex

- Decision: Use the Python executable bundled in the pinned Playwright container for the public workflow.
  Rationale: Installing a separate Python toolchain inside the container introduced an environment mismatch while archive validation only needs a working `python3` in the same runtime that runs the website build and Playwright checkpoint.
  Date/Author: 2026-07-30 / Codex

- Decision: Add a small shared telemetry formatter instead of formatting API reference estimates separately in generated Markdown and Vue.
  Rationale: Report pages and the interactive promotion panel must agree on recorded decimal display, unavailable statuses, and base-rate reference wording without duplicating business rules.
  Date/Author: 2026-07-30 / Codex

- Decision: Preserve normalized usage event detail rather than deriving a simplified cost view.
  Rationale: The website is an evidence viewer. It must report archived workload and reference estimates exactly enough to audit them, while avoiding false precision about billing or missing telemetry.
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

- Risk: Fixing only highlighted spans can leave plain diff context, blank lines, or language labels below the contrast target.
  Mitigation: Test the rendered fallback text and language label separately, set the block-level foreground explicitly, and apply dark Shiki tokens to all highlighted spans on the established dark surface.

- Risk: Copying the restored prose into three Vue surfaces can allow the definitions to drift again.
  Mitigation: Keep the compatible plain description and structured inline-code segments in the glossary, then render that same entry through one shared component.

- Risk: Listing `Executed sessions` both as production vocabulary and as an execution fact duplicates the model session definition in the complete guide.
  Mitigation: Keep the entry only in “How an evaluation is produced” while retaining contextual help on the report fact itself.

- Risk: A host-oriented setup action can drift away from the pinned browser container and make the GitHub Actions checkpoint fail for reasons local Docker validation cannot reproduce.
  Mitigation: Keep the workflow inside the pinned Playwright container and assert that `actions/setup-python` is absent from the workflow.

- Risk: Detailed telemetry can be misread as billing data or double-counted when cached input and reasoning output are displayed alongside totals.
  Mitigation: Document and test that cached input is a subset of input, reasoning output is a subset of output, and API reference estimates are not observed charges.

- Risk: Legacy reports without normalized usage events can appear to have zero usage if the generator fills missing values.
  Mitigation: Preserve missing telemetry as `Not recorded` and test absent event, aggregate, and estimate fields explicitly.

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

Milestone 6 validation on 2026-07-30:

1. The focused browser RED failed in both device profiles at 1.10:1 for the first light theme inline code assertion, confirming the intended contrast defect.
2. `npm test` passed all 24 tests after the CSS correction.
3. The focused contrast journey passed in desktop Chromium and Pixel 7 after rebuilding the site.
4. The public checkpoint passed 27 tests across desktop and Pixel 7; the desktop-only geometry journey remained intentionally skipped in the mobile project.
5. The post GREEN design review classified the CSS token override and local computed-contrast helper as `No action`; both remain cohesive at their existing presentation and browser-test boundaries.
6. Final `npm test` passed all 24 tests.
7. Final `npm run prettier:check` passed.
8. Final `npm run build` validated 53 reports, regenerated the disposable site projection, and completed successfully. The existing chunk size advisory remained nonblocking.
9. Final `npm run test:e2e` passed 27 tests across desktop and Pixel 7 with the same intentional mobile skip.
10. `git diff --check` passed in the main repository.
11. `git -C _temporary/codex-skills-ai-context diff --check` passed in the memory worktree.

Milestone 7 prefinal validation on 2026-07-30:

1. The first `npm test` RED failed because serialized `evaluationGlossary.executor` did not exist.
2. The focused public RED failed in desktop Chromium and Pixel 7 because both the promotion panel and `Executed sessions` help lacked the complete definition.
3. After implementation, `npm test` passed all 24 tests.
4. The focused public journey passed every semantic and inline-code assertion; only the two expected vocabulary guide snapshots failed.
5. Visual inspection confirmed the material snapshot differences were confined to the restored executor, judge, and model session explanations. Existing environment-specific glyph antialiasing remained within the previously approved rendering policy.
6. Only the existing desktop and mobile vocabulary guide snapshots were regenerated; no new snapshot was created.
7. The public checkpoint passed 27 tests across desktop Chromium and Pixel 7 with the desktop-only geometry journey intentionally skipped on mobile.
8. The post GREEN design review consolidated repeated public definition assertions and classified the production glossary entry, renderer, and consuming surfaces as `No action`.
9. After the test refactor, `npm test` again passed all 24 tests and `npm run test:e2e` again passed 27 tests with the same intentional mobile skip.

Milestone 7 final validation on 2026-07-30:

1. Final `npm test` passed all 24 tests.
2. Final `npm run prettier:check` passed.
3. Final `npm run build` validated 53 reports, regenerated the disposable site projection, and completed successfully. The existing chunk size advisory remained nonblocking.
4. Final `npm run test:e2e` passed 27 tests across desktop Chromium and Pixel 7; the desktop-only geometry journey remained intentionally skipped on mobile.
5. `git diff --check` passed in the main repository.
6. `git -C _temporary/codex-skills-ai-context diff --check` passed in the memory worktree.

Milestone 8 public validation on 2026-07-30:

1. GitHub Actions run `30573015827` executed commit `e15bdb8b6f2e1b585d39a040a0c8edf76c7d9461`.
2. The build job passed `Verify container Python`, dependency installation, deterministic website tests, website build, and public path checkpoints.
3. The Playwright diagnostics upload step was skipped because the public checkpoint passed.
4. The deploy job completed successfully.

Milestone 9 final validation on 2026-07-30:

1. Local final `npm test` passed all deterministic website tests.
2. Local final `npm run prettier:check` passed.
3. Local final `npm run build` validated archived reports, regenerated disposable projections, and completed successfully with the existing nonblocking chunk size advisory.
4. Local final `npm run test:e2e` passed the public desktop Chromium and Pixel 7 journeys.
5. `git diff --check` passed in the main repository.
6. `git -C _temporary/codex-skills-ai-context diff --check` passed in the memory worktree.
7. GitHub Actions run `30588390616` executed commit `bb07451395d77ca8e3d592d194f7a4f8da5ff3a5`.
8. The public run passed deterministic website tests, website build, public path checkpoints, skipped failure artifact upload because Playwright passed, uploaded the Pages artifact, and deployed successfully.

## Documentation Impact

`website/README.md` now documents the central glossary, independent concepts, structured generated data, precise missing and judge semantics, responsive learning surfaces, the complete canonical model session definition, detailed token telemetry, normalized usage events, API reference estimates, and the shared telemetry formatter. Milestone 4 requires no further README change because it repairs the existing promise that desktop help is an anchored popover and does not add configuration or user workflow. Milestone 5 changes only the applicable public workflow configuration by retaining failure diagnostics; it does not change the website's user behavior, contributor commands, generated data, or public contract, so no prose documentation changes are required. Milestone 6 repairs the existing readable report presentation and changes no generated data, contributor command, configuration, or user workflow, so it requires no README prose or screenshot baseline update; computed contrast is the canonical regression check. Milestone 8 changes the public workflow implementation but not contributor commands because `npm run test:e2e` already owns the pinned local container path; deterministic workflow tests cover the public contract. `website/content-config.json` remains accurate without changes because the base route and disabled skill selection did not change. Root `README.md` remains accurate because repository navigation and the website workflow entry point did not change. `CODEX_CLI.md` remains accurate because no CLI or runner operation changed and it already states that deterministic cases consume no model sessions. `EVALUATIONS.md` remains the detailed canonical contract and already defines the fresh ephemeral executor, separate judge, `codex exec --json` invocation, model sessions, token usage, API reference estimates, results, persistence, and zero-session deterministic behavior; the website translates that contract without changing it. `develop-skill-with-evals/references/eval-report.schema.json` and `eval-result.schema.json` remain unchanged canonical schemas consumed for parity validation. Milestones 7 through 9 leave generated projections, archived reports, schemas, root documentation, and `website/content-config.json` untouched.

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
- VitePress selected `--shiki-light` tokens from its theme name even though this site deliberately keeps code block surfaces dark in light mode. The surface decision and syntax token decision must stay aligned explicitly.
- Inline code and code block fallback text use separate VitePress variables. Correcting only `--vp-code-color` leaves unstyled fragment lines and language identifiers on their previous low-contrast fallbacks.
- A rootless Vue renderer can preserve surrounding paragraph and definition-list semantics while selectively emitting the glossary's structured code segment. Sharing the full entry object keeps plain serialization and rendered prose aligned.
- Opening an additional mobile contextual-help sheet changes the underlying page scroll position before the guide screenshot. Explicitly restoring the “Execution facts” heading position keeps the snapshot focused on guide content rather than an incidental journey scroll.
- The pinned Playwright container already contains the Python runtime needed by archive validation. Adding a host setup action inside the container adds a failure mode without improving reproducibility.
- The vocabulary implementation initially restored human explanations but underrepresented workload evidence. Token telemetry needs its own contract because cached input, reasoning output, event completeness, and API reference estimates have distinct audit meanings.
- API reference amounts can be small enough for JavaScript to serialize in scientific notation. Evidence pages should preserve recorded decimal readability when presenting money-like reference values.
