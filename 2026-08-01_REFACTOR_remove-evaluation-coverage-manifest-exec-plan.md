# Remove the evaluation coverage manifest

This ExecPlan is a living document. Keep `Progress`, `Decisions`, `Risks and Mitigations`, and `Lessons Learned` up to date as work advances.

## Purpose / Big Picture

Remove the repository's declarative evaluation coverage mechanism without replacing it with another manual traceability field. After this change, the website and documentation describe only evidence derived from the currently executable suite and canonical archived reports. A reader can observe the result by opening the `refactor-design` skill page: it lists eleven active evaluations, reports current evidence as historical because the source fingerprint changed, and retains the retired `coverage-contract` case only in operation history.

## Scope

In scope are the `refactor-design` evaluation suite, website catalog generation and presentation, website unit and end to end tests, website styles and glossary entries used only by coverage traceability, `EVALUATIONS.md`, and `website/README.md`. The change deletes `refactor-design/evals/coverage.json` and the complete `refactor-design/evals/cases/coverage-contract/` directory, removes `coverage-contract` from the suite, removes all website interpretation of `coverage.json`, and renames suite result fields and labels from coverage terminology to evidence terminology.

Out of scope are changes to `refactor-design` behavior, semantic case fixtures, prompts, judges, oracles, archived canonical reports and their digests, the root `README.md`, `CODEX_CLI.md`, commits, pushes, publication, deployment, and every executor or judge model invocation.

Safety boundary: This task is limited to authorized, defensive maintenance of this repository. Do not provide offensive guidance or policy bypassing instructions.

## Definitions

An active evaluation is a case ID listed in a skill's current `evals/suite.json` with a corresponding current case directory. A historical evaluation is a case absent from the current suite but still referenced by an immutable archived report. Current suite evidence is evidence from canonical reports whose evaluated source fingerprint matches the current skill source fingerprint. Complete current suite evidence means every active suite case has a current non baseline passing observation. Partial current suite evidence means at least one, but not every, active suite case has such an observation. Historical runs means reports exist but none matches the current skill fingerprint.

The website generated model is the JSON compatible data assembled from canonical repository sources by `website/scripts/generate-content.mjs` and `website/scripts/evaluation-catalog.mjs`. `website/.generated/` is a disposable projection and must never be edited directly.

## Existing Context

`refactor-design/evals/suite.json` currently names twelve cases, including the deterministic `coverage-contract`. `refactor-design/evals/coverage.json` manually maps skill contracts and rubric families to semantic cases. The `coverage-contract` fixture validates the shape and fingerprints of that manifest, but it does not execute or qualify semantic skill behavior.

`website/scripts/evaluation-catalog.mjs` gives `coverage.json` special meaning by reading it, mapping contracts and rubric families into each active evaluation, and returning a skill level traceability summary. `website/scripts/generate-content.mjs` exposes those values in generated content and derives suite status through fields named `coveredCases` and `coveredCaseCount`. `website/.vitepress/theme/EvidenceStatus.vue`, `EvaluationHelp.vue`, `custom.css`, and `website/scripts/evaluation-glossary.mjs` present coverage specific labels, help, and layout. `website/tests/site-content.test.mjs` and `website/e2e/site.spec.mjs` protect the current behavior.

Canonical reports under `evaluation-reports/` contain past observations and verified digests. They are inputs to historical pages and must remain byte unchanged. Once the manifest and case directory are deleted, the current `refactor-design` tree fingerprint no longer matches those reports, so the website must truthfully show Historical runs rather than infer current proof.

## Desired End State

`refactor-design/evals/suite.json` contains the eleven existing semantic cases and no deterministic coverage case. Neither the skill nor the website treats any `coverage.json` as configuration. The generated public model has no `skill.traceability`, `evaluation.coverage`, `evaluation.rubricCoverage`, or `evaluation.traceabilityDeclared`. Suite evidence uses `passingCases` and `passingCaseCount`; visible labels say Complete current suite evidence, Partial current suite evidence, and Suite evidence.

Active pages contain no traceability summary, coverage level, mapping label, contract mapping, or rubric family mapping UI. Archived reports and digests remain unchanged, and any retired `coverage-contract` observation remains discoverable only as historical evidence. The current `refactor-design` catalog has eleven active evaluations and historical current evidence.

## Milestones

### Milestone 1 - Specify removal through website behavior

#### Goal

Express the new public catalog and page behavior in `website/tests/site-content.test.mjs` before implementation.

#### Changes

