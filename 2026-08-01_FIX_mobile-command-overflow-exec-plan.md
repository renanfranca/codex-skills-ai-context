# Prevent horizontal overflow in mobile evaluation pages

This ExecPlan is a living document. Keep `Progress`, `Decisions`, `Risks and Mitigations`, `Documentation Impact`, and `Lessons Learned` current throughout implementation.

## Purpose / Big Picture

The active `restructure-documentation` evaluation page currently creates page-level horizontal scrolling on mobile. The command rows keep a long `<code>` item at its intrinsic minimum width while reserving space for the `Expected exit: 0` label, so the command row becomes wider than the viewport. A reader should be able to open the page at the `#judge-verification` anchor, read every command and exit label, and never need to scroll the entire page horizontally.

## Scope

In scope are the shared command-list presentation rule, an end-to-end regression check through the public evaluation route, and reconciliation of the affected evaluation-flow visual baseline. The fix must preserve the fixed exit label, allow only the command text to wrap, and retain the independent horizontal scrolling of long prompt code blocks.

Out of scope is the separate 8 px overflow observed in the VitePress navigation at exactly 320 CSS pixels. That header issue is global and requires a separate decision about title truncation; this change targets the reproduced command-row defect at common mobile widths.

## Definitions

- **Page-level horizontal overflow:** `document.documentElement.scrollWidth` is greater than the viewport width, making the browser page itself horizontally scrollable.
- **Command list:** The generated `.command-list` rows that pair a command `<code>` element with an `Expected exit: 0` label.
- **Prompt code block:** The long public prompt rendered by VitePress inside a `.language-text` container. It intentionally keeps its own horizontal scroll area.

## Existing Context

- `website/scripts/generate-content.mjs` generates the evaluation page and its command rows; the generated page is disposable and must not be edited directly.
- `website/.vitepress/theme/custom.css` currently makes `.command-list li` a flex row and keeps `.command-list span` as a non-shrinking item, but does not give the command code a zero minimum width.
- At a 390 px CSS viewport, the target route currently reports a document width of about 557 px. The overflowing descendants are the command rows and their fixed exit labels; the long prompt is contained by VitePress's `overflow-x: auto` code-block rule.
- `website/e2e/site.spec.mjs` already exercises evaluation pages and the public route through the configured desktop and Pixel 7 projects. `website/playwright.config.mjs` supplies the base URL and the mobile project.

## Desired End State

- The target evaluation route has no page-level horizontal overflow in the configured mobile project.
- Each command code item can shrink and wrap at arbitrary path boundaries when the available row width is insufficient.
- `Expected exit: 0` remains visible at the end of the row.
- Desktop command rows and independently scrollable prompt blocks retain their existing behavior.
- No public API, data model, generated projection, navigation behavior, or documentation contract changes.

## Milestones

### Milestone 1 - Contain command rows on mobile

#### Goal

Protect the public evaluation journey with a failing E2E assertion, then apply the minimum CSS change that removes page-level overflow while retaining command semantics.

#### Changes

- [x] Add one behavior-focused test in `website/e2e/site.spec.mjs` that opens `skills/restructure-documentation/evaluations/documentation-system-restructure#judge-verification`, checks the target section and exit label, and asserts `document.documentElement.scrollWidth <= window.innerWidth`.
- [x] Add `min-width: 0` and `overflow-wrap: anywhere` to `.command-list code` in `website/.vitepress/theme/custom.css`. Do not add a global `overflow-x: hidden` rule or modify the generated page.
- [x] Reconcile `website/e2e/site.spec.mjs-snapshots/evaluation-flow-mobile-linux.png` after the CSS fix removes page-level overflow and changes the `.definition-flow` capture from surrounding prose to the intended six flow cards.
- [x] Leave `website/README.md`, the public VitePress configuration, and root documentation unchanged after inspecting them; record the concrete no-change rationale in `Documentation Impact`.

#### Validation

