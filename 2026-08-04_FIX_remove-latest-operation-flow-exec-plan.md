# Remove latest operation flow

This ExecPlan is a living document. Keep `Progress`, `Decisions`, `Risks and Mitigations`, and `Lessons Learned` up to date as work advances.

## Purpose / Big Picture

Remove the "Latest operation flow" section from active evaluation pages, including `refactor-design`'s `hidden-invocation-state` page. Readers will retain the current evaluation definition and complete operation history, without the redundant latest-operation summary.

## Scope

In scope: the canonical evaluation-page generator, its behavior test, its now-unused presentation CSS, and the website documentation that describes the page layout. Out of scope: generated files, archived reports, operation history, deployment, commits, and other evaluation-page redesigns.

Safety boundary: This task is limited to authorized, defensive maintenance of this repository.

## Definitions

An active evaluation page is a generated route for a case still declared in the current suite. The latest operation flow is the generated summary of the newest archived runner invocation for that case. Operation history is the separate, complete list of archived invocations.

## Existing Context

`website/scripts/generate-content.mjs` renders active evaluation pages. It currently calls `renderLatestOperationFlow(evaluation)` below the "Latest operation flow" heading and above operation history. `website/tests/site-content.test.mjs` verifies that the generated evaluation page includes that heading and flow markup. `website/README.md` describes the page layout as including latest execution.

## Desired End State

Active evaluation pages contain no "Latest operation flow" heading, explanatory text, flow markup, or related summary. They continue to render operation history from the same archived data. The canonical generator and tests express that public behavior; `.generated/` remains unedited.

## Milestones

### Milestone 1 - Remove the redundant latest-operation summary

#### Goal

Make the generated page omit the latest-operation flow while preserving operation history.

#### Changes

- [ ] Update `website/tests/site-content.test.mjs` with a behavior assertion that an active evaluation page does not expose the latest-operation flow and still exposes its operation history.
- [ ] Update `website/scripts/generate-content.mjs` to remove the latest-operation flow from active evaluation page output and delete its unused renderer.
- [ ] Remove the now-unused `.operation-flow` CSS from `website/.vitepress/theme/custom.css`.
- [ ] Update `website/README.md` to describe the remaining page layout accurately.
- [ ] Do not edit `website/.generated/`; it is a disposable projection.

#### Validation

- [ ] Command: `npm test`
- [ ] Expected result: the full website unit suite first fails after the changed expectation and then passes after the generator change.
- [ ] Command: `npm run test:e2e`
- [ ] Expected result: the built public site completes its Playwright checkpoint.
- [ ] Commands, in order: `npm test`, `npm run prettier:check`, `npm run build`, `npm run test:e2e`
- [ ] Expected result: all final validations pass.

#### Acceptance Criteria

- [ ] Generated active evaluation Markdown contains no latest-operation flow section or markup.
- [ ] Generated active evaluation Markdown retains operation history.
- [ ] The published page path is covered through the Playwright checkpoint.

### Milestone 2 - Align the public checkpoint

#### Goal

Replace the obsolete Playwright assertion for the removed latest-operation panel with assertions for the retained operation history.

#### Changes

- [ ] Update `website/e2e/site.spec.mjs` so the evaluation-page journey does not look for `.operation-summary` and instead verifies that `Latest operation flow` is absent and `Operation history` remains visible.
- [ ] Do not modify the generator or restore the removed panel; the CI failure is an obsolete consumer expectation.

#### Validation

- [ ] Command: `npm run test:e2e`
- [ ] Expected result: the desktop and mobile variants of the evaluation-page journey pass without the removed panel.
- [ ] Commands, in order: `npm test`, `npm run prettier:check`, `npm run build`, `npm run test:e2e`
- [ ] Expected result: all final validations pass.

#### Acceptance Criteria

- [ ] Playwright verifies the public page without the latest-operation flow.
- [ ] Playwright still verifies that operation history is available.

## Progress

- [x] Applicable repository and website instructions read; memory worktree and origin verified.
- [x] Milestone 1 started.
- [x] Added the next behavior assertion for omission of the latest-operation flow.
- [x] Confirmed Node 24/npm 11 are installed in `Ubuntu-Seed4J` via NVM and load NVM explicitly for non-interactive commands.
- [x] Demonstrated RED: the new omission assertion failed while the latest-operation flow remained generated.
- [x] Removed the active-page flow renderer, generated section, and unused CSS; operation history is retained.
- [x] GREEN: `npm test` passed (37 tests).
- [x] Public checkpoint: `npm run test:e2e` passed after building the site and running Playwright.
- [x] Milestone 1 completed.
- [x] Documentation reconciled.
- [x] Final validation completed: `npm test`, `npm run prettier:check`, `npm run build`, and `npm run test:e2e` all passed.
- [x] CI failure inspected: GitHub Actions run 30920999831 shows an obsolete Playwright locator for the removed `.operation-summary` panel.
- [x] Milestone 2 started.
- [x] Replaced the obsolete panel assertions with absence of `Latest operation flow` and presence of `Operation history`.
- [x] Confirmed the targeted desktop and mobile Playwright scenario passes.
- [x] Milestone 2 completed.

