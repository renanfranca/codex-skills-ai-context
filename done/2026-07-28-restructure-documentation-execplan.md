# Create the restructure-documentation skill

This ExecPlan is a living document. Keep `Progress`, `Decisions`, `Risks`, and `Lessons Learned` up to date as work advances.

## Purpose / Big Picture

Create a general Codex skill that audits and restructures an existing documentation system around audiences, user journeys, canonical sources, and preserved public interfaces. Users should be able to invoke the skill on overlapping or poorly ordered repository documentation and receive a structurally coherent result with validated local navigation, while already coherent documentation may correctly produce no edits.

## Scope

In scope are the new `restructure-documentation/` skill, its metadata, one focused reference, a deterministic Markdown link validator and unit tests, four isolated evaluation cases, and catalog integration in `README.md` and `CODEX_CLI.md`.

Out of scope are `EVALUATIONS.md`, `AGENTS.md`, existing skill behavior, schemas, contracts, commits, staging, pushes, publication, external link checking, and documentation written from scratch. The current contents of `EVALUATIONS.md` must remain byte for byte unchanged by this work.

## Definitions

- A canonical source is the single document responsible for the normative or detailed treatment of one subject.
- A documentation role is the job a document performs, such as landing page, conceptual guide, cookbook, normative reference, agent instruction, or historical record.
- A public interface includes stable file paths, links, headings used as anchors, documented commands, facts, and other contracts readers or automation may rely on.
- GitHub anchors are the fragments generated from Markdown headings using GitHub compatible slugging and duplicate suffixes.
- A semantic evaluation invokes an executor and, when configured, a judge. A deterministic evaluation checks a complete code contract without model sessions.

## Existing Context

The repository root is `/home/renanfranca/.codex/skills`. At the start of work on 2026-07-27, the exact Git HEAD was `673dfb98aefc470d7fac3b3c822d87e7e0f6fba4`, branch `main`, in worktree `/home/renanfranca/.codex/skills`. `git status --short` showed only untracked `_temporary/`, `develop-skill-with-evals/scripts/__pycache__/`, and `develop-skill-with-evals/scripts/tests/__pycache__/`. No tracked diff in `EVALUATIONS.md` was visible in this fresh context, but it remains explicitly protected against any edit.

`README.md` is the canonical active skill catalog. `CODEX_CLI.md` maps user tasks to skills. New skills must be initialized with `.system/skill-creator/scripts/init_skill.py`, preserve the untouched scaffold as a baseline under `/tmp`, add evaluation cases before final behavior, and use the repository evaluation runner.

## Desired End State

`restructure-documentation` has concise triggering metadata with explicit positive and negative boundaries, a workflow that preserves repository instructions, facts, interfaces, language, style, and concurrent changes, and conditional use of `implement-execplan` for risky multi-document work. Its reference defines document roles and canonical ownership. Its validator accepts Markdown files or directories, checks local links, images, GitHub heading fragments, HTML IDs, duplicates, and input errors with exit codes 0, 1, and 2.

The four evaluation cases cover systemic restructuring, trigger boundaries, justified no action, and the deterministic validator contract. Structural validation, unit tests, the side effect free cross cutting promotion plan, archive checks, catalog navigation, whitespace, and diff scope pass. Model backed promotion and the additional fresh-agent forward test run only after explicit cost authorization.

## Milestones

### Milestone 1 - Freeze the scaffold and add evaluation contracts

#### Goal

Create the official skill skeleton, preserve its untouched baseline, and define isolated evaluation behavior before implementing the final instructions.

#### Changes

- Create `restructure-documentation/` with `SKILL.md`, `agents/openai.yaml`, `references/`, and `scripts/`.
- Copy the untouched scaffold to a unique `/tmp` baseline.
- Add `evals/suite.json` and four cases with minimal generic fixtures, hidden oracles where complete mechanical checks are possible, and no leaked expected answers.

#### Validation

- Command: `test -f /tmp/<baseline>/restructure-documentation/SKILL.md`
- Command: `python3 -m json.tool restructure-documentation/evals/suite.json`
- Expected result: the baseline exists and all manifests parse.

