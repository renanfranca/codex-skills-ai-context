# Build the Codex Skills evidence site

This ExecPlan is a living document. Keep `Progress`, `Decisions`, `Risks`, and `Lessons Learned` up to date as work advances.

Status: Implementation complete locally. The original site was published by 2026-07-29. Milestone 6 made visual snapshot generation and comparison hermetic on 2026-07-30. Rollout verification remains intentionally pending until the user pushes the change.

## Purpose / Big Picture

Create a VitePress site under `website/`, published at `https://renanfranca.github.io/codex-skills/`, that explains the project, presents its skills, and turns archived evaluation reports into understandable evidence. A reader must be able to inspect what was evaluated, what happened, and which facts were recorded without running Codex CLI locally.

## Scope

The work includes an English, mobile-first VitePress site; a deterministic content generator based on existing `evaluation-reports/**/report.json` files; skill and evaluation pages; Prettier, Husky, and lint-staged integration with the repository root; unit and browser tests; and a GitHub Pages workflow.

The work excludes changes to skills, the evaluation runner, evaluation contracts, canonical reports, and the personal `renanfranca.github.io` repository. It also excludes new model sessions and inferred facts that are absent from archived evidence. Milestone 6 adds only the website visual-test runner, its deterministic environment contract, the Pages build environment, regenerated existing snapshots, and contributor documentation. It does not add screenshot tolerance, new screenshot files, a commit, a push, or a deployment.

## Definitions

An evaluation archive is the canonical tree below `evaluation-reports/`. A normalized evaluation is a website-facing representation that preserves source facts and uses `Not recorded` for absent facts. Current promotion, current observations, historical evidence, and no archived evidence are editorial labels used to explain the relationship between a skill and the available reports. A public-path checkpoint exercises the built site with the configured `/codex-skills/` base path.

## Existing Context

The Seed4J `init` module was committed as `35d3933c`, the Prettier module as `634f761b`, and the npm dependency lock as `70918edb`. The nested `website/.git` directory has been removed, so `website/` now belongs to the repository rooted at `/home/renanfranca/.codex/skills`. `npm install` has been run, dependency versions were updated, and `website/package-lock.json` is committed.

The site package currently targets Node 24 and npm 11.7.0. Husky is not connected to the parent repository because `prepare` still runs `husky` from the nested package, `core.hooksPath` is unset, the hook does not enter `website/`, and lint-staged omits JavaScript, TypeScript, Vue, CJS, and MJS files.

The deleted nested Git history is no longer present in the workspace. Its effective file state is preserved by the outer repository commits, so no nested repository will be recreated.

## Desired End State

The public site provides `/codex-skills/`, `/codex-skills/skills/`, one page per skill, `/codex-skills/evaluations/`, and one page per archived evaluation path. Pages expose summaries first and expandable evidence details, including failed runs when present. Missing facts are shown as `Not recorded`.

`npm run dev` generates content and starts VitePress. `npm run build` validates the archive, regenerates deterministic content, and builds the static site. `npm test` runs behavior tests. `npm run test:e2e` builds on the host and runs the browser checkpoint in one digest-pinned Playwright container; `npm run test:e2e:direct` is the internal command used only when execution is already inside that container.

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

### Milestone 6 - Make visual snapshots hermetic

#### Goal

Generate and compare all Playwright screenshots in one reproducible Linux environment so local baselines and GitHub Actions use the same fonts, browser, operating system, and Playwright version without weakening exact visual comparison.

#### Changes

Add a deterministic behavior test under `website/tests/` that reads the public package contract, local container runner, and Pages workflow and rejects any version or image divergence. Add a local runner under `website/scripts/` that builds the site on the host, then runs Playwright with forwarded arguments in `mcr.microsoft.com/playwright:v1.62.0-jammy@sha256:b012874f829d298730411256666afcaeaeebaf505a0cf4c2f668d6dedb3d1e80`, `--init`, `--ipc=host`, the local user, the repository mounted at the same absolute path, and an isolated volume over `website/node_modules`. Make `npm run test:e2e` the containerized public command and retain `test:e2e:direct` only for execution already in the container.

Run the GitHub Actions `build` job in the same digest-pinned image, install Node 24 and Python explicitly, remove redundant browser installation, and call the direct E2E command. Remove `maxDiffPixelRatio: 0.01` from both page screenshots. Regenerate exactly the eight tracked PNG baselines in the container, inspect changes before retaining them, and update `website/README.md` with Docker as the visual-checkpoint prerequisite and the containerized baseline-update command. Do not edit `website/.generated/` directly.