- [x] Run `npm test` from `website/` after adding the test; the deterministic suite remains green because the visual behavior is covered by E2E (37 passed).
- [x] Run `npm run test:e2e` before the CSS change and record the expected mobile failure caused by page width exceeding the viewport.
- [x] Run `npm test` and the focused `npm run test:e2e -- --grep "active evaluation pages keep long commands visible" --project desktop` after the CSS change; the deterministic suite and focused public journey pass.
- [x] Compare the mobile `evaluation-flow.png` expected and actual images, confirm that clean `HEAD` passes with the former geometry, and update only the mobile snapshot because the CSS correction changes the locator capture to the intended flow cards.
- [x] Run the post GREEN design review for the two-line CSS rule, the E2E assertion, and the targeted snapshot update, using the existing public-path tests as protection; no actionable structural finding was identified.
- [x] Run final validation in this exact order: `npm test`, `npm run prettier:check`, `npm run build`, `npm run test:e2e`. All four commands pass; the E2E suite reports 32 passed and 2 skipped.

#### Acceptance Criteria

- [x] The target route loads at the requested `#judge-verification` anchor without page-level horizontal scrolling in the deterministic 390 px narrow-viewport test.
- [x] The command and `Expected exit: 0` are both visible, with long command paths wrapping inside the row.
- [x] The prompt code block still scrolls horizontally inside its own container when needed, as confirmed during diagnosis and by the unchanged VitePress rule.
- [x] Existing desktop and evaluation-page journeys remain fully green; the corrected mobile `evaluation-flow.png` snapshot now matches the current `.definition-flow` rendering.

## Progress

- [x] Investigated the published-route equivalent and local production preview.
- [x] Confirmed the command-row overflow at a 390 px viewport and isolated the overflowing elements.
- [x] Confirmed the prompt code block is already independently scrollable.
- [x] Confirmed the memory worktree and exact origin required by repository instructions.
- [x] Milestone 1 started.
- [x] Behavior test added and RED recorded.
- [x] CSS fix applied and relevant suite GREEN.
- [x] Public checkpoint GREEN; the focused new journey and the full E2E suite are GREEN.
- [x] Post GREEN design review completed; the changed CSS is local, the test crosses the public route, and the snapshot reconciliation changes no production behavior.
- [x] Documentation reconciliation completed.
- [x] Final validation completed; all required commands pass.
- [x] Clean `HEAD` visual checkpoint reproduced in an isolated copy and passed, correcting the earlier claim that the snapshot failure was pre-existing.
- [x] Narrow-viewport test uses a coherent desktop browser profile and explicitly proves the long command remains visible.
- [x] Revised public checkpoint, post GREEN design review, and required final validation completed without further code changes.

## Decisions

- Decision: Keep the exit label fixed and allow only command code to shrink and wrap.
  Rationale: This preserves the existing command-status relationship while eliminating the measured overflow.
  Date/Author: 2026-08-01 / Codex

- Decision: Use only `min-width: 0` and `overflow-wrap: anywhere` for the command code.
  Rationale: A browser prototype showed these declarations reduce the 390 px document width from about 557 px to the viewport width without requiring a flex-layout rewrite.
  Date/Author: 2026-08-01 / Codex

- Decision: Keep the 320 px VitePress navigation overflow out of this milestone.
  Rationale: It is a separate global header defect, not the command-row defect reported on the evaluation page, and fixing it would introduce a title-truncation UX decision.
  Date/Author: 2026-08-01 / Codex

- Decision: Run the regression in the desktop project with a deterministic 390 px CSS viewport.
  Rationale: The defect depends on available CSS width, while overriding `isMobile` inside the Pixel 7 project creates an artificial combination of mobile user agent, touch, and device scale with desktop viewport semantics. The desktop project at 390 px reproduces the observed 557 px page width without a hybrid device profile; the normal full E2E suite continues to cover the real Pixel 7 project.
  Date/Author: 2026-08-01 / Codex

- Decision: Refresh only the mobile evaluation-flow snapshot as an expected consequence of the overflow fix.
  Rationale: The isolated clean `HEAD` test passes with the former baseline, proving the snapshot did not fail beforehand. After the CSS fix removes page-level overflow, Playwright captures the `.definition-flow` locator as the intended six flow cards instead of including preceding prose. The updated snapshot records that corrected geometry without changing the production flow itself.
  Date/Author: 2026-08-01 / Codex

