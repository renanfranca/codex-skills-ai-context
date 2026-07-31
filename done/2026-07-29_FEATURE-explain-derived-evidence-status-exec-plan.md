# Explain derived current evidence status

This ExecPlan is a living document. Keep `Progress`, `Decisions`, `Risks and Mitigations`, and `Lessons Learned` current as work advances.

## Purpose / Big Picture

The public skill catalog currently assigns a few evidence labels manually and describes other skills only by whether archived reports exist. Readers cannot tell whether archived results apply to the skill source they are viewing, how much of the current suite passed, or whether a promotion contract was satisfied. This change derives one of six clearly explained evidence states from canonical source fingerprints, archived reports, and the current suite, then exposes the same state in catalog cards, an accessible evidence explainer, a shared legend, and individual skill pages.

The result is observable by building or serving `website/`, opening `/codex-skills/skills/`, and inspecting each card. State colors reinforce recognition in light and dark themes, while names, descriptions, counts, and accessible controls remain sufficient without color.

## Scope

In scope:

- Reproduce in JavaScript the evaluation runner's canonical skill source fingerprint.
- Distinguish current candidate or directly evaluated source evidence from baseline evidence and historical reports.
- Derive promotion, complete coverage, partial coverage, no current pass, historical, and no evaluation states in a documented priority order.
- Preserve every current result status in the generated model and evidence UI.
- Replace nested interactive catalog cards with semantic articles and separate navigation and evidence controls.
- Add a Vue evidence explainer that behaves as a desktop popover and a mobile bottom sheet.
- Add a shared six-state legend and use the same state definition on skill pages.
- Add behavior tests, browser journeys, theme styling, and user-facing documentation.

Out of scope:

- Changing evaluation runner contracts or archived reports.
- Editing `website/.generated/` directly.
- Adding dependencies, running model sessions, publishing, committing, or pushing.

## Definitions

- **Canonical fingerprint**: the deterministic SHA-256 identity of the files that constitute a skill source, using the same inclusion, ordering, path, and byte framing contract as the Python evaluation runner.
- **Current report**: an archived report whose evaluated source fingerprint exactly matches the fingerprint calculated from the current skill directory. For an operation with a baseline, only `sources.candidate` can establish current evidence. For a direct run, `sources.evaluated` establishes it.
- **Baseline observation**: an observation whose role is `baseline`; it never establishes evidence about the current skill even if another fingerprint happens to match.
- **Current suite**: case IDs declared by the skill's evaluation suite. Complete coverage requires at least one non-baseline current `PASS` for every declared case.
- **Promotion evidence**: a current `PASS` promotion operation that is promotion eligible.
- **Historical report**: an archived report that does not match the current source or lacks a comparable source fingerprint.
- **Public checkpoint**: `npm run test:e2e` from `website/`, which builds the projection and exercises it in desktop Chromium and an emulated Pixel 7.

## Existing Context

`website/scripts/generate-content.mjs` reads top-level `SKILL.md` files and `evaluation-reports/manifest.json`, normalizes each report, and writes disposable Markdown and JSON beneath the requested output directory. It currently uses an `evidenceLabels` map with manually assigned labels and otherwise reports only current archive counts. The same script renders the skills catalog, individual skill pages, evaluation history, and report pages.

`website/tests/site-content.test.mjs` observes the generator through its command line and generated output. `website/e2e/site.spec.mjs` observes the built public site through browser journeys. Theme registration and CSS live beneath `website/.vitepress/`. The canonical public documentation is `website/README.md`; applicable public configuration is `website/content-config.json`; root documentation is `README.md`, `CODEX_CLI.md`, and `EVALUATIONS.md`.

The evaluation runner implementation under `develop-skill-with-evals/scripts/` is the canonical fingerprint contract. Skill suites and archived `report.json` files are canonical inputs and must remain unchanged.

## Desired End State

Each generated skill has a structured evidence object containing the state key, label, description, visual variant and priority; current fingerprint; matching promotion report; current reports and counts grouped by original result; covered and declared case IDs; coverage counts; and historical report count.