The failure evidence is GitHub Actions run `30561602385` at commit `d0147bac64ac692da388f47714032e198bfc473f`. Its `build` job passed dependency installation and deterministic tests, then failed only the mobile `evaluation-vocabulary-guide.png` snapshot with 4,638 changed pixels, ratio 0.02, identically on the initial attempt and both retries. Artifact `playwright-test-results-30561602385-1`, digest `sha256:4c2775d6a2e70739106cbbfde35927773a8cf8c926d9fb362281c61ebc17c0e8`, contains equal 412 by 839 expected and actual images plus a diff concentrated on glyph edges. The three actual images are byte-identical, which identifies a stable cross-environment font-rasterization difference rather than an intermittent browser failure.

#### Validation

First run `npm test` after adding only the synchronization test and require the expected RED caused by the current unpinned direct runner and uncontainerized workflow. After implementation, run `npm test` for GREEN. Run `npm run test:e2e -- --update-snapshots`, confirm the tracked snapshot file set remains exactly eight PNG files, and inspect every changed baseline. Then run `npm run test:e2e` three consecutive times and require all exact comparisons to pass.

After the public checkpoint is green, load and follow `refactor-design`, reconcile `website/README.md`, `.github/workflows/deploy-website.yml`, `website/package.json`, and the root documentation, then run final validation from `website/` in this exact order:

    npm test
    npm run prettier:check
    npm run build
    npm run test:e2e

Finally run `git diff --check` in `/home/renanfranca/.codex/skills` and `git diff --check` in `_temporary/codex-skills-ai-context`.

#### Acceptance Criteria

The package, runner, and workflow name Playwright 1.62.0 and the same digest-pinned Jammy image; future divergence fails deterministic tests. The public E2E command requires Docker and forwards Playwright arguments. CI uses the direct command inside the same image and no longer downloads Chromium. Both page screenshots use exact comparison. Exactly eight snapshot files remain, three consecutive container runs pass, canonical documentation matches the commands, and all final validation commands are green.

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
- [x] Enable GitHub Pages with `Settings → Pages → Source: GitHub Actions`
- [x] Confirm the production site returns HTTP 200
- [x] Finalize this ExecPlan on 2026-07-29
- [x] Inspect GitHub Actions run `30561602385` and its Playwright artifact
- [x] Resolve the official multi-platform image digest for Playwright 1.62.0 Jammy
- [x] Start Milestone 6
- [x] Demonstrate synchronization-contract RED
- [x] Implement the hermetic local and CI visual environment
- [x] Regenerate and inspect exactly eight existing snapshots
- [x] Pass the exact visual suite three consecutive times
- [x] Complete post-GREEN design review
- [x] Reconcile canonical documentation
- [x] Complete Milestone 6 final validation
- [x] Complete Milestone 6 locally
- [ ] After a user push, verify that the new workflow run completes `build`, uploads the Pages artifact, and executes `deploy`

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

- Decision: Enable GitHub Pages manually once through the repository settings rather than granting an administrative token to the deployment workflow.
  Rationale: The first workflow run failed because no Pages site existed. Selecting `GitHub Actions` as the publishing source created the required Pages configuration without storing a privileged personal access token.
  Date/Author: 2026-07-29 / Renan Franca and Codex

- Decision: Pin the official Playwright 1.62.0 Jammy multi-platform image by tag and index digest in both local and CI execution.
  Rationale: Playwright recommends generating and comparing screenshots in the same environment. The tag communicates the package compatibility and the digest prevents the operating system, browsers, fonts, or architecture manifests from changing under that tag.
  Date/Author: 2026-07-30 / Renan Franca and Codex

- Decision: Keep page screenshots exact instead of raising `maxDiffPixelRatio`.
  Rationale: Run `30561602385` produced the same 2% glyph-edge difference on every retry, so tolerance would hide a stable environment mismatch rather than remove its cause.
  Date/Author: 2026-07-30 / Renan Franca and Codex

- Decision: Retain the image reference in the local runner and workflow rather than introducing another configuration format.
  Rationale: GitHub Actions cannot directly consume a shell variable for the job container image. The deterministic synchronization test makes the necessary boundary duplication executable and also verifies that the image tag matches the package version.
  Date/Author: 2026-07-30 / Codex

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

