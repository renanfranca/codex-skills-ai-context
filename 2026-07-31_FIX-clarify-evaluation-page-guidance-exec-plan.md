# Clarify evaluation page guidance

This ExecPlan is a living document. Keep `Progress`, `Decisions`, `Risks and Mitigations`, and `Lessons Learned` up to date as work advances.

## Purpose / Big Picture

Evaluation catalog pages currently expose the relevant facts, but readers must infer how a persistent evaluation definition relates to observations produced by runner operations. This change makes active and historical evaluation pages understandable on their own, without requiring readers to open the broader report guide. A reader will see a concise explanation at the top, local section guidance, expanded coverage contract facts, and contextual help for ambiguous terms while existing report help behavior remains unchanged.

## Scope

In scope are the generated skill and evaluation catalog pages, the evaluation glossary and contextual help component, public content tests, responsive Playwright journeys, intentional visual baselines if required, and reconciliation of `website/README.md`.

Out of scope are route changes, source or case fingerprints, evidence-state derivation, canonical coverage contracts, archived report data, root `content-config.json` decisions, deployment, publication, commits, and pushes. Generated files under `website/.generated/` remain disposable projections and will not be edited directly.

## Definitions

An **evaluation** is a persistent case contract declared by a skill. An **observation** is one recorded case result inside an archived runner invocation. An **operation** is that complete runner invocation and may contain several observations.

A **coverage contract** is a declaration in `evals/coverage.json` that maps intended skill behavior to one or more evaluation cases. It communicates intended coverage, not proof that an operation passed.

Coverage level **complete** declares that a mapping is intended to protect the whole named contract. Coverage level **partial** declares narrower protection and must not be read as complete protection. A **dimension** names the aspect protected by a mapping. **Evidence mechanisms** name the declared verification paths, and a **limitation** records a known boundary.

**Current evidence** is the evidence status derived for the current skill and case fingerprints. **Latest recorded result** is the newest related operation result, regardless of whether it establishes current evidence. **Suite state** identifies whether the case belongs to the active suite or exists only in archived history.

## Existing Context

`website/scripts/evaluation-catalog.mjs` reads active case definitions and reverses optional `evals/coverage.json` mappings into each case model. `website/scripts/generate-content.mjs` turns that model into skill cards, active case pages, historical case pages, operation pages, and the operation archive. `website/scripts/evaluation-glossary.mjs` is the canonical vocabulary used by generated contextual help. The Vue component and styles under `website/.vitepress/` render `EvaluationHelp` as an anchored desktop popover or responsive mobile bottom sheet.

`website/tests/site-content.test.mjs` observes public generated Markdown and JSON. `website/e2e/site.spec.mjs` exercises the built site in desktop Chromium and a Pixel 7 profile. `website/README.md` is the canonical contributor and behavior guide for the catalog.

## Desired End State

Every active evaluation page begins with an always-visible explanation of evaluation, observation, and operation. Relevant sections explain what their facts mean before presenting data. `Covered skill contracts` remains the heading and explicitly says mappings are intended coverage rather than execution results. Each mapped contract shows its declared `complete` or `partial` level, protected dimension, declared evidence mechanisms, and limitation when present.

Contextual help is available for `Current evidence`, `Latest recorded result`, `Suite state`, coverage level, and dimension. `EvaluationHelp` accepts evaluation-page context while preserving all existing report-page behavior. The glossary defines evaluation page terms and the closed coverage-level values. Historical pages explain why operation history exists even though a current definition is unavailable. Skill pages introduce the evaluation cards without adding nested interactive controls.

## Milestones

### Milestone 1 - Specify public evaluation guidance

#### Goal

Add behavior-focused content assertions through generated Markdown and glossary output for active pages, historical pages, complete and partial contract mappings, limitations, evidence mechanisms, and missing coverage maps.

#### Changes

- Edit `website/tests/site-content.test.mjs` to express the public content contract before implementation.
- Keep assertions at generated Markdown and exported glossary observation points rather than internal helper topology.
- No canonical documentation changes occur until final behavior is green and reconciled.

#### Validation

- Command: `npm test` from `website/`.
- Expected result before implementation: the new assertions fail because the guidance and expanded mapping facts do not yet exist.
- Expected result after implementation: all content and deterministic website tests pass.

#### Acceptance Criteria

- Active and historical page guidance is observable in generated Markdown.
- Both coverage levels and all required mapping details are covered.
- Missing coverage remains explicit.

### Milestone 2 - Implement generated content and contextual help

#### Goal

Make active and historical pages self-explanatory, expand coverage contract presentation, and generalize contextual help without regressing report pages.