## Decisions

- Decision: Remove the section for every active evaluation page rather than special-casing one route.
  Rationale: the named page is generated from the shared active-evaluation template; removing only one projection would be non-canonical and overwritten by regeneration.
  Date/Author: 2026-08-04 / Codex

## Risks and Mitigations

- Risk: historical or current evaluation evidence could be mistaken for the removed UI summary.
  Mitigation: leave the evaluation status panel, current definition, and operation history unchanged and assert the retained history in the behavior test.
- Risk: generated output could be changed directly and later overwritten.
  Mitigation: alter only the canonical generator and validate it through the website build.
- Risk: the required Node.js 24/npm 11.7.0 validation runtime is unavailable in this Linux workspace.
  Mitigation: the runtime is installed through NVM, but non-interactive shells return before loading `.bashrc`; source `$HOME/.nvm/nvm.sh` before each validation command.
- Risk: Node inherits a Windows temporary directory that is read-only in this sandbox.
  Mitigation: direct test temporary files to writable `/tmp` with `TMPDIR=/tmp`.
- Risk: the build reports pre-existing large JavaScript chunks and the Playwright dependency installation reports three audit findings.
  Mitigation: both are non-blocking warnings outside this narrow removal; no dependency or bundle change was authorized.
- Risk: local output from the Playwright container can be truncated before its final summary.
  Mitigation: use the GitHub Actions log and command exit status as the authoritative result, and retain the full public checkpoint in the final validation sequence.

## Validation Strategy

1. Change one public content assertion and run the full `npm test` suite to demonstrate RED.
2. Remove the generator output and rerun `npm test` for GREEN.
3. Run `npm run test:e2e` as the public checkpoint.
4. Inspect `website/README.md`, update its page-layout statement, then run the required final sequence: `npm test`, `npm run prettier:check`, `npm run build`, and `npm run test:e2e`. Source NVM and direct temporary files to `/tmp` for each command in this environment.

Validation evidence: on 2026-08-04, `npm test` passed 37 tests; `npm run prettier:check` reported every file formatted; `npm run build` validated the archive, generated 10 skills and 53 reports, and completed the VitePress build; and `npm run test:e2e` completed the packaged Playwright checkpoint. `git diff --check` passed, and the generated `hidden-invocation-state` page contains neither `Latest operation flow` nor `operation-flow`.

After the commit, GitHub Actions run 30920999831 failed its public checkpoint because `website/e2e/site.spec.mjs` still expected the removed `.operation-summary`. The next validation must re-run the exact final sequence after replacing that consumer expectation.

CI-fix validation evidence: `npm test` passed 37 tests, `npm run prettier:check` passed, `npm run build` passed, and the full `npm run test:e2e -- --reporter=dot` result was 32 passed and 2 skipped. The targeted failure scenario also passed in both desktop and mobile projects.

## Documentation Impact

`website/README.md` is the canonical documentation for this scope. Its statement that pages include latest execution will be changed to state that archived operations are available through operation history. No other canonical documentation source describes this page composition precisely enough to require an update. Generated pages are not documentation sources and will not be edited.

Milestone 2 updates only the test's consumer expectation; no canonical documentation changes are needed because the documented public behavior already describes operation history rather than latest execution.

## Rollout and Recovery

The existing GitHub Actions website deployment publishes the generated site after changes reach `main`; deployment is not part of this task. To recover, revert the canonical generator, behavior test, and README change together, then rerun the final validation sequence.

## Lessons Learned

- The active evaluation page is a generated projection, so page-level changes belong in `website/scripts/generate-content.mjs`, not in `website/.generated/`.
- `Ubuntu-Seed4J` already has Node 24.16.0 and npm 11.13.0 through NVM. Its `.bashrc` returns before NVM initialization in a non-interactive shell, so commands must source NVM explicitly or the profile must load it separately.
- The focused design review classified the only candidate as **No action**: deleting the unused renderer and its dedicated CSS leaves the operation-history contract independent and removes dead presentation code without a new abstraction.
- The original removal did not update the E2E journey that asserted the now-removed latest-operation summary; generated-content assertions alone did not cover that consumer contract.
