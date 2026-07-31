# Rebase the auditable runtime on proportional evaluation

This ExecPlan is a living document. Keep `Progress`, `Decisions`, `Risks and Mitigations`, and `Lessons Learned` current after every new piece of evidence.

## Purpose / Big Picture

Make skill evaluations auditable under a declared and repeatable runtime without claiming model determinism. `plan` will expose the intended runtime, all blockers, and the maximum authorized model sessions. `validate-change` will be the only integrated gate that requires a complete runtime when the plan includes model sessions.

Users will observe the behavior through public JSON, the exact arguments passed to Codex, blocking without side effects, and the distinction between planned maximum sessions and actual subprocess consumption.

## Scope

In scope are the runner, its 29 existing tests and new tests, both JSON schemas, the evaluation contract, `SKILL.md`, agent metadata, the self-evaluation suite, root `AGENTS.md`, `README.md`, `CODEX_CLI.md`, `EVALUATIONS.md`, and this living ExecPlan.

Out of scope are presets, model matrices, automatic escalation, reading or changing `config.toml`, `CODEX_REASONING_EFFORT`, commits, pushes, publication, and changes under `.system/`.

The historical root `/tmp/auditable-skill-eval-runtime.NRwiun` is evidence only. No file from it will be read for promotion, copied, or applied.

Safety boundary: This task is limited to authorized, defensive maintenance of this repository. Do not provide offensive guidance or policy-bypassing instructions.

## Definitions

`baseline-source` is the immutable copy of canonical source. `baseline-eval` is the byte-for-byte copy used in gates. `candidate` is the only implementation location until promotion.

A complete promotion runtime has an executor model and reasoning effort explicitly supplied by CLI. When a judge is required, its model and effort are either supplied by its own CLI options or inherited from that complete executor. It never depends on configured defaults or only on `CODEX_MODEL`.

`CODEX_MODEL` remains a known resolution propagated to legacy commands, but it does not qualify a runtime for promotion.

`sessions.total` in a plan is the maximum authorized number of model sessions. Top-level `model_sessions.total` in an executed report is the actual number of model subprocesses invoked.

The isolated development root created for this execution is `/tmp/auditable-skill-eval-runtime.d1bn53`. Its canonical source is `/home/renanfranca/.codex/skills`; the candidate skill is `/tmp/auditable-skill-eval-runtime.d1bn53/candidate/develop-skill-with-evals`.

## Existing Context

The canonical tracked source was unchanged in scope when isolation began. Untracked `_temporary/` contains this plan repository, and existing `__pycache__` directories were present under the canonical skill. Those cache files were copied by the explicitly specified byte-preserving isolation commands but are excluded from promotion and source comparison.

On 2026-07-23 the canonical baseline had 29 passing deterministic tests, a passing `quick_validate.py` result, valid schemas and manifest JSON, and a passing `git diff --check`.

The current runner accepts only `--model` on executed commands. It reports `CODEX_MODEL` without propagating it to the subprocess and invokes a semantic judge even after mechanical failure. The replaced ExecPlan described obsolete gates, behavior, and a historical temporary root incompatible with this revision.

Initial source hashes recorded before behavioral changes:

    a6b4e13b6d8bfc06b7a603646c6ecd0394345fa2d1d0e9378daeb12b05f823c2  develop-skill-with-evals/SKILL.md
    d275b571fb56e486d5e179d370889031a02dad50df90de17618677286e329175  develop-skill-with-evals/scripts/run_skill_evals.py
    e00f82d4723fb3263d564ec0cb5e3fc999b8cef12c88b174af2c752f30b47b2c  develop-skill-with-evals/references/eval-contract.md
    3382380e4f1121ddf747e4bc05b954ab895efa460e21022ed21e0e15583ab0da  develop-skill-with-evals/references/eval-plan.schema.json
    b9eac0d04b38304b1f91b2ab4670c20dfeb5e51d4598d5488098bd70d598efc9  develop-skill-with-evals/references/eval-result.schema.json
    bc2e67a3092d8b4ce7e49e47308d7a1470d4d60fe2e81c540a8b22e2baaf9568  develop-skill-with-evals/evals/suite.json
    592aa410f044f26ab921c4c950ff8f55c4573530ef7c263e105b5dc1d5c75aa0  AGENTS.md
    116d42115e244d78c8c09c113c2550771903502d494af2d266bfc84dd204d84f  README.md
    6e0944c966dd5388a3d418bb579cef18ad27910ef92b2c35cf736ec3b04c4fcb  CODEX_CLI.md
    d77abd907242f6afce1bfd0492be707773e0620298811663eeb9734d356f56df  EVALUATIONS.md

## Desired End State

