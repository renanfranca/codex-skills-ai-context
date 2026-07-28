# Build the Codex Skills evidence site

This ExecPlan is a living document. Keep `Progress`, `Decisions`, `Risks`, and `Lessons Learned` up to date as work advances.

## Purpose / Big Picture

Create a VitePress site under `website/`, published at `https://renanfranca.github.io/codex-skills/`, that explains the project, presents its skills, and turns archived evaluation reports into understandable evidence. A reader must be able to inspect what was evaluated, what happened, and which facts were recorded without running Codex CLI locally.

## Scope

The work includes an English, mobile-first VitePress site; a deterministic content generator based on existing `evaluation-reports/**/report.json` files; skill and evaluation pages; Prettier, Husky, and lint-staged integration with the repository root; unit and browser tests; and a GitHub Pages workflow.

The work excludes changes to skills, the evaluation runner, evaluation contracts, canonical reports, and the personal `renanfranca.github.io` repository. It also excludes new model sessions and inferred facts that are absent from archived evidence.

## Definitions

An evaluation archive is the canonical tree below `evaluation-reports/`. A normalized evaluation is a website-facing representation that preserves source facts and uses `Not recorded` for absent facts. Current promotion, current observations, historical evidence, and no archived evidence are editorial labels used to explain the relationship between a skill and the available reports. A public-path checkpoint exercises the built site with the configured `/codex-skills/` base path.

## Existing Context

The Seed4J `init` module was committed as `35d3933c`, the Prettier module as `634f761b`, and the npm dependency lock as `70918edb`. The nested `website/.git` directory has been removed, so `website/` now belongs to the repository rooted at `/home/renanfranca/.codex/skills`. `npm install` has been run, dependency versions were updated, and `website/package-lock.json` is committed.

The site package currently targets Node 24 and npm 11.7.0. Husky is not connected to the parent repository because `prepare` still runs `husky` from the nested package, `core.hooksPath` is unset, the hook does not enter `website/`, and lint-staged omits JavaScript, TypeScript, Vue, CJS, and MJS files.

The deleted nested Git history is no longer present in the workspace. Its effective file state is preserved by the outer repository commits, so no nested repository will be recreated.

## Desired End State

The public site provides `/codex-skills/`, `/codex-skills/skills/`, one page per skill, `/codex-skills/evaluations/`, and one page per archived evaluation path. Pages expose summaries first and expandable evidence details, including failed runs when present. Missing facts are shown as `Not recorded`.

`npm run dev` generates content and starts VitePress. `npm run build` validates the archive, regenerates deterministic content, and builds the static site. `npm test` runs behavior tests, and `npm run test:e2e` exercises the built public path.

Initial evidence labels are: `restructure-documentation` as current promotion, `refactor-design` as current observations, `develop-skill-with-evals` as historical evidence, and other active skills as no archived evidence.

## Milestones

### Milestone 1 - Connect website checks to the parent Git repository

#### Goal

Make the root repository invoke website formatting only for staged website files.

#### Changes

Edit `website/package.json` so `prepare` installs Husky from the parent Git root into `website/.husky`. Edit `website/.husky/pre-commit` to enter `website/` and invoke lint-staged through npm. Expand `website/.lintstagedrc.cjs` to cover JavaScript, TypeScript, Vue, CJS, and MJS while preserving existing formats. Add a behavior test that proves a staged website file is formatted while a staged root file is untouched.

#### Validation

Run `npm --prefix website run prepare`, `git config --get core.hooksPath`, `git -C website rev-parse --show-toplevel`, `npm --prefix website test`, and `npm --prefix website run prettier:check`.

#### Acceptance Criteria

`core.hooksPath` is `website/.husky/_`; the website resolves to the parent repository; the hook formats eligible staged website files; a root file is untouched; and the hook succeeds when no website file is staged.

### Milestone 2 - Define and implement the evidence content contract

#### Goal

Expose archived evidence through a deterministic, factual website data contract.

#### Changes

Add behavior tests for complete reports, failed reports, missing optional facts, multiple runs, skills without evidence, deterministic ordering, invalid archives, and unmodified diff or excerpt content. Implement the smallest TypeScript generator that validates the archive, discovers report JSON files, normalizes present facts, relates reports to skills, and writes generated VitePress content without changing canonical inputs.

#### Validation

Run `python3 -B develop-skill-with-evals/scripts/manage_evaluation_archive.py validate --archive evaluation-reports` and `npm --prefix website test`.

#### Acceptance Criteria

The same archive produces byte-identical generated output; pass and fail histories are retained; missing facts are explicit; invalid evidence stops the build; and canonical reports remain unchanged.

### Milestone 3 - Build the VitePress user experience

#### Goal