#### Acceptance Criteria

- Evaluation cases exist before final skill behavior is written.
- Fixtures contain no repository specific history, private context, transcripts, or generated responses.

### Milestone 2 - Implement the reusable workflow and validator

#### Goal

Deliver the documentation architecture workflow and deterministic local navigation validation.

#### Changes

- Write `restructure-documentation/SKILL.md`.
- Write `restructure-documentation/references/documentation-roles.md`.
- Implement `restructure-documentation/scripts/check_markdown_links.py`.
- Add focused unit tests under `restructure-documentation/scripts/tests/`.
- Keep `agents/openai.yaml` aligned with the requested public metadata.

#### Validation

- Command: `python3 -m unittest discover -s restructure-documentation/scripts/tests -v`
- Command: `python3 .system/skill-creator/scripts/quick_validate.py ./restructure-documentation`
- Expected result: all focused checks pass.

#### Acceptance Criteria

- The validator satisfies links, images, fragments, duplicate headings, HTML IDs, and exit code contracts.
- The skill explicitly permits evidence based no action and preserves public interfaces and concurrent work.

### Milestone 3 - Integrate discovery and plan promotion

#### Goal

Expose the ninth active skill and produce an auditable cross cutting promotion plan without consuming model sessions.

#### Changes

- Add the skill to `README.md` under “Skill development and design”.
- Add the task row to `CODEX_CLI.md`.
- Generate and inspect the cross cutting promotion plan using `gpt-5.6-terra` with medium effort for executor and judge.

#### Validation

- Command: `python3 develop-skill-with-evals/scripts/run_skill_evals.py plan --skill ./restructure-documentation --baseline /tmp/<baseline>/restructure-documentation --impact cross-cutting --case documentation-system-restructure --case trigger-boundaries --case cohesive-no-action --workflow promotion --model gpt-5.6-terra --reasoning-effort medium --judge-model gpt-5.6-terra --judge-reasoning-effort medium`
- Expected result: valid plan, deterministic regression selected, stable fingerprints, no runtime blocker, and no model session is consumed.

#### Acceptance Criteria

- Catalog and task map link to existing files.
- The plan reports at most 24 model sessions and matches the intended gates.

### Milestone 4 - Complete authorized gates and final validation

#### Goal

Run every authorized gate, preserve durable evidence, and verify a minimal clean diff.

#### Changes

- If and only if the user explicitly authorizes the planned model session cost, run `codex doctor --json` and the exact promotion command with the reviewed limit.
- After successful promotion, rebuild and validate the evaluation archive.
- If and only if separately authorized for one additional session, perform the fresh-agent forward test with a distinct fixture and no answer leakage.
- Update this plan with actual results and any remaining blocked gate.

#### Validation

- Command: `python3 develop-skill-with-evals/scripts/manage_evaluation_archive.py rebuild --archive evaluation-reports`
- Command: `python3 develop-skill-with-evals/scripts/manage_evaluation_archive.py validate --archive evaluation-reports`
- Command: `python3 restructure-documentation/scripts/check_markdown_links.py README.md CODEX_CLI.md restructure-documentation`
- Command: `git diff --check`
- Command: `git diff --name-only`
- Expected result: all authorized checks pass and diff scope contains only planned files, with no change to `EVALUATIONS.md` or `AGENTS.md`.

#### Acceptance Criteria

- Every executed gate passes; any non PASS model result stops further promotion attempts.
- Unexecuted cost gated work is reported honestly and not represented as promotion evidence.

## Progress