The `plan`, `validate-change`, `run`, `verify-change`, and `stability` commands accept:

    --model <model>
    --reasoning-effort <effort>
    --judge-model <model>
    --judge-reasoning-effort <effort>

Runtime precedence is:

| Role and field | Precedence |
| --- | --- |
| Executor model | `--model`, `CODEX_MODEL`, unknown configured default |
| Executor effort | `--reasoning-effort`, unknown configured default |
| Judge model | `--judge-model`, executor inheritance |
| Judge effort | `--judge-reasoning-effort`, executor inheritance |

The runner will not read `config.toml`. Unknown configured defaults are `null` in the runtime object and `configured-default` in the compatibility `model` field. Every known value is passed to its subprocess: model as `--model <value>` and effort as the direct argument pair `-c`, `model_reasoning_effort="<value>"`.

Plans and reports use this shape:

    {
      "runtime": {
        "required": true,
        "complete": true,
        "audit_quality": "promotion",
        "executor": {
          "required": true,
          "model": "gpt-5.6-sol",
          "model_source": "cli",
          "reasoning_effort": "medium",
          "reasoning_effort_source": "cli"
        },
        "judge": {
          "required": true,
          "model": "gpt-5.6-terra",
          "model_source": "cli",
          "reasoning_effort": "medium",
          "reasoning_effort_source": "cli"
        }
      }
    }

`audit_quality` accepts `promotion`, `exploratory`, or `not_applicable`. Plans add `runtime_fingerprint` and `execution_blockers`, an ordered list of `{code, message}` objects. Codes cover missing explicit runtime, unresolved judge runtime, and insufficient budget.

`runtime_fingerprint` is SHA-256 of canonical JSON containing the manifest fingerprint, per-role requirements, resolved runtime values, and their sources. Paths, budget, and derived fields stay outside the hash.

`plan` remains side effect free and exits 0 even with an incomplete runtime. `validate-change` aggregates all runtime and budget blockers, prints the plan, exits 2, and creates no workspace, artifact, or subprocess when any blocker exists.

Executed reports add top-level `runtime` and aggregate `model_sessions`. Existing per-result `model` and `model_sessions` fields remain compatible.

Judge reporting is:

| State | Required fields |
| --- | --- |
| Disabled | `enabled: false`, `executed: false`, current `PASS` verdict |
| Executed | `enabled: true`, `executed: true` |
| Skipped after mechanical failure | `enabled: true`, `executed: false`, `verdict: "SKIPPED"`, zero actual judge sessions |

## Milestones

### Milestone 1: Replace the plan and isolate current source

Create `/tmp/auditable-skill-eval-runtime.d1bn53` with `baseline-source`, `baseline-eval`, and `candidate` derived only from current canonical source. Copy the four root guides into `repo-docs` inside each tree. Make `baseline-source` recursively nonwritable. Replace the old ExecPlan before behavioral implementation and record literal paths, the confirmed 29 tests, and initial hashes.

Validate with:

    find /tmp/auditable-skill-eval-runtime.d1bn53 -maxdepth 2 -type d -printf '%M %p\n' | sort
    sha256sum /tmp/auditable-skill-eval-runtime.d1bn53/baseline-source/develop-skill-with-evals/{SKILL.md,scripts/run_skill_evals.py,references/eval-contract.md,references/eval-plan.schema.json,references/eval-result.schema.json,evals/suite.json}

Acceptance requires all three trees to derive only from current canonical source, `baseline-source` to be nonwritable, and this document to contain no obsolete milestones or decisions.

### Milestone 2: Develop the mechanical contract through RED and GREEN

Add public tests and the deterministic `auditable-runtime-contract` case first. Its fixture contains a fake Codex and `check_runtime_contract.py`, which locates the evaluated runner exclusively through `SKILL_EVAL_SKILL_DIR`.

Tests cover help and parsing, precedence, sources, inheritance, separate role argv, fingerprints, schemas, simultaneous blockers, exit code 2 without side effects, JSON on stdout, progress on stderr, judge skipping, and actual consumption.

Demonstrate RED by running the new tests against the `baseline-eval` runner. Then implement in the candidate:

* Immutable runtime objects shared by reports and argv construction.
* Separation of runtime selection options from execution controls.
* Runtime and blockers in plans.
* Combined fingerprint.
* Actual session aggregation.
* Judge skipping.
* Plan and result schema changes.

Validate with:

    PYTHONDONTWRITEBYTECODE=1 RUN_SKILL_EVALS_SCRIPT="/tmp/auditable-skill-eval-runtime.d1bn53/candidate/develop-skill-with-evals/scripts/run_skill_evals.py" \
      python3 -m unittest discover -s "/tmp/auditable-skill-eval-runtime.d1bn53/candidate/develop-skill-with-evals/scripts/tests" -v

    python3 .system/skill-creator/scripts/quick_validate.py \
      "/tmp/auditable-skill-eval-runtime.d1bn53/candidate/develop-skill-with-evals"

