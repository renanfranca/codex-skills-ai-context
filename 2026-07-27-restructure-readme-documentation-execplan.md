# Restructure the README as a Public Landing Page

This ExecPlan is a living document. Keep `Progress`, `Decisions`, `Risks`, and `Lessons Learned` up to date as work advances.

## Purpose / Big Picture

Rewrite `README.md` as a short, portable landing page for people arriving at the public GitHub repository. A visitor should quickly understand what the repository offers, install and invoke one skill, browse the canonical catalog of active skills, and follow the correct guide for operational use, evaluations, or contribution conventions.

The completed change is observable by reading the README from top to bottom: it introduces the value of the repository, explains a Codex skill, gives one minimal setup path, lists eight active skills, routes detailed topics to their authoritative documents, separates disabled historical skills from the active catalog, and closes with the license.

## Scope

In scope:

- Rewrite `README.md` in English.
- Preserve the `# Codex Skills` title.
- Preserve the generated anchors `what-is-a-codex-skill` and `skill-catalog`.
- Keep `README.md#skill-catalog` as the canonical list of eight active skills.
- Keep `README.md#what-is-a-codex-skill` as the introduction linked by `EVALUATIONS.md`.
- Use portable paths: `/path/to/codex-skills`, `$HOME/.agents/skills`, and `<repository>/.agents/skills`.
- Audit catalog descriptions against each active skill's `SKILL.md` and optional `agents/openai.yaml`.
- Add compact routes to `CODEX_CLI.md`, `EVALUATIONS.md`, and `AGENTS.md`.
- Put the four disabled TDD skills in a collapsed historical compatibility note.
- Create and maintain this ExecPlan, based on repository commit `0279e400da74402a080116526c07a3a7eac23efa`.

Out of scope:

- Changes to `AGENTS.md`, `CODEX_CLI.md`, `EVALUATIONS.md`, any skill, metadata, runner, report, or repository policy.
- Model-backed evaluations, archive rebuilding, staging, committing, pushing, or publishing.
- Detailed CLI commands, sandbox guidance, resume flows, troubleshooting, evaluation internals, pricing, archive operations, and detailed RED/GREEN policy in the README.

Safety boundary: This task is limited to authorized documentation maintenance in this repository.

## Definitions

- Codex skill: A directory containing a required `SKILL.md` with `name` and `description`, plus optional supporting resources such as references and scripts.
- Active skill: One of the eight skills intentionally presented in the canonical README catalog and available for normal use in this repository.
- Disabled skill: A retained source directory that exists for historical compatibility but is disabled in local Codex configuration and should not look active.
- Progressive disclosure: Codex initially sees a skill's name and description, then reads the full `SKILL.md` and relevant supporting resources when the skill is selected.
- Discovery location: A directory Codex scans for skills, including `$HOME/.agents/skills` for a user and `.agents/skills` within a repository hierarchy.
- Static change: A documentation-only change that does not alter skill behavior, schemas, APIs, or evaluation policy.
- Cookbook: `CODEX_CLI.md`, the detailed operational guide for using these skills with Codex CLI.

## Existing Context

The base commit is `0279e40`, whose subject is `docs: restructure Codex CLI cookbook`. At the start of this work, `README.md` mixes landing-page content with operational commands, repository internals, detailed evaluation policy, pricing and archive topics, and development workflow. It also contains the personal path `/home/renanfranca/.codex/skills`.

`CODEX_CLI.md` already links to `README.md#skill-catalog` as the canonical active skill list and contains operational selection, TUI, `codex exec`, evaluation recipes, review, safety, and troubleshooting guidance. `EVALUATIONS.md` links to `README.md#what-is-a-codex-skill` and owns evaluation concepts and supervision. `AGENTS.md` owns repository contribution conventions.

The eight active catalog entries are:

- `develop-skill-with-evals`
- `refactor-design`
- `implement-execplan`
- `seed4j-execplan-tdd`
- `seed4j-worktree-flow`
- `tdd-behavior-autonomous-quiet`
- `commit-staged-change`
- `commit-the-changes`

The retained but disabled historical directories are `tdd`, `tdd-strict-cycle-confirmation`, `tdd-strict-autonomous`, and `tdd-strict-autonomous-quiet`.