- Decision: Make no post GREEN structural refactor.
  Rationale: The design review found no temporal coupling, hidden invocation state, mixed responsibility, or test-only abstraction in the changed CSS, public-path assertion, or visual baseline. The smallest local rule already expresses the required responsive invariant.
  Date/Author: 2026-08-01 / Codex

## Risks and Mitigations

- Risk: `overflow-wrap: anywhere` can split a command path at an arbitrary character when space is constrained.
  Mitigation: Apply it only to command code, preserve normal desktop width, and assert that the command remains readable and the exit label remains visible.

- Risk: A broad overflow rule could hide other layout defects or break intentional code-block scrolling.
  Mitigation: Change only `.command-list code`; leave VitePress code-block overflow behavior untouched.

- Risk: A narrow desktop browser does not reproduce every mobile-browser semantic.
  Mitigation: Use it only for the deterministic CSS-width regression, exercise the exact public route, and retain the repository's full Pixel 7 project as the mobile-browser checkpoint.

- Risk: A visual baseline update can hide an unintended visual regression if it is accepted without inspecting the expected and actual images.
  Mitigation: Reproduce the failure in isolation, inspect both images, verify the desktop baseline and locator semantics, and regenerate only `evaluation-flow-mobile-linux.png`. The full suite then passes without changing the production flow layout.

## Validation Strategy

1. Add the public-path behavior test and confirm the expected E2E RED.
2. Apply the smallest CSS change and run the deterministic suite plus public E2E checkpoint.
3. Review the changed CSS and test for unnecessary coupling or broader layout impact.
4. Inspect every canonical documentation source named by `website/AGENTS.md` and record whether it changed.
5. Run the required final commands in their declared order and record exact outcomes here.

Current evidence: `npm test` passes with 37 tests. The revised narrow desktop regression fails against clean `HEAD` because page width exceeds the viewport, then passes with the CSS correction while proving the long command and exit label remain visible. The isolated clean `HEAD` visual test passes with the former baseline, proving the later snapshot change is caused by the CSS correction rather than a pre-existing failure. After the revised post GREEN review, `npm run prettier:check` and `npm run build` pass, and the final `npm run test:e2e` reports 32 passed and 2 skipped.

## Documentation Impact

- `website/README.md`: no update required. The fix changes responsive presentation of existing evaluation commands and introduces no new public concept, route, command, or validation contract.
- `website/.vitepress/config.mjs`: no update required. The base path, route policy, and public navigation configuration remain unchanged.
- Root repository documentation: no update required. The change is confined to website CSS and a browser regression test.
- `website/.generated/`: no direct edit. It remains a disposable projection regenerated by the repository build workflow.

## Rollout and Recovery

The normal GitHub Actions website workflow will rebuild and publish the static artifact after the change reaches the repository's normal release path. Recovery is a targeted revert of the command-list CSS declaration and its regression assertion; no migration or data repair is required. No commit, push, or deployment is part of this task unless explicitly requested.

## Lessons Learned

- The page anchor identifies where the problem is noticed, but global `scrollWidth` inspection is necessary because the offending command rows occur earlier in the document.
- VitePress already contains long prompt code inside an internal scroll container; page-level overflow should be diagnosed separately from intentional code-block overflow.
- The generated page currently contains duplicate `judge-verification` IDs on its heading and mechanism panel, so the regression test scopes the selector to `.mechanism-section` rather than relying on a strict ID-only locator.
- The first mobile E2E assertion passed without CSS because Pixel 7 emulation did not provide the required 390 px layout behavior. A deterministic narrow desktop viewport is clearer than overriding `isMobile` inside a mobile project.
- Visual snapshot failures need to be checked against the locator's current bounding box and its paired desktop baseline before changing production layout; here the mobile reference had captured surrounding prose while the locator contract was the flow list itself.
- Removing only the new regression test did not isolate the snapshot cause because the CSS change remained active. A clean `HEAD` reproduction is required before classifying a failure as pre-existing.