Run one deterministic validation with zero model sessions:

    python3 "/tmp/auditable-skill-eval-runtime.d1bn53/candidate/develop-skill-with-evals/scripts/run_skill_evals.py" validate-change \
      --skill "/tmp/auditable-skill-eval-runtime.d1bn53/candidate/develop-skill-with-evals" \
      --baseline "/tmp/auditable-skill-eval-runtime.d1bn53/baseline-eval/develop-skill-with-evals" \
      --impact deterministic \
      --case auditable-runtime-contract \
      --approved-model-sessions 0 \
      --progress

Acceptance requires baseline `FAIL`, three candidate `PASS` results with a stable signature, a zero-session plan, and no executor or judge subprocess.

### Milestone 3: Evaluate and update the central workflow

Before textual workflow changes, add semantic case `explicit-runtime-promotion-workflow`. Its raw prompt presents a realistic skill change, Sol and Terra runtimes, and the requirement to use `plan` followed by `validate-change`. It contains no criteria, diagnosis, or expected answer.

The fixture uses a minimal skill, isolated baseline, and fake Codex so the agent exercises semantically relevant validation without creating uncounted nested model sessions. Mechanical commands validate generated JSON, explicit arguments, and the absence of legacy operations as gates.

Then update:

* `SKILL.md` and `references/eval-contract.md`.
* `agents/openai.yaml`, preserving its display name and setting `short_description` to `"Plan auditable proportional skill evals"`.
* Both schemas and the suite.
* Root `AGENTS.md`, replacing mandatory full regression with proportional regression.
* Root `README.md`, `CODEX_CLI.md`, and `EVALUATIONS.md`.

Set `default_prompt` to:

    Use $develop-skill-with-evals to classify impact, declare executor and judge runtimes, and validate a skill change with proportional gates.

Acceptance requires the semantic case to predate workflow changes in candidate evidence and every guide to state the same promotion policy.

### Milestone 4: Plan cost and execute the single integrated semantic gate

First execute:

    python3 "/tmp/auditable-skill-eval-runtime.d1bn53/candidate/develop-skill-with-evals/scripts/run_skill_evals.py" plan \
      --skill "/tmp/auditable-skill-eval-runtime.d1bn53/candidate/develop-skill-with-evals" \
      --baseline "/tmp/auditable-skill-eval-runtime.d1bn53/baseline-eval/develop-skill-with-evals" \
      --impact cross-cutting \
      --case explicit-runtime-promotion-workflow \
      --model gpt-5.6-sol \
      --reasoning-effort medium \
      --judge-model gpt-5.6-terra \
      --judge-reasoning-effort medium

With eight existing cases plus two new cases, the expected maximum is 22 sessions: eight for the affected semantic case, fourteen for seven semantic regressions, and zero for two deterministic cases. The emitted JSON is authoritative.

Because the expected maximum exceeds eight, stop and request explicit approval for the displayed total. After approval, execute exactly one semantic `validate-change` with `--approved-model-sessions` equal to the plan total. Do not execute `run`, `verify-change`, `stability`, or a separate complete suite.

Any `FAIL`, `ERROR`, `INCONCLUSIVE`, `INVALID_RED`, or `UNSTABLE` stops the flow. Do not repeat an unchanged evaluation.

### Milestone 5: Fresh agent, review, and promotion

After the approved semantic `validate-change` is green, request separate approval for a fresh agent. Display this exact prompt first, replacing only the literal candidate path:

    Use $develop-skill-with-evals at /tmp/auditable-skill-eval-runtime.d1bn53/candidate/develop-skill-with-evals to plan and validate an authorized change to a temporary sample skill whose deterministic runner must add an audit_version field to its JSON output. Work only under /tmp, declare runtime explicitly for every model-backed operation, respect the displayed session limit, and do not commit or publish.

The fresh agent receives only candidate skill instructions and this task. It receives no criteria, diagnosis, historical baseline, or earlier conclusions.

Before promotion:

1. Compare canonical skill source and root documents against `baseline-source`, excluding only `__pycache__` and `*.pyc`.
2. Stop if any canonical byte changed.
3. Review diffs between `baseline-source`, `baseline-eval`, and `candidate`.
4. Apply only the reviewed patch to canonical files through file-scoped edits.
5. Do not copy the whole candidate tree.
6. Confirm byte-for-byte equivalence of promoted files.

Acceptance requires approved fresh-agent validation, unchanged canonical input, reviewed file-scoped promotion, and byte equivalence.

## Progress