Classification follows this order:

1. `Promotion evidence`
2. `Complete current coverage`
3. `Partial current coverage`
4. `No current pass`
5. `Historical runs`
6. `No evaluation yet`

A promotion takes precedence without hiding conflicting current outcomes. Complete coverage is the union of non-baseline current `PASS` observations across reports and does not imply promotion, stability, or repeatability. Reports without comparable fingerprints remain historical.

The catalog uses semantic articles with independent primary navigation, evidence button, and history link. One shared state definition drives the cards, legend, explainer, skill page, and styling variants. Desktop users receive a popover; mobile users receive a bottom sheet with backdrop. Both support pointer, Enter, Space, outside activation, close button, Escape, focus restoration, and reduced motion.

## Milestones

### Milestone 1 - Derive current evidence truthfully

#### Goal

Generate the six evidence states from canonical source and archive data through the generator's public command line.

#### Changes

- Add behavior fixtures and assertions in `website/tests/site-content.test.mjs` for all state transitions, priority, mixed results, suite coverage, baseline exclusion, missing fingerprints, and runner fingerprint parity.
- Update `website/scripts/generate-content.mjs` to calculate the canonical fingerprint, read suite declarations, normalize source fingerprints, aggregate current evidence, and expose a single shared state definition.
- Keep generated output disposable and make no direct changes beneath `website/.generated/`.
- No documentation change is completed in this milestone because the model contract must be green before public wording is finalized.

#### Validation

- Command: `npm test`
- Expected result: all generator and pre-commit behavior tests pass.
- Command: `npm run test:e2e`
- Expected result: the existing public journeys remain green as the model evolves.

#### Acceptance Criteria

- Every requested classification scenario is proven through generated public data.
- Fingerprint parity is proven against the runner contract.
- The real catalog yields promotion for `execplan-tdd`, `implement-execplan`, and `restructure-documentation`; partial coverage for `refactor-design` with 3 of 12 cases; historical runs for `develop-skill-with-evals`; and no evaluation for skills without reports.

### Milestone 2 - Explain status accessibly in the public UI

#### Goal

Expose the derived state consistently in cards, legend, evidence overlays, and skill pages without relying on color.

#### Changes

- Update generated catalog and skill Markdown in `website/scripts/generate-content.mjs` to use semantic articles, independent controls, shared state text, coverage and report details.
- Add and register a Vue component under `website/.vitepress/` for popover and bottom sheet behavior.
- Update theme CSS under `website/.vitepress/` with light and dark state tokens, indicator, border, surface, backdrop, responsive layout, focus visibility, and reduced motion.
- Extend `website/e2e/site.spec.mjs` with desktop and Pixel 7 journeys for legend, all indicators, mouse, touch, keyboard, dismissal, focus restoration, independent navigation, themes, overflow, and text-only comprehension.
- Documentation remains pending until the implemented public terms pass the browser checkpoint.

#### Validation

- Command: `npm test`
- Expected result: the full relevant suite passes.
- Command: `npm run test:e2e`
- Expected result: all desktop and mobile public journeys pass.

#### Acceptance Criteria

- The six states appear in the shared legend with consistent non-color text and indicators.
- The explainer behaves as a popover on desktop and a bottom sheet on mobile.
- Cards contain no nested interactive elements and their three actions work independently.
- Both themes and supported viewport profiles have no horizontal overflow.

### Milestone 3 - Review design and reconcile documentation

#### Goal

Review the green implementation for structural risks, apply only behavior-preserving improvements, and document the public evidence semantics.

#### Changes

- Load and follow `refactor-design` after the relevant suite and public checkpoint are green.
- Update `website/README.md` to explain derived current evidence, especially that complete coverage is not promotion or stability.
- Inspect `website/content-config.json`, `README.md`, `CODEX_CLI.md`, and `EVALUATIONS.md`; update only if their public contract changed, otherwise record a concrete no-change justification.

#### Validation

- Command: `npm test`
- Expected result: behavior remains green after review.
- Command: `npm run test:e2e`
- Expected result: the public checkpoint remains green.