#### Changes

- Edit `website/scripts/evaluation-catalog.mjs` only if the public model does not already retain coverage level or mechanisms.
- Edit `website/scripts/evaluation-glossary.mjs` to add evaluation-page terms and coverage-level values.
- Edit `website/scripts/generate-content.mjs` to add visible guidance, section explanations, expanded contract facts, historical explanation, and skill-page introduction.
- Edit the relevant files under `website/.vitepress/` to accept evaluation context in `EvaluationHelp` and preserve existing report context behavior.
- Do not edit `website/.generated/` directly.

#### Validation

- Command: `npm test` from `website/` after each behavior cycle.
- Command: `npm run test:e2e` from `website/` at the public checkpoint.
- Expected result: deterministic tests pass and generated pages retain safe escaped content, stable routes, and existing report help behavior.

#### Acceptance Criteria

- The evaluation page is understandable without opening contextual help.
- Help triggers deepen ambiguous vocabulary and do not duplicate or replace the visible explanation.
- Existing report help journeys remain unchanged.

### Milestone 3 - Verify accessible responsive help

#### Goal

Exercise evaluation help in desktop Chromium and Pixel 7, including keyboard activation, Escape dismissal, focus restoration, and overflow safety.

#### Changes

- Edit `website/e2e/site.spec.mjs` to cover the evaluation-page help journey through public UI behavior.
- Update existing snapshots only when the intended guidance changes an already-covered visual region.

#### Validation

- Command: `npm run test:e2e` from `website/`.
- Expected result: both configured projects pass with no horizontal overflow, keyboard behavior works, Escape restores focus, and intentional screenshots match.

#### Acceptance Criteria

- Desktop and Pixel 7 provide equivalent readable help behavior.
- Cards contain no nested interactive controls.
- The page remains understandable before any help is opened.

### Milestone 4 - Design review, documentation, and final validation

#### Goal

Review the green implementation for structural risks, reconcile canonical documentation, and complete all repository gates in the declared order.

#### Changes

- Apply `refactor-design` only after the relevant suite and public checkpoint are green.
- Update `website/README.md` to document local evaluation-page guidance and coverage mapping presentation.
- Inspect `website/content-config.json` and root documentation, changing them only if final public behavior invalidates their existing claims.
- Record a concrete no-change justification for every canonical source left untouched.

#### Validation

- Commands from `website/`, in order: `npm test`, `npm run prettier:check`, `npm run build`, `npm run test:e2e`.
- Commands from their respective roots after website validation: `git diff --check` in `/home/renanfranca/.codex/skills` and `git diff --check` in `_temporary/codex-skills-ai-context`.
- Expected result: every command exits successfully.

#### Acceptance Criteria

- Design review finds no unresolved structural risk.
- Canonical documentation matches the final behavior.
- All required commands pass in the mandated order.

## Progress

- [x] Repository and nested website instructions loaded.
- [x] Memory worktree and exact `origin` validated.
- [x] ExecPlan created before test or production edits.
- [x] Milestone 1 started.
- [x] Milestone 1 completed with expected RED evidence.
- [x] Milestone 2 started.
- [x] Milestone 2 completed with `npm test` green.
- [x] Milestone 3 started.
- [x] Milestone 3 completed with public checkpoint green.
- [x] Milestone 4 started.
- [x] Post-GREEN design review completed.
- [x] Documentation reconciled.
- [x] Final validation completed in required order.

## Decisions

- Decision: Keep the technical heading `Covered skill contracts` and add immediate plain-language interpretation.
  Rationale: The heading names the domain concept accurately; the comprehension problem is missing local explanation, not terminology alone.
  Date/Author: 2026-07-31 / Codex

- Decision: Use visible prose for the essential reading path and contextual help only for ambiguous vocabulary.
  Rationale: The page must remain understandable without repeated interaction, while compact help can preserve depth.
  Date/Author: 2026-07-31 / Codex

- Decision: Preserve routes, evidence derivation, fingerprints, archived records, and canonical coverage declarations.
  Rationale: This is a comprehension and presentation fix, not a change to evaluation semantics.
  Date/Author: 2026-07-31 / Codex

- Decision: Extract one shared renderer for the identical active and historical reading guide.
  Rationale: The evaluation/observation/operation definition is one canonical page concept; duplicating its generated HTML created a concrete divergence risk. Other similar markup remains local because extraction would add indirection without removing a demonstrated risk.
  Date/Author: 2026-07-31 / Codex

## Risks and Mitigations

- Risk: Added explanations could make dense pages harder to scan.
  Mitigation: Use one concise sentence per section and reserve panels for vocabulary depth.