- [x] Required skills and references loaded.
- [x] Canonical state and obsolete ExecPlan inspected.
- [x] 29 canonical tests confirmed by existing evidence.
- [x] Structural validation, schemas, and diff confirmed by existing evidence.
- [x] New isolated root created.
- [x] Old ExecPlan replaced in full.
- [x] Mechanical case and tests demonstrated RED.
- [x] Mechanical contract implemented and GREEN.
- [x] Stable deterministic validation completed with zero sessions.
- [x] Semantic case created before workflow changes.
- [x] Policy, metadata, and guides aligned.
- [x] Cross-cutting plan inspected.
- [x] Explicit approval for displayed maximum obtained.
- [x] Exactly one semantic `validate-change` executed; it stopped with `FAIL`.
- [x] Canonical source reconfirmed unchanged after the blocking gate.
- [x] Newly authorized semantic `validate-change` executed outside the restrictive sandbox; it stopped with `INCONCLUSIVE`.
- [x] User-authorized follow-up semantic gate executed after restored capacity; it stopped with `INCONCLUSIVE`.
- [x] Semantic fixture evidence corrected and replanned after judge feedback.
- [x] Explicit approval obtained for the corrected 22-session plan.
- [x] Corrected semantic `validate-change` executed; affected case passed three stable repetitions, then regression stopped with `FAIL`.
- [x] Ambiguous `load-skill-creator-first` destination corrected and replanned.
- [x] Explicit approval obtained for the destination-corrected 22-session plan.
- [x] Destination-corrected semantic `validate-change` executed; affected case passed three stable repetitions, then regression stopped with `INCONCLUSIVE`.
- [x] `load-skill-creator-first` process evidence made mechanically auditable and replanned.
- [x] Explicit approval obtained for the process-auditable 22-session plan.
- [x] Process-auditable semantic `validate-change` executed; it stopped with `INCONCLUSIVE` in `eval-before-behavior`.
- [x] `eval-before-behavior` temporal and RED evidence made mechanically auditable and replanned.
- [x] Explicit approval obtained for the revised 22-session plan.
- [x] Revised semantic `validate-change` executed; it stopped with `FAIL` in `self-evolution-candidate`.
- [x] `self-evolution-candidate` destination and isolation evidence corrected and replanned.
- [x] Explicit approval obtained for the isolation-auditable 22-session plan.
- [x] Isolation-auditable semantic `validate-change` executed; it stopped with `FAIL` on an obsolete full-regression criterion.
- [x] Non-behavioral regression criterion aligned with proportional validation and replanned.
- [x] Explicit approval obtained for the policy-aligned 22-session plan.
- [x] Policy-aligned semantic `validate-change` completed with `PASS`.
- [x] Separate approval and fresh agent completed.
- [x] Canonical source reconfirmed and patch promoted.
- [x] Final deterministic validation completed.

## Decisions

- Decision: Propagate `CODEX_MODEL`, but do not treat it as an explicit promotion model.
  Rationale: It is observable runtime resolution but does not meet the declared CLI requirement.
  Date/Author: 2026-07-23 / Codex

- Decision: Inherit judge runtime from executor when judge-specific options are absent.
  Rationale: Inheritance preserves a single explicit declaration while keeping role-specific overrides.
  Date/Author: 2026-07-23 / Codex

- Decision: Represent unknown configured defaults honestly without reading `config.toml`.
  Rationale: The runner must not fabricate or acquire undeclared runtime state.
  Date/Author: 2026-07-23 / Codex

- Decision: Define `runtime.complete` as promotion quality, not merely the existence of a value.
  Rationale: Environment-only or unknown defaults are insufficient for a repeatable promotion gate.
  Date/Author: 2026-07-23 / Codex

- Decision: Let `plan` report blockers and let only `validate-change` enforce them.
  Rationale: Planning must remain side effect free and useful for remediation.
  Date/Author: 2026-07-23 / Codex

- Decision: Use two distinct `validate-change` operations.
  Rationale: The deterministic operation consumes zero sessions; the semantic operation is the only model-backed integrated gate.
  Date/Author: 2026-07-23 / Codex

- Decision: Create the semantic case before central workflow text changes.
  Rationale: This preserves evaluation-before-behavior evidence.
  Date/Author: 2026-07-23 / Codex

- Decision: Never use the historical temporary root as implementation input.
  Rationale: Its baseline and gates belong to an obsolete plan.
  Date/Author: 2026-07-23 / Codex

- Decision: Stop before fresh-agent validation and promotion after the authorized semantic gate returned `FAIL`.
  Rationale: The plan makes every non-`PASS` result terminal and prohibits repeating an unchanged evaluation.
  Date/Author: 2026-07-23 / Codex