- [x] Initial HEAD, branch, worktree, and status recorded
- [x] Required skill instructions and evaluation contract loaded
- [x] Milestone 1 started
- [x] Milestone 1 completed
- [x] Milestone 2 started
- [x] Milestone 2 completed
- [x] Milestone 3 started
- [x] Milestone 3 completed
- [x] Milestone 4 started
- [x] Milestone 4 completed
- [x] Validator unit tests passed: 6 tests
- [x] Repository evaluation runner tests passed: 90 tests
- [x] Skill structure and metadata validated
- [x] Deterministic validator case passed with zero model sessions
- [x] Cross cutting promotion plan generated with 24 maximum sessions
- [x] Existing evaluation archive validated: 30 reports and 1 comparison
- [x] Explicit authorization received for 24 promotion sessions
- [x] First model backed promotion stopped on a blocking result after 5 sessions
- [x] Material evidence and oracle contract fixes validated
- [x] Revised promotion plan generated with evaluation fingerprint `0be0d987e6a4fdd91725aa77b4d0371a67a906baee294ffd3dd6f19d8e6d77e4`
- [x] Explicit authorization received for up to 24 additional promotion sessions
- [x] Second model backed promotion stopped on a blocking result after 9 sessions
- [x] No-action evidence contract revised and validated
- [x] Third promotion plan generated with evaluation fingerprint `170a7045d60e7d9404c016d41c8c7be47c27e4a846a6048a15f23a251d31e12d`
- [x] Explicit authorization received for up to 24 additional sessions for the third promotion
- [x] Third model backed promotion stopped on a blocking result after 5 sessions
- [x] Judge evidence responsibilities revised to match its observable inputs
- [x] Fourth promotion plan generated with evaluation fingerprint `d28d7578948a88e21f15c184a7973e30e0e58d724151ead133998bd854f85e07`
- [x] Explicit authorization received for up to 24 additional sessions for the fourth promotion
- [x] Fourth model backed promotion stopped on a blocking contract result after 4 sessions
- [x] First occurrence contract made explicit after the fourth promotion evidence
- [x] Fifth promotion plan generated with evaluation fingerprint `0d43088cacbc823766df938886f8c671e13d7ebcd83fba81c18a55fd83a78065`
- [x] Explicit authorization received for up to 24 additional sessions for the fifth promotion
- [x] Fifth model backed promotion stopped on an inconclusive trigger judge after 7 sessions
- [x] Trigger final evidence and judge observability contracts aligned
- [x] Sixth promotion plan generated with evaluation fingerprint `752b109a1e2596236f4230da51b385b4ad633d8f0a865188543af84769a8086b`
- [x] Explicit authorization received for up to 24 additional sessions for the sixth promotion
- [x] Sixth model backed promotion completed all gates but stopped as unstable after 21 sessions
- [x] Systemic ExecPlan output path made explicit to stabilize outcome relevant paths
- [x] Seventh promotion plan generated with evaluation fingerprint `134d53a1035e33d3c392697d1088eb26dbabb6b20a7348d7a7f36fb336718dc8`
- [x] Seventh cross cutting plan discarded before execution as disproportionate
- [x] Scoped systemic promotion plan generated with evaluation fingerprint `3e578255f84c06a16ba32ec6013bc0a083af7d0d61315d3a6b408ece6330858a`
- [x] Explicit authorization received for up to 8 sessions for the scoped systemic promotion
- [x] Next materially revised promotion passed through the scoped systemic gate
- [x] Evaluation archive rebuilt and revalidated after promotion
- [x] Separate authorization received for one fresh-agent forward-test session
- [x] Fresh-agent forward test passed

## Decisions

- Decision: Classify the new behavior as cross cutting.
  Rationale: Triggering, central workflow, and shared documentation architecture have uncertain semantic reach.
  Date/Author: 2026-07-27 / Codex

- Decision: Treat `EVALUATIONS.md` as immutable even though no tracked modification is visible.
  Rationale: The user explicitly identified it as protected concurrent work; absence in the current status does not broaden scope.
  Date/Author: 2026-07-27 / Codex

- Decision: Do not run a diagnostic by default.
  Rationale: The requested workflow reserves model sessions for one promotion operation after cost authorization and says not to diagnose by default.
  Date/Author: 2026-07-27 / Codex

- Decision: Make implicit invocation explicit in `agents/openai.yaml`.
  Rationale: Although omission defaults to true, an explicit policy entry makes the requested public integration auditable.
  Date/Author: 2026-07-27 / Codex

- Decision: Stop before preflight and promotion when the plan reported the default eight-session authorization blocker.
  Rationale: The reviewed promotion requires up to 24 sessions and shell approval is not model cost authorization.
  Date/Author: 2026-07-27 / Codex