- [x] Update fixture behavior tests so a present `evals/coverage.json` has no special meaning.
- [x] Assert removal of public traceability and coverage mapping fields from the generated model.
- [x] Assert active pages omit traceability, coverage level, mapping label, contract, and rubric mapping presentation.
- [x] Assert evidence data uses `passingCases` and `passingCaseCount`, with complete and partial current suite evidence labels.
- [x] Assert `refactor-design` exposes eleven active evaluations, retains `coverage-contract` only as historical, and has Historical runs for current evidence.
- [x] No canonical documentation changes occur during RED because behavior has not changed yet.

#### Validation

- [x] Command: `cd /home/renanfranca/.codex/skills/website && npm test`
- [x] Expected result: the full website unit suite fails only on assertions that describe the removed mechanism and new names.

#### Acceptance Criteria

- [x] The failure proves current production code still interprets the manifest or emits retired fields and labels.
- [x] Tests observe generated public content and data rather than internal helper topology.

### Milestone 2 - Remove the mechanism and restore GREEN

#### Goal

Make executable suites, report fingerprints, and report observations the only inputs to evaluation evidence and public pages.

#### Changes

- [x] Delete `refactor-design/evals/coverage.json` and `refactor-design/evals/cases/coverage-contract/`.
- [x] Remove `coverage-contract` from `refactor-design/evals/suite.json` without changing any semantic case.
- [x] Remove coverage loading, mapping, and traceability summaries from `website/scripts/evaluation-catalog.mjs`.
- [x] Remove the retired generated model fields, rendering functions, page sections, and evidence terminology from `website/scripts/generate-content.mjs`.
- [x] Rename evidence state labels and model fields in the website code and update `EvidenceStatus.vue` to present Suite evidence.
- [x] Remove glossary entries, contextual help paths, CSS, and component branches used only by the retired mechanism.
- [x] Update desktop and Pixel 7 end to end assertions for the new labels, absence of retired UI, historical only case visibility, keyboard focus, and overflow.
- [x] Do not edit `website/.generated/`; regenerate it only through declared npm commands.

#### Validation

- [x] Command: `cd /home/renanfranca/.codex/skills/website && npm test`
- [x] Expected result: all website unit tests pass.
- [x] Command: `cd /home/renanfranca/.codex/skills/website && npm run test:e2e`
- [x] Expected result: desktop and Pixel 7 journeys pass, including accessibility and overflow assertions.

#### Acceptance Criteria

- [x] A fixture `coverage.json` is ignored and contributes no fields or UI.
- [x] The public model and pages contain only current suite evidence terminology.
- [x] `refactor-design` has eleven active cases and `coverage-contract` appears only through archived operations.

### Milestone 3 - Review design and reconcile documentation

#### Goal

Remove dead structure after GREEN, preserve observable behavior, and align canonical documentation with the simpler evidence model.

#### Changes

- [x] Run the scoped `refactor-design` review after unit and public checkpoints are green, using the existing behavior tests as protection.
- [x] Update `EVALUATIONS.md` by removing its coverage contract section, table of contents item, current explanations of `coverage.json`, contract and rubric mappings, guarantee levels, dimensions, and the obsolete deterministic case. Describe the current eleven case `refactor-design` suite and previous reports only as history.
- [x] Update `website/README.md` to document only factual evidence derivation from suite membership, fingerprints, and archived reports.
- [x] Record concrete no change justifications for the root `README.md`, `CODEX_CLI.md`, archived reports, and semantic skill files.

#### Validation

- [x] Command: `cd /home/renanfranca/.codex/skills/website && npm test`
- [x] Expected result: all website unit tests remain green after refactoring and documentation reconciliation.
- [x] Command: `cd /home/renanfranca/.codex/skills/website && npm run test:e2e`
- [x] Expected result: the public checkpoint remains green.

#### Acceptance Criteria

- [x] No dead coverage only component, glossary, rendering, or style code remains.
- [x] Canonical documentation explains the final observable behavior without defending or replacing manual traceability.

### Milestone 4 - Complete deterministic validation

#### Goal

Verify structure, formatting, build output, browser behavior, absence of active references, and clean patches without any model session.

#### Changes

- [x] No new behavior is introduced; repair only defects exposed by the declared gates.
- [x] Update this ExecPlan with exact results, decisions, risks, and lessons.

#### Validation