- Decision: Resume with one newly authorized semantic gate outside the restrictive sandbox.
  Rationale: The user explicitly authorized a new execution after the environmental failure. Changing the client initialization environment addresses the recorded cause, so this is not an unchanged retry.
  Date/Author: 2026-07-23 / Codex

- Decision: Stop again before fresh-agent validation and promotion after the resumed gate returned `INCONCLUSIVE`.
  Rationale: The judge hit the external usage limit after mechanically valid candidate evidence. `INCONCLUSIVE` remains terminal, and another unchanged evaluation is prohibited.
  Date/Author: 2026-07-23 / Codex

- Decision: Record the user's authorization for one follow-up semantic gate, but do not execute it while the reported usage limit is unchanged.
  Rationale: Authorization permits the future operation but does not alter the external blocking condition. Immediate execution would be an unchanged retry with a predictable `INCONCLUSIVE` result.
  Date/Author: 2026-07-23 / Codex

- Decision: Execute the authorized follow-up gate after the user confirmed restored capacity.
  Rationale: The external condition that caused the previous `INCONCLUSIVE` result has materially changed.
  Date/Author: 2026-07-23 / Codex

- Decision: Add explicit audited workflow evidence to the mechanical checker output before considering another gate.
  Rationale: The executor and every mechanical assertion passed, but the judge received no contents from the generated plan, validation report, or invocation log and therefore could not verify the semantic criteria independently.
  Date/Author: 2026-07-23 / Codex

- Decision: Make the `load-skill-creator-first` destination explicit as `./weather-brief`.
  Rationale: Its mechanical contract requires `weather-brief/SKILL.md`, while the prompt only said “in this workspace.” The executor reasonably chose `skills/weather-brief`, so the prompt did not uniquely specify the observed contract.
  Date/Author: 2026-07-23 / Codex

- Decision: Require and mechanically validate `creation-evidence.json` in `load-skill-creator-first`.
  Rationale: A created skill and passing structural validation do not prove that `skill-creator` was loaded first or that `init_skill.py` created the scaffold. The judge needs direct process evidence rather than inference.
  Date/Author: 2026-07-23 / Codex

- Decision: Stop the process-auditable gate after `eval-before-behavior` returned `INCONCLUSIVE`.
  Rationale: The final workspace and executor narrative cannot independently prove file creation order, a real baseline `FAIL`, or enforcement of invalid RED. The plan makes `INCONCLUSIVE` terminal and prohibits an unchanged retry.
  Date/Author: 2026-07-23 / Codex

- Decision: Use a fixed evaluator in `eval-before-behavior` to record RED and GREEN observations.
  Rationale: Hashes of the focused case and skill at both phases prove that the evaluation existed before behavior changed, remained fixed through GREEN, and produced an observed baseline `FAIL` without nested model sessions.
  Date/Author: 2026-07-23 / Codex

- Decision: Stop the revised gate after `self-evolution-candidate` returned `FAIL`.
  Rationale: The executor safely used an isolated `/tmp` candidate, but the mechanical contract required `candidate/SKILL.md` under the evaluation workspace. Because the prompt did not declare that destination, the result exposes a case ambiguity and cannot be promoted or retried unchanged.
  Date/Author: 2026-07-23 / Codex

- Decision: Retain self-evolution baseline and candidate under explicit workspace-relative paths.
  Rationale: A fixed checker can then independently prove that the baseline matches the repository-scoped skill, the candidate contains only the requested reminder, and promotion remains deferred.
  Date/Author: 2026-07-23 / Codex

- Decision: Remove the obsolete complete-regression requirement from `non-behavioral-no-artificial-red`.
  Rationale: The purpose of this change is to replace mandatory full regression with proportional gates. Keeping the old hidden criterion contradicts the updated public workflow and causes correct zero-session validation to fail.
  Date/Author: 2026-07-23 / Codex

- Decision: Advance to the separately approved fresh-agent stage after the policy-aligned gate passed.
  Rationale: The authoritative plan, complete runtime, affected-case stability, proportional regressions, and structural validation all passed within the approved maximum.
  Date/Author: 2026-07-23 / Codex

- Decision: Advance to canonical byte comparison after the isolated fresh agent passed.
  Rationale: The fresh agent independently completed a deterministic RED and three stable GREEN runs with explicit runtime declarations and zero nested model sessions.
  Date/Author: 2026-07-23 / Codex

- Decision: Promote the reviewed candidate patch without a post-GREEN refactor.
  Rationale: The design review found no concrete temporal coupling, hidden invocation state, duplicated transformation, or mixed responsibility that justified changing the already validated behavior. Additional extraction would add surface without reducing demonstrated risk.
  Date/Author: 2026-07-23 / Codex

## Risks and Mitigations

- Risk: Reported runtime and subprocess argv diverge.
  Mitigation: Derive both from the same immutable runtime objects and assert argv using fake Codex.