Let a reader move from the project purpose to skills and their evaluation evidence on desktop and mobile.

#### Changes

Add VitePress 1.6.4 and Vue 3, configure the `/codex-skills/` base, implement an editorial technical theme, and create the home, skill catalog, skill detail, evaluation catalog, and report detail experiences. Add search, GitHub source links, factual evidence labels, and expandable details.

#### Validation

Run `npm --prefix website test`, `npm --prefix website run build`, and `npm --prefix website run prettier:check`.

#### Acceptance Criteria

Every active top-level skill is discoverable; evidence labels match the documented mapping; failed evidence is visible; source links resolve; and the static build succeeds under the repository base path.

### Milestone 4 - Protect the public path and publish it

#### Goal

Verify the deployed user journey and automate GitHub Pages publication.

#### Changes

Add Playwright checks for navigation, successful and failed evidence, expandable details, no-evidence skills, mobile layout, internal links, GitHub links, and console errors. Add a GitHub Actions workflow using Node 24, archive validation, deterministic tests, build, public-path checkpoint, and Pages deployment from the main branch.

#### Validation

Run `npm --prefix website run test:e2e`, inspect the generated site at `/codex-skills/`, and validate the workflow syntax and build artifact path `website/.vitepress/dist`.

#### Acceptance Criteria

The public-path suite is green on desktop and mobile, assets load with the configured base path, and the workflow deploys only after every required check passes.

### Milestone 5 - Consolidate design and validate the repository

#### Goal

Remove structural risks without changing green behavior, then run final repository validation.

#### Changes

Load and follow `refactor-design`. Review hidden mutable state, mixed responsibilities, fragile report representations, parser and UI coupling, and archive layout leakage. Record meaningful refactors and rerun behavior checkpoints. If behavior is missing or wrong, return to `tdd-behavior-autonomous-quiet` before resuming design review.

#### Validation

Run archive validation, Prettier check, unit tests, VitePress build, Playwright, `git diff --check`, and `git status --short`.

#### Acceptance Criteria

All milestones and public paths are green; canonical skills and reports are unchanged; unrelated caches are untracked; and this ExecPlan records final validation evidence.

## Progress

- [x] Create `website/` with Seed4J
- [x] Apply the Seed4J init module
- [x] Apply the Seed4J Prettier module
- [x] Remove `website/.git`
- [x] Confirm `website/` belongs to the parent repository
- [x] Run `npm install`
- [x] Update package versions and commit `package-lock.json`
- [x] Create this ExecPlan at the required path
- [x] Complete Milestone 1
- [x] Complete Milestone 2
- [x] Complete Milestone 3
- [x] Complete Milestone 4
- [x] Complete Milestone 5

## Decisions

- Decision: Use VitePress rather than Nuxt.
  Rationale: The canonical content is documentation and static evidence, while Vue components remain available for richer presentation.
  Date/Author: 2026-07-28 / Renan Franca and Codex

- Decision: Keep the dependency versions already selected by the user and update the npm lock only through npm.
  Rationale: The installed and committed package baseline is intentional and reproducible.
  Date/Author: 2026-07-28 / Codex

- Decision: Treat archived reports as canonical and never infer missing facts.
  Rationale: The site must explain recorded evidence rather than manufacture a stronger claim.
  Date/Author: 2026-07-28 / Renan Franca and Codex

- Decision: Do not recreate a Git repository inside `website/`.
  Rationale: Website history and hooks must belong to the parent project.
  Date/Author: 2026-07-28 / Renan Franca

- Decision: Scope lint-staged by running it from `website/` and protect the boundary through a temporary-repository behavior test.
  Rationale: This proves formatting behavior without staging or rewriting unrelated repository files.
  Date/Author: 2026-07-28 / Codex

- Decision: Exclude the four compatibility TDD skills through `website/content-config.json`.
  Rationale: The repository README and local Codex configuration identify them as disabled, so the public catalog must contain the 9 active skills rather than every directory that retains a `SKILL.md`.
  Date/Author: 2026-07-28 / Codex

- Decision: Use the Chromium managed by Playwright with desktop and Pixel 7 projects and a maximum of two workers.
  Rationale: This provides complete Chromium mobile emulation, matches CI, and avoids dependence on or resource exhaustion from the system Chrome.
  Date/Author: 2026-07-28 / Renan Franca and Codex

- Decision: Copy public SVG assets into `.generated/public` during content generation.
  Rationale: VitePress 1.6.4 copies static assets from `<srcDir>/public`; the configured source directory is `.generated`.
  Date/Author: 2026-07-28 / Codex

- Decision: Keep the GitHub Pages base path in `website/content-config.json` and read it from both the generator and VitePress.
  Rationale: A duplicated base path could make generated links disagree with built asset URLs after a future repository rename.
  Date/Author: 2026-07-28 / Codex