- [x] Command: `cd /home/renanfranca/.codex/skills && python3 -B .system/skill-creator/scripts/quick_validate.py ./refactor-design`
- [x] Command: `cd /home/renanfranca/.codex/skills/website && npm test`
- [x] Command: `cd /home/renanfranca/.codex/skills/website && npm run prettier:check`
- [x] Command: `cd /home/renanfranca/.codex/skills/website && npm run build`
- [x] Command: `cd /home/renanfranca/.codex/skills/website && npm run test:e2e`
- [x] Command: `cd /home/renanfranca/.codex/skills && rg -n --hidden --glob '!evaluation-reports/**' --glob '!_temporary/**' '<retired mechanism patterns>' .`
- [x] Command: `cd /home/renanfranca/.codex/skills && git diff --check`
- [x] Command: `cd /home/renanfranca/.codex/skills/_temporary/codex-skills-ai-context && git diff --check`
- [x] Expected result: every command succeeds; the reference audit finds no active mechanism outside allowed historical and planning locations.

#### Acceptance Criteria

- [x] Every required deterministic and public gate is green in the declared order.
- [x] No executor or judge session was invoked.
- [x] Archived reports and their digests are unchanged.

## Progress

- [x] Repository and nested website instructions read; workflow profile confirmed.
- [x] Memory worktree and exact origin validated before plan creation.
- [x] Required skill and evaluation contract instructions read.
- [x] Milestone 1 started.
- [x] Milestone 1 completed with expected RED: `npm test` ran 37 tests and failed only the new manifest ignorance assertion because `skill.traceability` was still present.
- [x] Milestone 2 started.
- [x] Milestone 2 completed with unit and public GREEN: `npm test` passed 37 tests; `npm run test:e2e` passed 31 tests across desktop and Pixel 7 with 1 expected skip.
- [x] Milestone 3 started.
- [x] Milestone 3 completed with a no action design review and documentation reconciliation in `EVALUATIONS.md` and `website/README.md`.
- [x] Milestone 4 started.
- [x] Milestone 4 completed.

## Decisions

- Decision: Classify the change as deterministic and authorize zero model sessions.
  Rationale: Every requested behavior is observable through repository files, generated data, unit tests, browser tests, fingerprints, and archived reports. The user explicitly prohibited executor and judge runs.
  Date/Author: 2026-08-01 / Codex

- Decision: Treat `coverage.json` as an ordinary ignored file rather than reject it.
  Rationale: The required behavior says a present fixture file has no special meaning. Ignoring it demonstrates complete removal without creating a replacement prohibition or schema contract.
  Date/Author: 2026-08-01 / Codex

- Decision: Preserve archived reports byte for byte and derive the retired case only from their observations.
  Rationale: Reports are canonical evidence with verified digests. Rewriting them would destroy auditability, while the existing historical catalog path can preserve visibility without keeping the case active.
  Date/Author: 2026-08-01 / Codex

- Decision: Observe manifest removal through generated model and Markdown pages in the existing site content suite.
  Rationale: These are stable public outputs. The expected RED failed at the first removed model field, proving that production still assigned special meaning to `coverage.json` without coupling the test to parser helpers.
  Date/Author: 2026-08-01 / Codex

- Decision: Retain the current evidence derivation structure after the post GREEN design review.
  Rationale: The scoped rubric review found no temporal coupling, hidden state, duplicated transformation, fragile mapping, or independent policy that justified a behavior preserving refactor. The change already removes the obsolete responsibilities; further extraction would add indirection without reducing risk.
  Date/Author: 2026-08-01 / Codex

- Decision: Regenerate only `evaluation-flow-desktop-linux.png`.
  Rationale: The snapshot had been regenerated in the commit that introduced traceability UI. Removing that preceding page content changed only subpixel text rasterization in the downstream flow capture while preserving its 672 by 293 dimensions, stages, focus outline, and layout. The targeted regeneration passed, followed by a green full desktop and Pixel 7 checkpoint.
  Date/Author: 2026-08-01 / Codex

- Decision: Leave the root `README.md`, `CODEX_CLI.md`, semantic skill files, and archived reports unchanged.
  Rationale: The root README contains no retired evaluation metadata contract; the CLI guide uses contract in the general TDD and prompt sense; semantic skill behavior and case criteria are explicitly out of scope; archived reports and digests are immutable historical facts. Searches after documentation reconciliation confirmed that neither canonical document being left untouched describes the removed mechanism.
  Date/Author: 2026-08-01 / Codex

## Risks and Mitigations