- Risk: Judge sessions are counted without execution.
  Mitigation: Separate planned maximum from actual consumption and report skipped judges explicitly.

- Risk: The semantic case launches uncounted nested evaluations.
  Mitigation: Use fake Codex in the fixture so only the outer executor and judge are real sessions.

- Risk: The maximum changes after approval.
  Mitigation: Rebuild the plan from snapshots and compare runtime fingerprint, manifest, selection, and counts before the first model invocation.

- Risk: Promotion includes temporary files or concurrent changes.
  Mitigation: Compare against immutable `baseline-source`, use file-scoped patches, and confirm final byte equivalence.

- Risk: Existing `__pycache__` files contaminate evidence.
  Mitigation: Exclude only `__pycache__` and `*.pyc` from canonical comparison and never promote them.

- Risk: Nested Codex sessions cannot initialize when their client state is on a read-only filesystem.
  Mitigation: Retain the blocking artifact and require a newly authorized evaluation in an environment where the Codex client can initialize normally. Do not reinterpret or retry this gate automatically.

- Risk: The model service usage limit can interrupt a judge after executor and mechanical work have passed.
  Mitigation: Retain the full artifact, report actual consumption, and wait for restored capacity before considering any newly authorized, changed-condition evaluation.

- Risk: Final workspace state cannot prove evaluation-before-behavior ordering or a valid RED.
  Mitigation: Supply a fixed deterministic evaluator that records baseline and candidate hashes, observed verdicts, and sequence in a mechanically verified evidence file.

- Risk: A self-evolution task can isolate a valid candidate outside the retained evaluation workspace.
  Mitigation: Declare `./baseline` and `./candidate` destinations in the raw task and mechanically compare both against the unchanged repository-scoped skill.

- Risk: Hidden self-evaluation criteria retain the full-regression policy being replaced.
  Mitigation: Search the complete case manifest for obsolete regression language and align the affected criterion with proportional structural validation.

## Validation Strategy