- Decision: Interpret the user's unqualified “autorizo” as approval for both explicitly enumerated limits.
  Rationale: The immediately preceding question presented only two authorization items: 24 promotion sessions and one conditional forward-test session.
  Date/Author: 2026-07-27 / Codex

- Decision: Treat the first promotion result as actionable evidence and make a material contract revision before any new run.
  Rationale: The candidate passed the systemic mechanical checks and hidden oracle, but the judge returned `INCONCLUSIVE` because the structured executor response did not contain concrete headings or link destinations. The trigger oracle also rejected a valid status placed in a heading.
  Date/Author: 2026-07-27 / Codex

- Decision: Extend the concrete evidence requirement to the public no-action prompt before another plan.
  Rationale: The second promotion passed GREEN 1 for the systemic and trigger cases, then the no-action judge returned `INCONCLUSIVE` solely because the executor named document roles without citing exact headings and link destinations.
  Date/Author: 2026-07-27 / Codex

- Decision: Calibrate the systemic judge to the evidence surface it actually receives.
  Rationale: The third promotion's executor supplied concrete headings, destinations, lines, and preserved facts; all mechanical checks and the hidden oracle passed, but the judge demanded an additional diff unavailable in its input. The same evidence shape had passed the prior judge, demonstrating contract instability.
  Date/Author: 2026-07-27 / Codex

- Decision: Keep the first occurrence oracle strict and make its rule explicit.
  Rationale: The fourth promotion's candidate used the plural `workspaces` in the conceptual introduction before the `## Workspace` definition. The skill required first occurrence checks, but the executor interpreted ordering only relative to procedures. Making the lexical rule public aligns behavior and evaluation without weakening the architectural contract.
  Date/Author: 2026-07-27 / Codex

- Decision: Require trigger rationale in the executor's final evidence.
  Rationale: The fifth promotion produced a semantically correct assessment and passed its oracle, but the judge does not receive the complete file and could not verify reasons that appeared only there. The public prompt now requires each status and reason in final evidence, while the judge delegates exact status verification to the oracle.
  Date/Author: 2026-07-27 / Codex

- Decision: Make the systemic case's required ExecPlan path deterministic.
  Rationale: All sixth-promotion gates passed, but only one of three systemic repetitions created the ExecPlan required by the skill's risk rule. A fixed public path and required-path check make that observable output stable without weakening the runner's signature.
  Date/Author: 2026-07-27 / Codex

- Decision: Replace the unexecuted seventh cross cutting plan with a scoped systemic gate.
  Rationale: The only post-promotion change affected the systemic case's prompt and required paths. Trigger boundaries, no-action behavior, and the deterministic validator had already passed all required repetitions or regression in the immediately preceding operation, so repeating them would add cost without observing the change.
  Date/Author: 2026-07-27 / Codex

- Decision: Use `gpt-5.6-sol` with medium effort whenever a future runtime choice would otherwise use Terra, including executor and judge roles.
  Rationale: The user explicitly rejected further Terra use after repeated literal and inconclusive judge outcomes. This is an execution preference and does not modify the skill or its evaluation contracts. The fresh-agent forward test therefore used one Sol medium executor and no judge.
  Date/Author: 2026-07-28 / Codex

## Risks and Mitigations

- Risk: Restructuring tests may reward prose rather than architecture.
  Mitigation: Use exact structural oracles for preserved files, paths, anchors, and facts, plus a semantic judge only for audience, journey, and ownership quality.

- Risk: Fixtures or references could leak this repository's private design trajectory.
  Mitigation: Use small fictional repositories and general principles only; never copy `ai-context` content into the skill or fixtures.

- Risk: GitHub anchor emulation may diverge on punctuation, Unicode, duplicate headings, or HTML IDs.
  Mitigation: Specify the supported algorithm, test representative edge cases, and keep failures explicit instead of silently accepting ambiguous fragments.

- Risk: Model backed validation could consume unapproved sessions.
  Mitigation: Work stopped after the side effect free plan; request explicit cost authorization before `codex doctor` and `validate-change`.