#### Acceptance Criteria

- No unresolved structural risk remains within scope.
- Every canonical documentation source is updated or has a recorded reason to remain unchanged.

### Milestone 4 - Complete ordered final validation

#### Goal

Prove the finished source, projection, browser behavior, formatting, and both worktrees are clean of whitespace errors.

#### Changes

- Record exact final command outcomes in this ExecPlan.
- No source changes are expected unless validation exposes a defect.

#### Validation

- Command: `npm test`
- Command: `npm run prettier:check`
- Command: `npm run build`
- Command: `npm run test:e2e`
- Command from repository root: `git diff --check`
- Command from memory worktree: `git -C _temporary/codex-skills-ai-context diff --check`
- Expected result: every command exits successfully in the listed order.

#### Acceptance Criteria

- All final commands are green in the required order.
- Every milestone acceptance criterion is satisfied.

## Progress

- [x] Repository and nested workflow instructions read.
- [x] Memory worktree and exact origin validated.
- [x] Required `execplan-tdd`, `implement-execplan`, and behavior TDD instructions loaded.
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

- Decision: Observe classification through the generator command and generated `data.json`, not through internal helper exports.
  Rationale: The command is the stable public contract used by build and tests; this avoids coupling tests to production topology.
  Date/Author: 2026-07-29 / Codex

- Decision: Preserve raw report statuses separately from the derived evidence state.
  Rationale: The state summarizes the strongest current evidence, while raw `FAIL`, `ERROR`, `INCONCLUSIVE`, `UNSTABLE`, and `INVALID_RED` outcomes remain essential contradictory context.
  Date/Author: 2026-07-29 / Codex

- Decision: Treat a report as a baseline operation whenever its source map contains `baseline`, then compare only `candidate`; otherwise compare only `evaluated`.
  Rationale: This reproduces the runner's two report shapes and prevents a coincidentally matching baseline fingerprint from establishing current evidence.
  Date/Author: 2026-07-29 / Codex

- Decision: Count current results by the report operation's original status while calculating coverage from non-baseline observation passes.
  Rationale: Operation results preserve mixed `PASS`, `FAIL`, `ERROR`, `INCONCLUSIVE`, `UNSTABLE`, and `INVALID_RED` outcomes, while case observations are the only source that can prove suite coverage.
  Date/Author: 2026-07-29 / Codex

- Decision: Use a native button as the evidence trigger and one responsive Vue dialog implementation for desktop popover and mobile bottom sheet behavior.
  Rationale: Native activation provides click, Enter, and Space semantics without custom key emulation, while one implementation keeps dismissal and focus restoration consistent.
  Date/Author: 2026-07-29 / Codex

- Decision: Follow Python Unicode code point ordering and `ensure_ascii` serialization, including linked files, when recreating the runner fingerprint.
  Rationale: JavaScript's default UTF 16 ordering and literal Unicode JSON output differ from the runner for valid file names. Exact source identity requires reproducing the runner's serialized bytes, not merely hashing the same apparent values.
  Date/Author: 2026-07-29 / Codex

- Decision: Store one authoritative `currentReportGroups` mapping and derive status counts from it.
  Rationale: The design review identified duplicate grouping transformations that could let linked reports and displayed counts diverge.
  Date/Author: 2026-07-29 / Codex

## Risks and Mitigations

- Risk: A JavaScript fingerprint that only resembles the Python runner could falsely mark stale evidence current.
  Mitigation: Read the canonical runner code, reproduce its file selection, symlink handling, mode, SHA-256, Unicode ordering, and escaped JSON bytes exactly, and prove parity with deterministic cross runtime fixtures.

- Risk: Baseline observations or fingerprints could accidentally establish current evidence.
  Mitigation: Select only `sources.candidate` for operations with a baseline and exclude observations with role `baseline` from coverage and pass aggregation.

- Risk: A complete suite pass could be mistaken for stable promotion.
  Mitigation: Give promotion a distinct state, use separate visual tokens, and state explicitly in UI and documentation that complete coverage says nothing about repetition or promotion.