- Risk: Generalizing `EvaluationHelp` could regress report help behavior.
  Mitigation: Preserve the existing report context as the default and retain its deterministic and E2E coverage.

- Risk: Coverage mappings could be mistaken for executed proof.
  Mitigation: State explicitly beside the heading that mappings describe intended protection and do not report an operation result.

- Risk: Mobile help could overflow or fail to restore focus.
  Mitigation: Run the public journey in the Pixel 7 project and assert overflow, Escape dismissal, and focus restoration.

- Risk: Smooth scrolling can leave a tall mobile element screenshot in motion after focusing its first stage.
  Mitigation: Disable smooth scrolling within that Playwright journey before focus and screenshot capture, matching the existing report-help screenshot practice.

- Risk: Existing uncommitted work could be overwritten.
  Mitigation: Inspect current files before each edit, make narrow patches, and never reset or clean either worktree.

## Validation Strategy

1. Add one observable content behavior at a time to `website/tests/site-content.test.mjs` and run the full relevant suite, `npm test`, to prove RED and GREEN.
2. Exercise the built public path with `npm run test:e2e` at the required checkpoint and after E2E journey changes.
3. After public GREEN, run the `refactor-design` gate and rerun both suite and checkpoint after material refactors.
4. Reconcile `website/README.md`, `website/content-config.json`, and root repository documentation.
5. Run final validation exactly as declared, then `git diff --check` in both worktrees.

Final validation evidence after the formatting correction:

1. `npm test` from `website/`: PASS, 37 tests passed.
2. `npm run prettier:check` from `website/`: PASS, all matched files use Prettier formatting.
3. `npm run build` from `website/`: PASS, archive validation reported 53 reports and the VitePress site rendered successfully. Rollup retained its existing informational large chunk warning.
4. `npm run test:e2e` from `website/`: PASS, 31 journeys passed in desktop Chromium and Pixel 7; one preconfigured journey was skipped.
5. `git diff --check` from `/home/renanfranca/.codex/skills`: PASS.
6. `git diff --check` from `_temporary/codex-skills-ai-context`: PASS.

## Documentation Impact

- `website/README.md`: updated to describe always visible page guidance, expanded coverage mappings, historical page rationale, and evaluation context help.
- `website/content-config.json`: inspected and left unchanged because it controls the site base path and compatibility skill exclusions, not evaluation page presentation.
- Root `README.md`: inspected and left unchanged because its website content only tells contributors how to start Codex in the nested scope.
- Root `CODEX_CLI.md`: inspected and left unchanged because it documents Codex CLI operation rather than website presentation.
- Root `EVALUATIONS.md`: inspected and left unchanged because it specifies canonical evaluation and archive behavior; this change preserves those contracts and changes only their website explanation.
- `website/.generated/`: disposable output only, regenerated through repository commands and never edited directly.

## Rollout and Recovery

No deployment, publication, commit, or push is authorized. The change is static generated-site code and can be recovered by reverting only the narrow source, test, style, snapshot, and documentation edits recorded by this plan. Existing uncommitted work must remain untouched.

## Lessons Learned

- The website workflow profile explicitly supplies every required ExecPlan, TDD, checkpoint, validation, and documentation field.
- The memory repository is available at the required path with the exact required `origin`.
- The first public-content cycle produced the expected RED in `renders an active evaluation card and a linked current-definition page`: the generated skill page lacked the evaluation-card introduction, before implementation added that text and the local evaluation guidance.
- The coverage-mapping cycle produced the expected RED because generated contract cards exposed only a bare guarantee and dimension; they did not provide contextual help or the declared evidence mechanisms.
- The historical-page cycle produced the expected RED because archived-only pages lacked the evaluation/observation/operation reading guide and did not explain why operation history survives without a current definition.
- The case-kind wording cycle exposed a genuine comprehension defect: deterministic pages claimed that a prompt was supplied to an executor even though deterministic cases omit the executor. The page now explains deterministic public inputs accurately.
- The first public checkpoint passed 28 journeys, skipped one configured case, and exposed a repeatable mobile screenshot instability after the new section prose increased the focus scroll distance. The screenshot target itself was unchanged; the journey now disables smooth scrolling before focus capture.
- The first final-validation attempt passed `npm test` with 37 tests and then stopped at `npm run prettier:check`, which identified formatting drift only in `EvaluationHelp.vue` and `site-content.test.mjs`. Those two files require mechanical formatting before the complete validation sequence is restarted.
- After formatting only the two reported files, the complete final sequence passed in the mandated order. No route, fingerprint, evidence derivation, canonical coverage declaration, or archived report changed.