- Risk: Renaming evidence fields can leave stale consumers in Vue components, generated pages, or tests.
  Mitigation: Search all repository references, assert absence of old public fields, run the full unit suite each TDD cycle, and run the browser checkpoint.

- Risk: Removing `coverage-contract` could accidentally erase its historical page or archive references.
  Mitigation: Leave `evaluation-reports/` untouched and test that the case is absent from active evaluations but present in historical evaluations and operation history.

- Risk: Generated output or snapshots may be modified accidentally.
  Mitigation: Never patch `website/.generated/`; use npm commands to regenerate disposable content. Update visual snapshots only if an intentionally covered region changes and inspect such diffs before retaining them.

- Risk: Broad text searches can report unrelated technical uses of words such as contract, dimension, or guarantee.
  Mitigation: Audit retired identifiers and phrases precisely, then inspect any remaining context. Do not remove legitimate domain language from unrelated skills, runner contracts, or semantic fixtures.

- Risk: The current fingerprint will invalidate all current `refactor-design` report compatibility.
  Mitigation: This is an accepted outcome. Assert Historical runs and document that archived observations remain available without being claimed as current evidence.

## Validation Strategy

Use behavior first website tests for RED and GREEN. Run the entire `npm test` suite for every cycle and `npm run test:e2e` as the public checkpoint at milestone completion. After GREEN, use the scoped design review rubric, reconcile canonical documentation, and execute final validation exactly in this order: skill structural validation, website unit tests, Prettier check, production build, and browser tests. Finish with a precise reference audit and `git diff --check` in both worktrees. No model backed evaluation, forward test, executor, or judge is appropriate or authorized for this deterministic removal.

## Documentation Impact

`EVALUATIONS.md` is canonical for the evaluation system and must remove the coverage manifest section and all present tense explanations of the retired mechanism. Its `refactor-design` example must describe eleven current executable semantic cases and archived reports as historical observations only.

`website/README.md` is canonical for website generation and must remove the traceability projection contract. It must explain suite evidence only through active suite membership, current fingerprints, passing report observations, and historical reports.

The root `README.md` remains unchanged because it lists skills and broad repository entry points but does not document `coverage.json`, coverage mappings, traceability fields, or the website evidence calculation. `CODEX_CLI.md` remains unchanged because its use of public contracts describes prompt and TDD semantics, not the retired evaluation coverage manifest. Both will be rechecked after implementation.

Archived files under `evaluation-reports/` remain unchanged because they are canonical, digest protected historical evidence. Their old `coverage-contract` observations are historical facts, not active documentation or configuration. `refactor-design/SKILL.md`, its two rubric references, semantic fixtures, prompts, oracles, and judge criteria remain unchanged because the requested refactor removes only a manual evaluation metadata mechanism and explicitly preserves skill behavior.

## Rollout and Recovery

There is no deployment in this task. The repository diff is the rollout artifact and remains uncommitted. Recovery is a normal patch reversal of the source, test, suite, and documentation changes; archived evidence requires no recovery because it is never modified. If a required validation fails, return to the relevant TDD or documentation gate, record the failure here, correct the smallest in scope issue, and rerun the complete final sequence.

## Lessons Learned

- The retired deterministic case validated only a manually maintained manifest; it did not provide semantic qualification. Removing both together avoids preserving an orphan checker or implying that traceability itself is execution evidence.
- The first RED was narrow and expected: 36 tests passed and only the new public model assertion failed because the current catalog still emitted `skill.traceability`.
- The post GREEN design review found no actionable structural risk in the changed website data flow. Removal, rather than a replacement abstraction, is the simplest representation of the requested behavior.
- The flow screenshot changed by 297 pixels only in antialiased text after removal of preceding traceability content. Its dimensions, structure, focus behavior, and overflow behavior remained unchanged.
- Final validation on 2026-08-01 completed with `quick_validate.py` reporting `Skill is valid!`, all 37 Node tests passing, all files matching Prettier, the VitePress build completing after validation of 53 archived reports and one comparison, and Playwright reporting 31 passes with one expected skip across desktop Chromium and Pixel 7.
- The final source audit found no retired mechanism identifiers in active production or canonical documentation after excluding archived reports, the living plan, disposable generated pages, and tests that assert absence or historical visibility. `refactor-design/evals/suite.json` contains exactly eleven active IDs and excludes the retired case.
- `git diff --name-only` confirmed no changes under `evaluation-reports/`, the root `README.md`, `CODEX_CLI.md`, `refactor-design/SKILL.md`, or its rubric references. No executor or judge session was invoked.