## Risks and Mitigations

- Risk: Husky remains inactive because the npm package is below the Git root.
  Mitigation: Install hooks explicitly from the parent and verify `core.hooksPath`.

- Risk: lint-staged formats files outside the website.
  Mitigation: Run it with `website/` as its working directory and protect this boundary with a behavior test.

- Risk: Website prose turns interpretation into fact.
  Mitigation: Normalize only recorded values, explain editorial labels, and display `Not recorded` for absent values.

- Risk: Archive schema or layout evolves.
  Mitigation: Validate before generation, test observable output, and fail with a precise diagnostic.

- Risk: GitHub Pages breaks asset URLs.
  Mitigation: Configure `/codex-skills/` and exercise that exact public path with Playwright.

- Risk: VitePress 1.6.4 brings development-server advisories through Vite and esbuild with no compatible automated fix reported by npm.
  Mitigation: Keep the development server bound locally, deploy only static output, record the audit result, and reassess when a compatible VitePress release is selected.

- Risk: Evidence-rich report pages and the local search index produce bundle chunks above 500 kB.
  Mitigation: Keep the complete evidence available for the first release, record the build warning, and treat search or evidence lazy loading as a measured follow-up rather than hiding evidence now.

- Risk: Local caches enter the change.
  Mitigation: Never stage `_temporary/`, `__pycache__`, `node_modules`, or generated artifacts that are not intentional site sources.

## Validation Strategy

Validation proceeds from focused behavior tests through archive generation and build, then through the browser-visible public path. Final commands are:

    python3 -B develop-skill-with-evals/scripts/manage_evaluation_archive.py validate --archive evaluation-reports
    npm --prefix website run prettier:check
    npm --prefix website test
    npm --prefix website run build
    npm --prefix website run test:e2e
    git diff --check
    git status --short

Final results on 2026-07-28:

- `python3 -m unittest discover -s develop-skill-with-evals/scripts/tests -v`: 82 tests passed.
- Archive validation: PASS with 44 reports, 1 comparison, and the dated pricing file.
- Website behavior suite: 7 tests passed.
- Prettier check: PASS.
- VitePress build: PASS with 9 active skills and 44 reports.
- Playwright public path: 10 tests passed across desktop Chromium and the Pixel 7 profile.
- The browser journey confirms that retained code fragments render as distinct `Before` and `After` evidence.
- `git diff --check`: PASS.
- `core.hooksPath`: `website/.husky/_`.
- Canonical skills and evaluation reports have no diff.
- Build warning retained: evidence-heavy chunks exceed 500 kB.
- npm audit retained: 2 moderate and 1 high development-server advisories inherited through VitePress 1.6.4, with no compatible automated fix reported.

## Rollout and Recovery

GitHub Pages deploys only from the main branch after all checks pass. A failed workflow leaves the previous Pages deployment available. Recovery is a normal revert or a generator fix followed by the same workflow; canonical evaluation evidence is not rewritten during rollout or recovery.

## Lessons Learned

- A nested npm package needs an explicit Husky path when the Git root is its parent.
- Removing `website/.git` resolved accidental repository separation, while the outer commits preserved the effective website state.
- Human-readable pages must remain projections of canonical evidence rather than new sources of truth.
- Husky reports success even when the sandbox prevents writing `.git/config`; final validation must assert the resulting `core.hooksPath`, not rely only on the lifecycle command exit code.
- The initial Seed4J Prettier commit still contained files that failed its own `prettier:check`; Milestone 1 normalized those files and established a green formatter checkpoint.
- VitePress 1.6.4 resolves public assets from `<srcDir>/public` even when a separate `publicDir` is configured, so generated-source sites must place static assets below the generated source tree.
- Playwright's `iPhone 13` descriptor selects WebKit by default. A fully emulated Chromium mobile checkpoint should use a Chromium profile such as `Pixel 7`.
- The public checkpoint is green with 7 deterministic tests and 10 browser journeys across desktop and Pixel 7. The archive validator reports 44 reports and 1 comparison, and generation reports 9 active skills.
- The post-green design review classified the duplicated GitHub Pages base path as a design risk and centralized it without changing behavior. Splitting the generator and optimizing evidence chunks were classified as maintainability opportunities with no justified refactor in this scope.
- The first final-review pass found that archived fragment metadata was normalized but not rendered. Execution returned to behavior TDD before final completion, as required by the workflow.
- The restored behavior checkpoint added fragment rendering without weakening archive escaping: generated evidence remains inert text, and both deterministic and browser suites are green.
- Final repository validation completed after post-green design consolidation on 2026-07-28.
