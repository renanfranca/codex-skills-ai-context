# Add the promotion qualification map

This ExecPlan is a living document. Keep `Progress`, `Decisions`, `Risks and Mitigations`, and `Lessons Learned` up to date as work advances.

## Purpose / Big Picture

Every generated skill page will show the same always-expanded, documentary map immediately after its current evidence callout and before its active evaluations. The map explains how a proposed change qualifies through the static, deterministic, scoped, or cross-cutting evidence paths without representing a running evaluation or changing any runner-derived evidence status.

A reader can open any skill page and understand the four qualification paths, the distinct role of static validation, and the limits of `Validated promotion` without opening the evidence panel. The existing callout and evidence panel remain the only representations of the current archived state.

## Scope

In scope: a reusable semantic HTML renderer in `website/scripts/generate-content.mjs`; internal website CSS for a four-phase vertical timeline and responsive impact-card grid; deterministic generator coverage; Playwright journey and approved snapshots; and a concise `website/README.md` update.

Out of scope: CLI behavior, JSON schemas, report formats, Vue properties, evidence-state derivation, evaluation/operation/report pages, `EvidenceStatus.vue`, the evaluation runner, generated files under `website/.generated/`, publishing, commits, pushes, and model-backed evaluations. This is deterministic website work and uses zero model sessions.

Future work that is explicitly out of scope and recorded only here: archived plans with impact, cases, stages, and fingerprints; explicit per-gate results without inferring them from observations; identity and expiry for an in-progress promotion; canonical qualification for `static` changes; a dynamic tracker or last archived checkpoint; progress totals proportional to the selected path rather than a fixed count; distinctions among cost, process, and promotion authorization; and a complete publication flow that adds review, metadata, and a fresh-agent test.

## Definitions

**Evidence callout** is the existing static summary of the archived evidence currently matching a skill source. **Qualification map** is the new documentary explanation, not stateful UI. **RED** is the expected baseline failure that proves an affected behavior is exercised; **GREEN** is a candidate pass. **Fingerprint** is the canonical source identity recorded by the runner. **Validated promotion** is the existing evidence status for an eligible archived `PASS` matching the current source; it is not a general correctness or publication claim.

## Existing Context

`website/scripts/generate-content.mjs` emits canonical Markdown/HTML pages into disposable `website/.generated/`; `renderSkill` currently places the evidence callout directly before `## Active evaluations`. `website/.vitepress/theme/custom.css` already defines theme variables, responsive media queries, and evidence surfaces but has no documentary qualification map. `website/tests/site-content.test.mjs` executes the generator against temporary repositories; `website/e2e/site.spec.mjs` exercises the built public site on desktop and mobile.

The applicable workflow profile is `website/AGENTS.md`: every TDD cycle uses `npm test` from `website/`, the public checkpoint is `npm run test:e2e`, and final validation is `npm test`, `npm run prettier:check`, `npm run build`, then `npm run test:e2e`. Canonical documentation sources are `website/README.md`, applicable public configuration, and root repository documentation. The required memory worktree exists at `_temporary/codex-skills-ai-context` and its `origin` is exactly `https://github.com/renanfranca/codex-skills-ai-context.git`.

## Desired End State

All generated skill pages contain one `.promotion-qualification-map` between their evidence callout and active evaluations. The map has the specified English title, documentary introduction, four ordered phases, four exact impact labels, the static exception, qualification limits, and closing limitation. Its semantic ordered phases and card headings communicate the content without color. Desktop cards form a two-by-two grid; at 640 px and below they form one column, remain readable in both themes, and do not cause horizontal overflow. No page claims live progress.

## Milestones

### Milestone 1 - Generate the documentary qualification map

#### Goal

Lead with a generator-level behavioral test, then produce the same semantic qualification map on every skill page without altering evidence behavior.

#### Changes

- [x] Add one behavior-focused temporary-workspace test to `website/tests/site-content.test.mjs` that verifies map position, heading, four impacts, gate ordering, static exception, qualification limitation, and the absence of a live-progress claim.
- [x] Add `renderPromotionQualificationMap` to `website/scripts/generate-content.mjs` and insert its single call immediately after the evidence callout in `renderSkill`.
- [x] Do not edit `website/.generated/`, runner code, schemas, reports, Vue components, or evidence derivation.

#### Validation

- [x] Command: `npm test`
- [x] Expected result: baseline and new generator behavior tests pass after the initially absent map gives the expected RED.