- Risk: A repository without Pages enabled returns `Get Pages site failed` from `actions/configure-pages`.
  Mitigation: GitHub Pages was enabled once through `Settings → Pages → Source: GitHub Actions`, the workflow then succeeded, and the production URL was confirmed with HTTP 200.

- Risk: The current `configure-pages@v5` and `deploy-pages@v4` releases emit a Node 20 deprecation warning even though the GitHub runner uses Node 24.
  Mitigation: The warning does not block the successful deployment. Updating the Pages actions to their Node 24 releases is recorded as future workflow maintenance outside this completed feature plan.

- Risk: VitePress 1.6.4 brings development-server advisories through Vite and esbuild with no compatible automated fix reported by npm.
  Mitigation: Keep the development server bound locally, deploy only static output, record the audit result, and reassess when a compatible VitePress release is selected.

- Risk: Evidence-rich report pages and the local search index produce bundle chunks above 500 kB.
  Mitigation: Keep the complete evidence available for the first release, record the build warning, and treat search or evidence lazy loading as a measured follow-up rather than hiding evidence now.

- Risk: Local caches enter the change.
  Mitigation: Never stage `_temporary/`, `__pycache__`, `node_modules`, or generated artifacts that are not intentional site sources.

- Risk: A bind-mounted host `node_modules` contains binaries incompatible with the container or lets host dependency state leak into visual tests.
  Mitigation: Mount a dedicated Docker volume over `website/node_modules` and run `npm ci` inside the container before the direct Playwright command.

- Risk: The pinned container defaults do not match the repository's Node 24 and Python requirements.
  Mitigation: Configure Node 24 and Python explicitly in the CI job, while the local runner builds the evidence-backed site on the host and uses the container only for browser execution.

- Risk: Running the container as the local user cannot write to a newly created Docker volume.
  Mitigation: Make the local runner initialize the isolated dependency volume with ownership suitable for the invoking user before running `npm ci`.

- Risk: A future Playwright dependency update silently retains the old browser image or snapshots.
  Mitigation: A deterministic test compares the exact package version, local image reference, and workflow image reference.

- Risk: CSS smooth scrolling leaves the page behind a mobile sheet at a different position when the screenshot is captured.
  Mitigation: The visual journey disables smooth scrolling before its interactions, then selects and verifies the document's absolute top with an instant scroll and two rendered frames. Unlike the lower boundary, the top does not depend on total rendered document height. The sheet content, overflow, keyboard, geometry, and semantic assertions remain unchanged.

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

Production validation on 2026-07-29:

- The first Pages configuration attempt failed with `Get Pages site failed` because the repository did not yet have GitHub Pages enabled.
- Renan Franca selected `GitHub Actions` under `Settings → Pages → Source`.
- The same deployment path then completed successfully without adding `enablement: true` or an administrative token.
- `curl -fsSL -o /dev/null -w '%{http_code} %{url_effective}\n' https://renanfranca.github.io/codex-skills/`: `200 https://renanfranca.github.io/codex-skills/`.

Milestone 6 validation evidence will be appended here as each gate completes. Deployment verification is intentionally deferred until the user pushes the completed change; no agent commit, push, publication, or deployment is authorized.

The synchronization contract RED was demonstrated on 2026-07-30 with `npm test`: 24 existing tests passed and the new visual-environment test failed because the public container runner did not exist. This is the expected missing-behavior failure before implementation.

The containerized update exercised all eight existing snapshot assertions without adding files. The final inspected set still contains exactly eight PNG files. Six remained byte-identical; `evaluation-vocabulary-guide-mobile-linux.png` and `execution-facts-help-desktop-linux.png` changed after the visual journey was placed in an explicit stationary scroll state. With those final baselines, three consecutive exact `npm run test:e2e` executions each passed 27 tests with the one expected project-specific skip.

Final validation on 2026-07-30 passed in the required order:

- `npm test`: 25 tests passed.
- `npm run prettier:check`: all matched files use Prettier formatting.
- `npm run build`: archive validation passed with 53 reports and 1 comparison; generation produced 10 skills and 53 reports; VitePress built successfully with the existing large-chunk warning.
- `npm run test:e2e`: 27 tests passed with 1 expected project-specific skip in the digest-pinned container.
- `git diff --check` in `/home/renanfranca/.codex/skills`: passed.
- `git diff --check` in `_temporary/codex-skills-ai-context`: passed.
- `bash -n website/scripts/run-playwright-container.sh`: passed.