The current official Codex skills documentation states that skills use progressive disclosure; activation can be explicit with `$skill-name` or implicit when the request matches the skill description; Codex discovers user skills in `$HOME/.agents/skills` and repository skills in `.agents/skills` from the current working directory through the repository root; symlinked skill folders are supported; and `agents/openai.yaml` is optional metadata for interface, invocation policy, and dependencies. The official source was refreshed through the Codex manual helper on 2026-07-27.

## Desired End State

`README.md` is a concise public landing page in this order:

1. Repository value proposition.
2. `## What is a Codex skill?`.
3. Portable quick start with one installation example and one explicit invocation example.
4. `## Skill catalog` with the eight active skills grouped by category.
5. A compact documentation routes section for operational use, evaluations, and maintenance.
6. A collapsed `<details>` block for four disabled skills retained for historical compatibility.
7. License.

The README contains no personal filesystem path and no detailed operational or evaluation procedures already owned by the specialized guides. Only `README.md` and this ExecPlan differ as a result of this task.

## Milestones

### Milestone 1 - Establish the documentation contract

#### Goal

Capture the current cross-document anchors, official Codex skill behavior, active catalog metadata, allowed file scope, and intended information architecture before editing.

#### Changes

- [x] Create `_temporary/codex-skills-ai-context/2026-07-27-restructure-readme-documentation-execplan.md`.
- [x] Record base commit `0279e40`, preserved anchors, eight active skills, four disabled skills, and out-of-scope files.
- [x] Inspect active `SKILL.md` frontmatter and `agents/openai.yaml`.
- [x] Inspect official Codex skill discovery, invocation, progressive disclosure, and optional metadata guidance.

#### Validation

- [x] Command: `git show -s --format='%H%n%cs%n%s' 0279e40`
- [x] Expected result: the full base commit hash, date, and cookbook restructuring subject are visible.
- [x] Command: `rg -n '^#|README\\.md#|\\]\\(README\\.md' CODEX_CLI.md EVALUATIONS.md AGENTS.md`
- [x] Expected result: references to `README.md#skill-catalog` and `README.md#what-is-a-codex-skill` are identified.

#### Acceptance Criteria

- [x] Another engineer can understand the documentation ownership and compatibility requirements from this plan alone.
- [x] The official product behavior needed by the README is recorded without relying on memory.

### Milestone 2 - Rewrite the README

#### Goal

Produce the short public landing page while preserving canonical anchors and catalog accuracy.

#### Changes

- [x] Rewrite `README.md` in the required section order.
- [x] Replace the personal path with portable discovery and source paths.
- [x] Keep one minimal installation example and one explicit invocation example.
- [x] Audit and retain eight active catalog entries with concise, accurate descriptions.
- [x] Replace duplicated operational, evaluation, and maintenance material with three documentation routes.
- [x] Move the four disabled TDD skills into a collapsed historical compatibility note.
- [x] Retain the Apache License 2.0 link.

#### Validation

- [x] Command: `rg -n '^#|/home/renanfranca|\\$HOME/\\.agents/skills|<repository>/\\.agents/skills|<details>|CODEX_CLI\\.md|EVALUATIONS\\.md|AGENTS\\.md' README.md`
- [x] Expected result: required headings, portable discovery locations, routes, and collapsed legacy note are present; the personal path is absent.
- [x] Command: `git diff --check -- README.md`
- [x] Expected result: no whitespace errors.

#### Acceptance Criteria

- [x] A visitor understands the repository's purpose from the opening paragraphs.
- [x] A new user can install and explicitly invoke one skill from the quick start.
- [x] The eight active skills remain easy to browse and the disabled skills cannot be mistaken for active catalog entries.
- [x] Detailed subjects route to their authoritative documents without duplicated procedures.

### Milestone 3 - Validate navigation and scope

#### Goal

Verify all local Markdown targets and required anchors, inspect the exact diff, and confirm the five reader journeys.

#### Changes

- [x] Update this ExecPlan with final progress, decisions, risks, and lessons learned.
- [x] Make no additional repository changes unless validation exposes a README defect.

#### Validation

- [x] Command: run a local Markdown link and anchor checker over `README.md`, `CODEX_CLI.md`, and `EVALUATIONS.md`.
- [x] Expected result: all 76 local file targets and explicit anchor references resolve.
- [x] Command: inspect headings and their first occurrence in `README.md`.
- [x] Expected result: `What is a Codex skill?` and `Skill catalog` each occur once, at lines 7 and 29, and generate the preserved anchors.
- [x] Command: `git diff -- README.md` and `git diff --no-index /dev/null _temporary/codex-skills-ai-context/2026-07-27-restructure-readme-documentation-execplan.md`
- [x] Expected result: the scoped diffs contain only the requested README rewrite and living ExecPlan.
- [x] Command: `git status --short`
- [x] Expected result: `README.md` is the only modified tracked file; pre-existing untracked `_temporary/` and Python cache directories remain present and untouched except for the requested new ExecPlan.