#### Acceptance Criteria

- [x] A generated skill page exposes a documentary, non-live four-path map before `Active evaluations`.
- [x] `Static` is never presented as producing `Validated promotion`.

### Milestone 2 - Present and document the map

#### Goal

Lead with the public desktop/mobile journey, then provide responsive, theme-aware documentary styling and reconcile documentation.

#### Changes

- [x] Add a Playwright journey in `website/e2e/site.spec.mjs` for always-visible content, two-by-two desktop cards, one-column mobile cards, light/dark readability, no overflow, and text-first comprehension; create `promotion-qualification-map` snapshots in the official Playwright environment.
- [x] Add internal `.promotion-qualification-*` styles in `website/.vitepress/theme/custom.css`, using existing text, divider, background, and brand variables without hover or interactive controls.
- [x] Update `website/README.md` only with delivered map position, documentary nature, static-versus-validated-promotion distinction, and lack of live tracking.
- [x] Inspect `EVALUATIONS.md`, root `README.md`, and applicable public configuration. Leave each unchanged only when it remains accurate and record why.

#### Validation

- [x] Command: `npm test`
- [x] Expected result: generator behavior remains green.
- [x] Command: `npm run test:e2e`
- [x] Expected result: public desktop/mobile journey and official snapshots pass.

#### Acceptance Criteria

- [x] The map is visible without opening a panel, works in both themes, is responsive, and has no horizontal overflow.
- [x] Documentation describes only the delivered static behavior.

### Milestone 3 - Review, reconcile, and validate

#### Goal

Use `refactor-design` only after both behavior and public-path gates are green, then run the profile’s complete ordered validation.

#### Changes

- [x] Inspect only altered generator, tests, CSS, and README for structural risks; make any behavior-preserving refactor that has concrete evidence.
- [x] Reconcile all canonical documentation sources and update this plan with exact results, decisions, risks, and recovery details.

#### Validation

- [x] Command: `npm test`
- [x] Command: `npm run prettier:check`
- [x] Command: `npm run build`
- [x] Command: `npm run test:e2e`
- [x] Expected result: all commands pass in that order from `website/`.

#### Acceptance Criteria

- [x] All requested behavior is implemented, design-reviewed, documented, and fully validated.

## Progress

- [x] Confirmed the required memory worktree and exact origin.
- [x] Read root and website repository instructions plus `execplan-tdd`, `implement-execplan`, `tdd-behavior-autonomous-quiet`, and `refactor-design` requirements.
- [x] Milestone 1 started.
- [x] Recorded baseline `npm test` result: 38 passing tests, 0 failures (2026-08-05).
- [x] Milestone 1 generator RED observed: the new page test failed because no map followed the evidence callout (2026-08-05).
- [x] Milestone 1 generator GREEN observed: 39 passing tests, 0 failures after adding the stateless renderer (2026-08-05).
- [x] Milestone 2 started.
- [x] Milestone 2 public-path RED observed: desktop impact cards computed to one column before the map CSS was added (2026-08-05).
- [x] Milestone 2 public-path GREEN observed with official snapshots: 34 passed and 2 existing desktop-only skips (2026-08-05).
- [x] `refactor-design` entry gate satisfied and review completed: no structural risk justified a redesign; restored an incidental renderer indentation change to minimize the diff (2026-08-05).
- [x] Documentation reconciled.
- [x] Final ordered validation completed: `npm test` (39 passed), `npm run prettier:check` (passed), `npm run build` (passed), and official `npm run test:e2e` (34 passed, 2 existing desktop-only skips) (2026-08-05).

## Decisions

- Decision: Create the requested plan file because it was absent, while leaving the existing untracked memory directory and all other files untouched.
  Rationale: The user explicitly requires this exact destination and filename; the verified worktree is the only authorized plan location.
  Date/Author: 2026-08-05 / Codex

- Decision: Keep the map generator-owned and stateless.
  Rationale: The requirement is documentary content on every skill page and explicitly rejects live tracking or new evidence-state derivation.
  Date/Author: 2026-08-05 / Codex

- Decision: Use one semantic `<section>` with an ordered list of four phases and nested `<article>` cards for the four impact paths.
  Rationale: The source order remains available to assistive technology and survives responsive presentation without relying on color or interaction.
  Date/Author: 2026-08-05 / Codex