## Documentation Impact

`website/README.md` is the canonical contributor guide for website prerequisites and commands. It now requires Docker for the browser checkpoint, removes the obsolete host Chromium installation, documents the digest-pinned container behavior, gives the public baseline-update command, and restricts `test:e2e:direct` to execution already inside the exact container.

`website/package.json` is the canonical public command configuration. It exposes the container runner as `test:e2e` and the direct Playwright command as `test:e2e:direct`. `.github/workflows/deploy-website.yml` is the canonical publication configuration. It pins the build job to the same image, configures Node 24 and Python 3.13, builds once, and invokes the direct checkpoint without installing Chromium.

The root `README.md` remains accurate without change because its website section only explains how to start Codex in the nested workflow scope; it does not document browser prerequisites or E2E commands. `CODEX_CLI.md`, `EVALUATIONS.md`, and `website/content-config.json` do not describe visual testing or CI runtime and therefore remain unchanged. `website/.generated/` remains a disposable projection and was regenerated only through repository commands, never edited directly.

## Rollout and Recovery

GitHub Pages was enabled with GitHub Actions as its publishing source and the production site is available at `https://renanfranca.github.io/codex-skills/`. Future deployments run from the main branch only after all checks pass. A failed workflow leaves the previous Pages deployment available. Recovery is a normal revert or a generator fix followed by the same workflow; canonical evaluation evidence is not rewritten during rollout or recovery.

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
- A valid Pages workflow cannot configure a repository that has never had Pages enabled when it uses only the default `GITHUB_TOKEN`; the repository owner must first select `GitHub Actions` as the publishing source or deliberately provide a separate administrative credential.
- The first deployment failure was therefore an external repository configuration prerequisite, not a defect in the generated site or its public-path tests.
- Production publication was confirmed with HTTP 200, and this ExecPlan was finalized on 2026-07-29.
- GitHub Actions run `30561602385` retained a byte-stable actual screenshot across all three attempts while differing from the local baseline only around text glyphs. Retrying cannot repair this class of failure; baseline generation and comparison must share the full visual environment.
- The first exact container rerun exposed a second source of nondeterminism: the mobile guide test used `window.scrollTo` while the document enabled smooth scrolling. Playwright observed materially different intermediate page positions behind an otherwise identical bottom sheet. The journey now requests an instant scroll to the already intended position rather than weakening pixel comparison.
- The attempted fixed `48px` heading position was below the document's lower scroll boundary; Chromium correctly stopped with the heading at `92px`. This ruled out an element offset and led to testing an explicit document boundary instead.
- A later exact run proved that the computed bottom boundary was also unsuitable as a canonical screenshot background because total rendered document height could place different collapsed evidence blocks under the fixed backdrop. The absolute document top is always reachable and independent of content height, so it is the stable background state for the page screenshot.
- The final-validation diff showed the fixed sheet unchanged while the underlying document advanced roughly 32 pixels after the test had observed `scrollY = 0`. The page's smooth-scroll policy was still active across earlier focus and click interactions. The visual journey now disables smooth scrolling before those interactions and waits for two rendered frames at the canonical top before capture.
- After that setup change, the desktop help screenshot differed from its previous baseline by 232 glyph-edge pixels. Ten isolated single-worker repetitions all produced the identical actual PNG hash `3bf273279949c70e2589ffc710f50c2b0747431c5dad66258e124f6a215cde19`. This was a stable obsolete baseline caused by the changed journey setup, not concurrency or residual rasterization instability, so the container baseline was updated.
- The first post-update exact run passed every screenshot and exposed a separate existing race in the desktop anchored-popover geometry test: `window.scrollBy` inherited smooth scrolling and the assertion sometimes observed the unchanged initial top. The test now requests the same 160-pixel movement instantly and retains its viewport and repositioning assertions.
- Post-GREEN design review classified early cleanup registration as a design risk: a host build failure occurred before volume creation but still invoked volume removal, which could obscure the original diagnostic. The runner now registers cleanup only after successful volume creation and never lets cleanup failure replace the primary command failure. Image-reference duplication was classified as no action because it crosses shell and GitHub Actions configuration boundaries and is mechanically synchronized.