- Risk: Overlay controls could create nested links, trap focus, or behave differently across pointer and keyboard input.
  Mitigation: Render articles with sibling actions, implement Escape/outside dismissal and focus restoration, and exercise desktop and mobile journeys.

- Risk: Real archived report schemas may contain older variants.
  Mitigation: Treat absent or unrecognized fingerprints as historical and preserve their history links rather than guessing.

## Validation Strategy

1. Add one observable generator behavior at a time and run the full `npm test` suite for each RED and GREEN.
2. Run `npm run test:e2e` at least every two behavior cycles and at each completed milestone.
3. After all behavior and public journeys are green, run the `refactor-design` gate and repeat both checks after material refactors.
4. Reconcile every canonical documentation source.
5. Run the ordered final validation and both `git diff --check` commands.

Final validation completed on 2026-07-29 in the required order:

1. `npm test` passed all 20 tests.
2. `npm run prettier:check` reported that all matched files use Prettier style.
3. `npm run build` validated 53 archived reports, generated 10 skills and 53 reports, and completed the VitePress build. Vite emitted its existing advisory that some minified chunks exceed 500 kB; this is not a build failure.
4. `npm run test:e2e` rebuilt the site and passed all 22 desktop Chromium and Pixel 7 journeys.
5. `git diff --check` completed with no output.
6. `git -C _temporary/codex-skills-ai-context diff --check` completed with no output.

## Documentation Impact

- `website/README.md`: updated with the six derived states, candidate versus evaluated fingerprint rule, baseline exclusion, and the boundary between complete coverage, stability, and promotion.
- `website/content-config.json`: unchanged because no public configuration option was added; it continues to own only the base path and disabled catalog skills.
- Root `README.md`: unchanged because it links to the public catalog but does not define website evidence status semantics.
- `CODEX_CLI.md`: unchanged because the change adds no CLI command, flag, discovery behavior, or workflow instruction.
- `EVALUATIONS.md`: unchanged because it remains the canonical explanation of runner fingerprints, gates, coverage, and promotion. The new six status projection is specific to the website and is documented in `website/README.md`.
- `website/.generated/`: disposable projection; never edit directly.

## Rollout and Recovery

The change is static website source and takes effect only after the existing GitHub Pages workflow builds a later committed revision. No publication is authorized here. Recovery is a normal source revert of generator, theme, component, test, and documentation changes, followed by regeneration and the full validation sequence. Archived evaluation data is not mutated.

## Lessons Learned

- The current generator uses a manual `evidenceLabels` map, so labels can drift from both source content and archived evidence. A single derived model is required before styling can be trustworthy.
- The real archive confirms the requested classification: the three promotion skills exactly match their latest eligible passing candidate fingerprints; `refactor-design` has three current passing cases out of twelve; and all `develop-skill-with-evals` fingerprints are historical.
- The first browser checkpoint exposed the expected stale journey for “No archived evidence”; after the UI migration, an outside-dismissal journey exposed a real focus timing defect. Closing on the completed click event, rather than pointer down, restores focus reliably on desktop and touch profiles.
- The chosen light and dark accent/surface pairs have computed contrast ratios from 4.80:1 to 7.24:1, exceeding the 3:1 indicator and relevant border target. Primary text continues to use the theme's normal text tokens.
- Milestone 1 and 2 validation: `npm test` passed 18 tests; `npm run test:e2e` rebuilt the site and passed 22 journeys across desktop Chromium and Pixel 7.
- The post GREEN review found two missing fingerprint edge cases, linked files and Unicode serialization, plus missing report links within result groups. Each returned to behavior TDD with an observed RED before implementation. The final review consolidated duplicated grouping logic as a behavior preserving refactor.
- Post review validation: `npm test` passed 20 tests and `npm run test:e2e` passed all 22 desktop and Pixel 7 journeys after the grouping refactor.
- The ordered final validation remained fully green. The only retained advisory is Vite's existing chunk size warning; no new dependency or bundling policy was introduced in this scope.