- Decision: Capture only the qualification map in visual baselines, temporarily hiding fixed VitePress navigation and outline controls during the screenshot.
  Rationale: The map’s responsive content is the visual contract; fixed controls otherwise overlap a locator screenshot without being part of the map.
  Date/Author: 2026-08-05 / Codex

- Decision: Add `scroll-margin-top` to the static map.
  Rationale: A fragment or test scroll to the map leaves the title below the fixed navigation while preserving its noninteractive character.
  Date/Author: 2026-08-05 / Codex

- Decision: Keep test formatting scoped to the changed deterministic test file after the final formatting gate identified it.
  Rationale: `npx prettier --write tests/site-content.test.mjs` applied the repository formatter without touching generated output or unrelated website sources; the complete required validation sequence then restarted.
  Date/Author: 2026-08-05 / Codex

## Risks and Mitigations

- Risk: Documentary copy could be mistaken for a real-time process tracker.
  Mitigation: State both in the introduction and closing limitation that it explains qualification only, and test for no live-progress claim.

- Risk: `static` wording could imply an evidence status that the runner does not currently produce.
  Mitigation: State the exception directly in its card and test the exact limitation.

- Risk: Timeline/card layout may overflow on small devices or become dependent on color.
  Mitigation: Use semantic ordered content, textual headings, existing theme variables, a 640 px single-column breakpoint, and Playwright desktop/mobile overflow checks.

- Risk: Generated output could be edited directly or stale snapshots could hide rendering differences.
  Mitigation: Change only canonical generator/CSS/test files and use the official containerized checkpoint to create and verify snapshots.

- Risk: The public E2E environment reports existing package-audit and bundle-size warnings.
  Mitigation: They are outside this static-content scope and did not fail the official checkpoint; leave dependency/configuration changes out of this delivery.

## Validation Strategy

1. Establish the requested `npm test` baseline from `website/`.
2. Add and run one behavior-focused generator test to observe RED, implement the minimal renderer, then rerun all `npm test` tests for GREEN.
3. Add and run one public Playwright journey to observe RED, add CSS and snapshots, then rerun the official `npm run test:e2e` checkpoint for GREEN.
4. After both gates are green, perform `refactor-design` only within altered files, then rerun its protected behavior and public-path checks if code changes.
5. Reconcile canonical documentation sources and run final validation in the mandated order.

Final evidence on 2026-08-05:

1. `npm test` completed with 39 passing tests and zero failures.
2. `npm run prettier:check` completed with all matched files formatted after one scoped formatter correction.
3. `npm run build` validated the evaluation archive (53 reports, one comparison), regenerated the disposable projection, and built VitePress successfully. Its existing bundle-size advisory remained non-blocking.
4. `npm run test:e2e` used the pinned official Playwright container and completed with 34 passing tests and two existing desktop-only skips. The new `promotion-qualification-map` baselines passed for desktop/mobile and light/dark themes.

## Documentation Impact

- `website/README.md`: update to document the map’s placement after the evidence callout and before active evaluations, its documentary/no-live nature, and why static structural validation is not `Validated promotion`.
- `website/content-config.json` and public VitePress configuration: inspected and unchanged. The map adds no configuration, route, public API, or deployment behavior.
- Root `README.md`: inspected and unchanged. It documents repository-level skill discovery and contribution context, not this website-only documentary block.
- `EVALUATIONS.md`: inspected and unchanged. Evaluation execution, contracts, schemas, reports, and runner behavior are unchanged.
- `website/.generated/`: remains a disposable projection and must not be edited.

## Rollout and Recovery

The change publishes only when a normal future website deployment occurs; this task neither publishes nor commits. Recovery is a source-level revert of the renderer, CSS, tests, snapshots, README paragraph, and this plan’s associated changes, followed by the same final validation commands. No archive, report, schema, runner, or external state must be migrated or recovered.

## Lessons Learned

- The requested plan path did not already exist at the verified memory worktree when work began; creating it is necessary, while unrelated untracked memory and Python cache files must be preserved.
- The repository baseline was 38 passing `npm test` tests; the new deterministic generator behavior test raised it to 39 while leaving evidence derivation unchanged.
- VitePress fixed navigation and mobile outline controls can overlap a locator-only screenshot even though they do not belong to the map; isolate those controls during snapshot capture rather than adding product interaction or layout behavior.
- The final formatter gate caught only the newly added generator test’s line wrapping. A scoped Prettier write followed by a complete restart of final validation restored the green state.