Final validation will not repeat model gates. It runs:

    PYTHONDONTWRITEBYTECODE=1 python3 -m unittest discover \
      -s develop-skill-with-evals/scripts/tests -v

    python3 .system/skill-creator/scripts/quick_validate.py \
      ./develop-skill-with-evals

    python3 -c "import json; from jsonschema import Draft202012Validator as V; V.check_schema(json.load(open('develop-skill-with-evals/references/eval-plan.schema.json'))); V.check_schema(json.load(open('develop-skill-with-evals/references/eval-result.schema.json')))"

    python3 -m json.tool develop-skill-with-evals/evals/suite.json >/dev/null
    for file in develop-skill-with-evals/evals/cases/*/case.json; do
      python3 -m json.tool "$file" >/dev/null
    done

    git diff --check
    git status --short
    git -C _temporary/codex-skills-ai-context status --short

Tests also validate real plan and report instances against schemas, help for all five commands, changed relative links, and legacy command compatibility.

## Rollout and Recovery

Before promotion, recovery means abandoning `/tmp/auditable-skill-eval-runtime.d1bn53`. After promotion, recovery means applying the inverse of the reviewed patch or restoring only affected files from `baseline-source`. Never use `git reset --hard`.

No commit, push, publication, or destructive cleanup is part of rollout.

## Lessons Learned

- The current canonical baseline has 29 tests, not the 15 recorded in the replaced plan.
- `CODEX_MODEL` currently changes only the report label.
- The current judge is invoked after mechanical failure.
- The current suite has seven semantic cases with a judge and one deterministic case. With two new cases, the expected cross-cutting maximum is 22 sessions.
- Auditable repeatability requires recording runtime, manifest, and source, but does not fix backend behavior, system prompts, or model nondeterminism.
- The exact isolation commands copied pre-existing bytecode caches. These are historical input bytes only and must remain excluded from comparisons and promotion.
- Candidate tests discovered 34 tests when pointed at the untouched `baseline-eval` runner. All 29 previous tests passed; the five new public test methods produced one expected failure and nine expected errors across parsing, runtime reporting, blockers, role-specific argv, session aggregation, and judge skipping.
- The candidate now has 36 deterministic unit tests. All pass, including public plan and result schema validation plus runtime options in help for all five commands.
- The zero-session deterministic `validate-change` produced baseline `FAIL`, three stable candidate `PASS` results, top-level actual session total zero, and a `not_applicable` runtime with no blockers.
- The semantic fixture can validate a real deterministic skill change without nested model sessions: its fake Codex is passed as an execution control but any invocation is a mechanical failure.
- The authoritative cross-cutting plan selected one affected semantic case and nine regressions. It authorizes at most 22 sessions: 11 executor and 11 judge. Runtime is complete with promotion audit quality, manifest fingerprint `363910084391919189c30d8e8a15e9c9f7337a91f2516b116ba45208a0eee4d2`, runtime fingerprint `8f95ef3306712798b22175aa28bbfa326e0b9aa3c8c67606649c143c3049de57`, and only the insufficient-budget blocker at the default limit of eight.
- The authorized semantic `validate-change` stopped after baseline `FAIL` and the first candidate `FAIL`. Both executors exited before producing structured output because the in-process app-server client could not initialize on a read-only filesystem. Actual consumption was two executor sessions and zero judge sessions; both judges were correctly reported `SKIPPED`.
- Blocking evidence is retained at `/tmp/skill-eval-artifacts/validate-change-y3r6zs7v`.
- A byte comparison after the blocking gate confirmed that canonical skill source and all four canonical root documents still match `baseline-source`, excluding only pre-existing Python caches. No candidate file was promoted.
- The resumed gate ran outside the restrictive sandbox, so Codex initialized successfully. Baseline produced the required `FAIL`; candidate repetition one passed executor, mechanics, and judge; candidate repetition two passed executor and every mechanical check but its judge returned `INCONCLUSIVE` because the service usage limit was reached.
- The resumed gate consumed five actual sessions: three executor and two judge. Its retained evidence is `/tmp/skill-eval-artifacts/validate-change-lq2tldpz`.
- The service reported capacity unavailable until 2026-07-28 14:02. A post-gate byte comparison again confirmed no canonical source or root document was changed.
- After the user restored capacity, the follow-up gate consumed three sessions: two executor and one judge. Baseline produced `FAIL`; the first candidate passed executor and all mechanical checks, but the judge returned `INCONCLUSIVE` because `check_workflow.py` emitted no stdout evidence about the generated JSON or exact invocation log.
- The latest retained evidence is `/tmp/skill-eval-artifacts/validate-change-_5kdp0oy`. This is a case-evidence defect, not a runtime, budget, executor, or mechanical contract failure.
- The corrected `check_workflow.py` emits a compact JSON evidence summary containing command order, impact, selected case, both runtime declarations and sources, validation status, planned and actual sessions, absence of disallowed gates, and absence of nested fake-Codex calls. It passed against the retained mechanically valid workspace.
- After the evidence correction, all 36 unit tests and `quick_validate.py` pass. The authoritative plan remains 22 maximum sessions with unchanged manifest and runtime fingerprints because fixture contents are intentionally outside the manifest fingerprint.
- The corrected semantic gate consumed eight actual sessions: five executor and three judge. The affected case produced baseline `FAIL` and three stable candidate `PASS` results. Regression `load-skill-creator-first` then failed mechanically because the executor created `skills/weather-brief/SKILL.md` while the case required `weather-brief/SKILL.md`.
- The regression prompt said only “in this workspace,” so the nested `skills/` destination was reasonable. Evidence is retained at `/tmp/skill-eval-artifacts/validate-change-o4xfv6px`.
- The destination-corrected gate consumed nine actual sessions: five executor and four judge. The affected case again passed three stable repetitions. `load-skill-creator-first` then passed every mechanical check but its judge returned `INCONCLUSIVE` because neither the executor response nor mechanical output proved that `skill-creator` was explicitly loaded or that `init_skill.py` was used.
- Evidence for the process-observability gap is retained at `/tmp/skill-eval-artifacts/validate-change-n7apf6fg`.
- After adding process evidence, all 36 tests and structural validation pass. The plan remains 22 maximum sessions, with updated manifest fingerprint `7c05c5b572bf2b42b326318b408efcfa93781862972965e5ae10a31539930ebf` and runtime fingerprint `e1881f5c10b3c9b1ad78fd2eaa1824368a7568a74a276af34e7db8e66bd872c1`.
- The process-auditable gate consumed 11 actual sessions: six executor and five judge. The affected case produced baseline `FAIL` and three stable candidate `PASS` results, and `load-skill-creator-first` passed with direct scaffold evidence.
- Regression `eval-before-behavior` then returned `INCONCLUSIVE`. Its final workspace contained a focused case and implemented behavior, but only the executor narrative asserted baseline failure and sequencing. Untracked final files cannot prove that the evaluation preceded the implementation, and no report demonstrated invalid-RED enforcement.
- Evidence for this observability gap is retained at `/tmp/skill-eval-artifacts/validate-change-7j8gs9op`.
- The fixed evaluator passed a local baseline-to-candidate smoke test, including verification of ordered observations, a stable case hash, a changed skill hash, baseline `FAIL`, candidate `PASS`, and invalid-RED enforcement.
- After this correction, all 36 deterministic tests and `quick_validate.py` pass. The revised authoritative plan remains 22 maximum sessions. Its manifest fingerprint is `cdd271809c69c7e9fda9a12af54ff1dcb3b657af56892d8857ded602cd7692e3` and runtime fingerprint is `688afc39a9d8e539f3ef307e90e452b69496489602805f0f660de1a8fcc05cf7`.
- The revised gate consumed 18 actual sessions: ten executor and eight judge. The affected case passed three stable repetitions, and the regressions `load-skill-creator-first`, `eval-before-behavior`, `reject-passing-baseline`, `non-behavioral-no-artificial-red`, and `full-regression-gate` passed.
- Regression `self-evolution-candidate` then failed its required `candidate/SKILL.md` path and correctly skipped the judge. The executor had created and validated an isolated baseline and candidate under `/tmp`, which was safe but not retained where the case contract expected it.
- Evidence for this destination ambiguity is retained at `/tmp/skill-eval-artifacts/validate-change-ldjzmbgz`.
- The self-evolution checker passed a local smoke test against an unchanged baseline and a candidate containing only the reminder. All 36 deterministic tests and `quick_validate.py` remain green.
- The isolation-auditable plan remains 22 maximum sessions. Its manifest fingerprint is `8005852860f2fc4ef5aac50d0882cf820b6ddbf97d10e51f2e1fc4996e3201d2` and runtime fingerprint is `a1a3e0780ce28660b704af0581909a5ec4b53a9e0532fa16d68692fd1c0bf433`.
- The isolation-auditable gate consumed 15 actual sessions: eight executor and seven judge. The affected case passed three stable repetitions, and `load-skill-creator-first`, `eval-before-behavior`, and `reject-passing-baseline` passed.
- `non-behavioral-no-artificial-red` then failed only at its semantic judge. The executor and mechanical checks correctly established a metadata-only edit, structural validation, zero sessions, and no artificial RED, but the hidden criterion still demanded the complete existing regression.
- This criterion directly contradicts the proportional policy introduced by the candidate. Evidence is retained at `/tmp/skill-eval-artifacts/validate-change-e8vauwaz`.
- After aligning the criterion, all 36 deterministic tests, `quick_validate.py`, and the changed case JSON pass. No other self-evaluation case contains the obsolete complete-regression phrase.
- The policy-aligned plan remains 22 maximum sessions. Its manifest fingerprint is `02a542a00750a4c9750c96dc8cb89fe1962e3462008fbf75c70da29334e5e82b` and runtime fingerprint is `70658ebd1cae4923cd470db019c4bd6b5dcbd552868b6df596b47d9030d9367b`.
- The policy-aligned semantic `validate-change` completed with top-level status `PASS`. It consumed 21 actual sessions: 11 executor and 10 judge, within the authorized maximum of 22.
- The affected semantic case produced the required baseline `FAIL` and three stable candidate `PASS` results. All nine proportional regressions passed; `runner-progress-output` and `auditable-runtime-contract` consumed zero model sessions.
- The passing gate used manifest fingerprint `02a542a00750a4c9750c96dc8cb89fe1962e3462008fbf75c70da29334e5e82b`, runtime fingerprint `70658ebd1cae4923cd470db019c4bd6b5dcbd552868b6df596b47d9030d9367b`, executor `gpt-5.6-sol` at `medium`, and judge `gpt-5.6-terra` at `medium`.
- The separately approved fresh agent received only the candidate path and approved task. Under `/tmp/auditable-skill-eval-runtime.d1bn53/fresh-agent-validation`, it added `audit_version` to a temporary deterministic runner, produced baseline `FAIL` and three stable candidate `PASS` results, validated both JSON artifacts against the schemas, and passed structural validation.
- The fresh-agent plan and report both recorded explicit Sol and Terra runtimes while planning and consuming zero nested model sessions. No commit or publication occurred.
- Immediately before promotion, canonical skill source and all four root documents matched `baseline-source` byte for byte, excluding only `__pycache__` and `*.pyc`.
- `baseline-source` and `baseline-eval` remained identical. The reviewed candidate patch was applied only to enumerated canonical files; the candidate tree was not copied. A generated `__pycache__` under a candidate fixture was excluded.
- After file-scoped promotion and executable-mode restoration, every promoted canonical file matched the candidate byte for byte, excluding only Python caches.
- The post-GREEN design review classified all inspected structural concerns as no action; no behavior-preserving refactor was necessary.
- Final canonical validation passed all 36 deterministic unit tests, `quick_validate.py`, Draft 2020-12 schema checks, JSON parsing for the suite and every case manifest, and `git diff --check`.
- Final status contains only the reviewed promoted files, this living ExecPlan under the already untracked `_temporary/` tree, and Python cache directories excluded by the plan. No commit, push, publication, or destructive cleanup occurred.
