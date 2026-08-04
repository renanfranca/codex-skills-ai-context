# Keep mobile workspace checks readable

This ExecPlan is a living document. Keep `Progress`, `Decisions`, `Risks and Mitigations`, and `Lessons Learned` current as work advances.

## Purpose / Big Picture

On narrow screens, a workspace check currently puts its role, command, and expected exit code on one flex row. The command receives only a narrow middle column and wraps character by character, making a normal check difficult to read. This fix will keep the metadata visible while giving the command a full readable line beneath it on a 390 px viewport.

## Scope

This work is limited to the responsive command row styling in `website/.vitepress/theme/custom.css`, its public browser regression coverage in `website/e2e/site.spec.mjs`, and snapshots only if the intended visible layout changes them.

Do not modify evaluation cases, prompts, fixtures, oracles, the generator, runner, schemas, reports, fingerprints, generated pages, routes, or operation history. The existing root worktree is otherwise clean except for pre-existing untracked local directories, which are out of scope.

## Definitions

A workspace check is a command declared by a case and displayed with its role and expected exit code. The command row is the list item containing those three facts. A narrow viewport here is 390 CSS pixels wide, matching the existing mobile layout regression coverage.

## Existing Context

`.command-list li` in `website/.vitepress/theme/custom.css` uses a horizontal flex layout with an 18 px gap. Its role and expected-exit label retain their width, leaving the command code element little horizontal space. `overflow-wrap: anywhere` prevents horizontal scrolling but allows the resulting unreadable character-level wrapping.

The existing narrow-layout E2E test protects visibility and absence of horizontal overflow for an oracle command, but it does not protect the number of rendered command lines or the workspace-check case that exposed the issue.

## Desired End State

At 390 px, the workspace check in `refactor-design/hidden-invocation-state` displays its role and expected exit together, with `python3 -m unittest -q` below them at usable width and in no more than two rendered lines. Desktop styling and every command's content, order, and accessibility remain unchanged. The layout remains free of horizontal overflow.

## Milestones

### Milestone 1: Protect and repair narrow command readability

#### Goal

Add a public visual regression assertion, show that it fails under the current flex layout, then use the smallest responsive CSS change to make command rows readable at the existing narrow viewport.

#### Changes

Edit `website/e2e/site.spec.mjs` first to observe the rendered command line count for `hidden-invocation-state` at 390 px. Edit `website/.vitepress/theme/custom.css` only after the expected visual RED. Keep the DOM order and generator output unchanged. Update snapshots only through `npm run test:e2e -- --update-snapshots` if the intentional layout changes a snapshot.

#### Validation

Run `npm test` after every test or CSS change. Run `npm run test:e2e` to demonstrate the new visual test as RED and then GREEN. Acceptance requires the command to use no more than two rendered lines, its role and expected exit to remain visible, and no horizontal document overflow.

## Progress

- [x] Website profile, memory worktree, and exact origin validated.
- [x] ExecPlan created before implementation.
- [x] Milestone 1 started.
- [x] Visual behavior test added.
- [x] Expected RED observed.
- [x] Responsive CSS implemented.
- [x] Milestone 1 completed.
- [x] Post-GREEN design review completed.
- [x] Documentation reconciled.
- [x] Final validation completed.

## Decisions

- Decision: Keep the current semantic HTML order and solve the problem through responsive CSS.
  Rationale: The command row already contains all required information in a meaningful order; only the narrow layout assigns insufficient width to the command.
  Date/Author: 2026-08-04 / Codex

- Decision: Define readability as at most two rendered command lines at 390 px.
  Rationale: This is a directly observable user outcome that rejects character-level wrapping without coupling the test to a particular CSS layout primitive.
  Date/Author: 2026-08-04 / Codex

## Risks and Mitigations

- Risk: A mobile-only layout rule could affect oracle commands or desktop presentation unintentionally.
  Mitigation: Scope the rule to the existing narrow breakpoint and exercise both the affected workspace check and the existing long oracle command journey.

- Risk: The test could assert CSS topology rather than readable behavior.
  Mitigation: Assert rendered command line count, visible metadata, and lack of overflow instead of flex or grid properties.

- Risk: A command naturally longer than the available width could need more than two lines.
  Mitigation: Apply the quantitative assertion only to the known short command that regressed; retain the existing visibility test for longer real commands.

## Validation Strategy

The relevant suite is `npm test` from `website/`. The public checkpoint is `npm run test:e2e` from `website/`, which builds the canonical projection and runs desktop and mobile browser journeys. The final ordered validation is `npm test`, `npm run prettier:check`, `npm run build`, `npm run test:e2e`, followed by `git diff --check` from the repository root.

## Documentation Impact

`website/README.md` remains accurate because it describes the site behavior and validation workflow, not the internal responsive placement of command metadata.

`website/content-config.json` remains accurate because it only configures catalog inclusion and has no layout contract.

Root repository documentation remains accurate because this change does not alter evaluation authoring, runner behavior, or public repository workflows. Generated pages under `website/.generated/` remain disposable projections and will not be edited directly.

## Rollout and Recovery

The static site will pick up the responsive style on the normal build and deployment path. Recovery is a targeted revert of the CSS, public regression test, and any intentional visual baseline updates; evaluation data remains untouched.

## Lessons Learned

The diagnosis found that preventing horizontal overflow is not sufficient evidence of command readability: the prior rule allowed the command itself to collapse and wrap at individual characters.

The new browser assertion was RED at 390 px: `python3 -m unittest -q` occupied four line boxes. A mobile grid places the role and expected exit on the first row and lets code and an optional explanatory note occupy the full row below. `npm test` passed 37 tests after both the test and CSS changes; the public checkpoint passed 32 browser journeys with 2 expected skips.

The post-GREEN review found no action. The responsive rule is local, preserves DOM order and desktop behavior, and the test observes readability rather than the chosen grid implementation. `website/README.md`, `website/content-config.json`, and root documentation remain accurate because this is an internal responsive presentation fix.

Final validation completed in the required order: `npm test` passed 37 tests, `npm run prettier:check` passed, `npm run build` validated 53 reports and generated 10 skills and 53 report pages, `npm run test:e2e` passed 32 journeys with 2 expected skips, and `git diff --check` completed with no output.