#### Acceptance Criteria

- [x] Visitor journey: purpose is clear without reading another document.
- [x] New-user journey: installation and explicit invocation are immediately actionable.
- [x] Operator journey: cookbook is easy to find.
- [x] Maintainer journey: evaluation and contribution sources are easy to find without duplicated commands.
- [x] Legacy journey: historical skills remain discoverable but visually separate from active skills.

## Progress

- [x] Milestone 1 started.
- [x] Milestone 1 completed.
- [x] Milestone 2 started.
- [x] Milestone 2 completed.
- [x] Milestone 3 started.
- [x] Milestone 3 completed.

## Decisions

- Decision: Classify the change as static documentation.
  Rationale: It changes navigation and presentation only, with no skill behavior, schema, runner, or policy change.
  Date/Author: 2026-07-27 / Codex

- Decision: Treat the Codex manual's Build skills section as the official source for product behavior.
  Rationale: The `openai-docs` workflow requires the refreshed Codex manual first for broad Codex self-knowledge, and that section directly covers all claims needed here.
  Date/Author: 2026-07-27 / Codex

- Decision: Keep the catalog grouped into skill development and design, Seed4J workflows, test-driven development, and Git commits.
  Rationale: The categories remain useful for scanning and avoid duplicating the task-oriented workflow map already maintained in `CODEX_CLI.md`.
  Date/Author: 2026-07-27 / Codex

- Decision: Use one copied skill in the quick start and mention the repository discovery location in prose.
  Rationale: This gives a complete first use path in one example while keeping the README portable and leaving symlinks, installation verification, and alternate workflows to `CODEX_CLI.md`.
  Date/Author: 2026-07-27 / Codex

## Risks and Mitigations

- Risk: Renaming or removing headings could break links from specialized guides.
  Mitigation: Preserve the exact `What is a Codex skill?` and `Skill catalog` headings and validate their generated anchors.

- Risk: Simplification could make setup non-portable or omit the minimum usable path.
  Mitigation: Use official discovery locations, a generic source checkout path, one copy example, and one explicit invocation example.

- Risk: Short catalog descriptions could drift from actual skill triggers or policy.
  Mitigation: Compare every description with both `SKILL.md` frontmatter and optional `agents/openai.yaml` before finalizing.

- Risk: Disabled skills could look like supported active workflows.
  Mitigation: Keep them outside `## Skill catalog` in a collapsed block labeled as historical compatibility.

- Risk: Validation could accidentally modify generated caches or unrelated files.
  Mitigation: Use read-only static checks, avoid model-backed evals and generators, and inspect `git status` plus the scoped diff.

## Validation Strategy

1. Check the README for required headings, portable paths, documentation routes, and the collapsed historical note.
2. Compare the catalog count and descriptions with the eight active skill sources and optional metadata.
3. Resolve local Markdown links and cross-document anchors in `README.md`, `CODEX_CLI.md`, and `EVALUATIONS.md`.
4. Run `git diff --check -- README.md`.
5. Inspect headings, first occurrences, the scoped diff, and repository status.
6. Read the page as the five target audiences: visitor, new user, operator, maintainer, and legacy reader.

Model-backed evaluations, archive generation, staging, commits, and pushes are intentionally excluded because this is a static documentation change.

## Rollout and Recovery

No deployment or migration is required. The repository landing page changes when the README revision is later committed and published by an authorized maintainer. Recovery is a normal revert of the README documentation commit; the preserved specialized guides and unchanged skill implementations remain authoritative throughout.

## Lessons Learned

- The official Codex documentation supports symlinked skill folders as well as copied folders, so a portable quick start can remain minimal without prescribing repository relocation.
- `CODEX_CLI.md` and `EVALUATIONS.md` already depend on the two planned README anchors, making exact heading preservation a compatibility requirement rather than a presentation preference.
- A static link check across all three public guides resolved 76 local targets and anchors, which provides stronger navigation evidence than checking only the two known inbound README anchors.
- Keeping the disabled skills after the route section, inside `<details>`, preserves historical discoverability without allowing them to inflate the canonical active catalog count of eight.