- Risk: A second promotion would exceed the original authorization after five sessions were consumed.
  Mitigation: Make and validate the material contract fix, generate a fresh plan, and request separate authorization for its full maximum before execution.

- Risk: Untracked files predate this task.
  Mitigation: Do not delete, stage, or modify `_temporary/` or existing Python caches.

- Risk: The protected concurrent `EVALUATIONS.md` change became visible again during final validation.
  Mitigation: Treat its entire diff as external state, do not edit it, and verify the task's own patches and commands never target that file.

## Validation Strategy

1. Run validator unit tests and direct CLI contract checks.
2. Validate the skill structure, YAML metadata, JSON manifests, and hidden oracle syntax.
3. Generate and schema validate the cross cutting promotion plan without model calls.
4. Run model backed promotion only after explicit cost authorization and a healthy preflight.
5. Rebuild and validate archived evidence after any successful model backed execution.
6. Validate repository links, anchors, whitespace, protected files, and exact diff scope.

## Rollout and Recovery

This task does not install, publish, stage, commit, or push the skill. Recovery is limited to reviewing and reverting only the newly created `restructure-documentation/` directory, this ExecPlan, and the two explicit catalog edits. Existing untracked state and protected documents must remain untouched.

## Lessons Learned

- The fresh context did not contain the expected concurrent `EVALUATIONS.md` diff, so protection must be enforced by path scope rather than inferred from current Git output.
- The untouched generated scaffold was frozen at `/tmp/restructure-documentation-baseline.TBpwid/restructure-documentation` before any evaluation or behavior file was added.
- Semantic RED does not rely only on model judgment: each affected case invokes the bundled validator, which is absent from the untouched scaffold.
- Deterministic `mechanical.commands` do not expand `{oracle_dir}`. The first zero-session contract run exposed this and the checker was moved into the isolated fixture before rerunning.
- The cross cutting plan selects the three semantic cases as affected, the deterministic case as regression, and requires exactly 24 maximum model sessions. Its only execution blocker is the unapproved increase from the default limit of 8.
- Local validation passed with 6 validator tests, 90 runner tests, valid skill structure, valid local navigation, clean whitespace, protected documents unchanged, and a valid existing archive containing 30 reports.
- The first authorized promotion consumed 5 sessions and stopped at candidate GREEN 1 with `INCONCLUSIVE`. The candidate had passed all systemic mechanical and hidden oracle checks, but the judge lacked concrete document evidence in the structured response. Its report is archived at `evaluation-reports/restructure-documentation/operations/20260727T170833.654743Z-a06b21740974/`.
- The same operation exposed that the trigger oracle was stricter than its public prompt: it rejected `## SYS-1 — APPLY` even though this is a natural heading plus status. The oracle now accepts the status either in the heading or section body while still requiring exactly one allowed status.
- The revised cross cutting plan remains 24 maximum sessions and has a new evaluation fingerprint, `0be0d987e6a4fdd91725aa77b4d0371a67a906baee294ffd3dd6f19d8e6d77e4`. Because the earlier operation consumed 5 sessions, this is a new cost decision rather than a continuation within the original limit.
- The second promotion consumed 9 sessions. Systemic restructuring and trigger boundaries both passed candidate GREEN 1; `cohesive-no-action` passed all mechanical checks but its judge returned `INCONCLUSIVE` because the response lacked exact headings and reader-path links. Its evidence is archived at `evaluation-reports/restructure-documentation/operations/20260727T171652.898155Z-23d46f21e916/`.
- The previously anticipated concurrent `EVALUATIONS.md` edit became visible during validation. It concerns `refactor-design`, is outside this task, and remains untouched. A compound shell check initially printed a misleading later success line after `git diff --exit-code` returned nonzero; subsequent scope reporting must distinguish “concurrent diff exists and is preserved” from “path has no diff.”
- The third plan has fingerprint `170a7045d60e7d9404c016d41c8c7be47c27e4a846a6048a15f23a251d31e12d` and still requires up to 24 sessions. The two earlier blocked promotions consumed 14 sessions in total, so this plan requires another explicit cost decision.
- The third promotion consumed 5 sessions and stopped at systemic candidate GREEN 1 with `INCONCLUSIVE`, despite passing every mechanical check and the hidden oracle and supplying concrete cited evidence. Its report is archived at `evaluation-reports/restructure-documentation/operations/20260727T172917.092637Z-24a0aea91593/`. Across the three blocked operations, 19 sessions were consumed.
- The fourth plan has fingerprint `d28d7578948a88e21f15c184a7973e30e0e58d724151ead133998bd854f85e07`. Its systemic judge contract now explicitly assigns independently mechanical facts to the passing oracle and reserves semantic interpretation for the judge.
- The fourth promotion consumed 4 executor sessions and stopped before semantic judgment because systemic candidate GREEN 1 violated the first occurrence oracle. Links, protected paths, public anchors, and other mechanical checks passed. Its report is archived at `evaluation-reports/restructure-documentation/operations/20260727T174503.293755Z-31ee59c08177/`.
- The material revision explicitly covers inflected and plural terms in the skill workflow and public systemic prompt. The fifth plan has fingerprint `0d43088cacbc823766df938886f8c671e13d7ebcd83fba81c18a55fd83a78065`, requires up to 24 sessions, and is blocked only by the default eight-session limit. The archive rebuild and validation pass with 35 reports and 1 comparison.
- The fifth promotion consumed 7 sessions. Systemic candidate GREEN 1 passed every mechanical, oracle, and judge gate, proving the first occurrence revision effective. Trigger candidate GREEN 1 then passed mechanical checks and its oracle but received `INCONCLUSIVE` because its correct per-request reasons existed only in the generated file, outside the judge's evidence surface. Its report is archived at `evaluation-reports/restructure-documentation/operations/20260727T181016.250690Z-0aafa27ac157/`.
- The sixth plan has fingerprint `752b109a1e2596236f4230da51b385b4ad633d8f0a865188543af84769a8086b`, requires up to 24 sessions, and is blocked only by the default eight-session limit. Structural checks pass and the archive validates with 36 reports and 1 comparison.
- The sixth promotion consumed 21 sessions. All three REDs, all nine semantic GREENs, and the deterministic regression passed, but promotion ended `UNSTABLE`: systemic repetition 1 changed the four documentation files plus `documentation-restructure-execplan.md`, while repetitions 2 and 3 changed only the four documentation files. Its report is archived at `evaluation-reports/restructure-documentation/operations/20260727T182350.025533Z-9643f8cb7f3b/`.
- The seventh plan requires the living ExecPlan at one public path and has fingerprint `134d53a1035e33d3c392697d1088eb26dbabb6b20a7348d7a7f36fb336718dc8`. It still requires up to 24 sessions and is blocked only by the default eight-session limit. The archive validates with 37 reports and 1 comparison.
- The unexecuted seventh cross cutting plan was superseded by a scoped plan for the only affected case. The scoped plan has fingerprint `3e578255f84c06a16ba32ec6013bc0a083af7d0d61315d3a6b408ece6330858a` and a maximum of 8 sessions.
- The scoped systemic promotion passed with one valid RED and three stable GREEN results, consuming 7 sessions. Every candidate repetition changed exactly `README.md`, the three role-specific documents, and `documentation-restructure-execplan.md`; mechanical checks, the hidden oracle, and the judge passed. Its report is archived at `evaluation-reports/restructure-documentation/operations/20260727T233326.147750Z-48228593ef91/`.
- The fresh-agent forward test used one `gpt-5.6-sol` session with medium effort and no judge against the distinct Aster Backup fixture at `/tmp/restructure-documentation-forward.ryBqXv`. It correctly found structural overlap, created a living ExecPlan, separated landing, concept, operations, and reference roles, preserved both public anchors and every required fact, and left `AGENTS.md` unchanged. The bundled link validator, `git diff --check`, and the protected-file diff check all exited 0. Direct review confirmed that the first conceptual occurrence of “snapshot” is its definition heading followed immediately by the definition.
