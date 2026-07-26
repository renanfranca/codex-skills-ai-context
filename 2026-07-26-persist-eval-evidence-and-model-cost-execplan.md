# Persist Eval Evidence and Compare Model Cost

This ExecPlan is a living document. Keep `Progress`, `Decisions`, `Risks and Mitigations`, and `Lessons Learned` current as work advances.

## Purpose / Big Picture

Make every executed skill evaluation auditable after its disposable workspace is removed. An operator who supplies `--report-dir` will receive canonical JSON evidence and deterministic Markdown, including runtime provenance, observed token usage, duration, sanitized changes, the executor's concise account of its work, mechanical checks, oracle and judge results, and an optional API price reference.

Use that evidence for a controlled pilot across Sol, Terra, and Luna. The pilot compares observed quality, stability, consumption, duration, and API reference estimates. It must not claim that an authenticated ChatGPT session incurred an API charge and must not change the default runtime automatically.

## Scope

In scope:

- Add `--report-dir` and `--pricing-file` to executed runner operations while retaining compatibility when they are absent.
- Persist `<report-dir>/<operation-id>/report.json` atomically before successful workspace cleanup.
- Render `report.md` deterministically from `report.json`.
- Capture executor and judge duration and all exposed usage fields, including reasoning output tokens.
- Record sanitized Codex CLI version, authentication mode, runner digest, fingerprints, structured executor explanation, changed files, limited diff and limited fragments.
- Add a deterministic report renderer and model report comparator.
- Add deterministic unit contracts and one semantic self evaluation named `execution-evidence-report`.
- Prepare and, only after explicit approval, run an 18 session pilot covering two cases, three executor models, and three repetitions.
- Recommend a runtime from the pilot without modifying runtime policy.

Out of scope:

- Result caching or reuse in promotion.
- Judge model comparison in the first pilot.
- Automatic runtime presets or default changes.
- Claims about actual ChatGPT billing.
- Versioning generated reports, pricing snapshots, raw JSONL, full transcripts, or generated model responses.
- A broad case, model, or reasoning effort matrix.

Safety boundary: This task is limited to authorized, defensive maintenance of this repository. Do not expose private reasoning, credentials, raw transcripts, or hidden oracle contents.

## Definitions

- **Execution evidence:** normalized, persisted evidence of an observation that actually ran.
- **Observation:** one planned case execution for a specific source, role, and repetition.
- **API reference estimate:** a calculation that applies an explicitly supplied, dated API price table to observed tokens. It is not a statement of ChatGPT billing.
- **Qualifying model:** a model with three stable `PASS` results in both pilot cases.
- **Effective reference cost:** summed API reference estimates divided by valid stable gates. Failed or unstable observations remain in total cost.
- **Provenance:** origin of an observation. This change supports only `executed`; future reuse is reserved.
- **Report digest:** SHA-256 over canonical JSON with the digest field omitted.

## Existing Context

The canonical source is `/home/renanfranca/.codex/skills` at commit `de90eea09769cbc634ae7bcbe6bad2ebe47fb9eb`. The skill tree object is `f523238902b647fef05123d1f9ee98d11e134dc9`. The canonical runner SHA-256 is `21665c13b50ee59fefd3f3becd2315a1b7c7e9c623e8fe71b06cc7196a51bc11`.

Development uses:

- immutable source: `/tmp/persist-eval-evidence.auoqZx/baseline-source`
- immutable evaluated baseline: `/tmp/persist-eval-evidence.auoqZx/baseline-evaluation/develop-skill-with-evals`
- writable candidate: `/tmp/persist-eval-evidence.auoqZx/candidate-source/develop-skill-with-evals`

The two baseline directories are mode `0555`. The candidate was copied byte for byte from the same commit before edits.

The environment reports `codex-cli 0.145.0` and sanitized authentication state `ChatGPT`. The canonical worktree already contained untracked `_temporary/` and Python cache directories; those are user or prior work and are not part of the candidate patch.

The runner currently aggregates input, cached input, and output tokens from Codex JSONL but does not retain reasoning output tokens, durations, CLI version, authentication mode, or price estimates. Successful operation workspaces are removed, so later report generation cannot reconstruct changed content or diffs.

The proposed runner and contract changes affect central evaluation behavior and are therefore `cross-cutting`.

## Desired End State

Executed operations accept:

    --report-dir <directory>
    --pricing-file <json>

When `--report-dir` is present, an operation creates:

    <report-dir>/<operation-id>/report.json
    <report-dir>/<operation-id>/report.md

`report.json` includes operation status and workflow; role and repetition; skill, case, source, runtime, manifest, and evaluation fingerprints; Codex CLI version; sanitized authentication mode; runner digest; model and reasoning effort per role; planned and executed sessions; provenance; timestamps and durations; token usage and completeness; an optional applied pricing snapshot and its limitations; an API reference estimate with `actual_charge: false` under ChatGPT; the case prompt; structured executor response; mechanical, oracle, and judge facts; eligible changed files; sanitized limited diff; limited text fragments; and its own digest.

The executor response contract includes `diagnosis`, `approach`, `decisions`, `rejected_alternatives`, `key_changes`, and `validation`. Arrays may be empty. Prompts ask for concise records of decisions actually made and explicitly prohibit private reasoning reconstruction.

Evidence excludes `.git/**`, `.agents/skills/**`, `.eval-*`, `**/__pycache__/**`, and `*.pyc`. Per file and per report limits are deterministic and every truncation is declared.

`render_eval_report.py` reconstructs Markdown only from canonical JSON. `compare_model_reports.py` reads a report directory and writes deterministic machine and Markdown summaries of pass rate, stability, tokens, cache ratio, duration, API reference estimate, and explanation completeness.

Without `--report-dir`, existing stdout JSON, cleanup, command compatibility, and schemas continue to work.

## Final audit dossier

The Portuguese, self-contained audit and economic evolution policy is consolidated in [2026-07-26-skill-eval-evidence-and-cost-dossier.md](2026-07-26-skill-eval-evidence-and-cost-dossier.md). It preserves the safe, auditable facts needed after the `/tmp` evidence expires without copying raw model responses, JSONL, transcripts, private reasoning, credentials, or hidden oracle contents.

## Milestones

### Milestone 1: Freeze sources and record provenance

#### Goal

Protect the canonical skill and make every later comparison reproducible.

#### Changes

- Create this ExecPlan in the context repository.
- Derive immutable source and baseline plus a writable candidate from canonical commit `de90eea`.
- Record source hashes, CLI version, sanitized authentication, worktree state, and cross cutting classification.

#### Validation

- Command: `sha256sum /tmp/persist-eval-evidence.auoqZx/baseline-evaluation/develop-skill-with-evals/scripts/run_skill_evals.py /tmp/persist-eval-evidence.auoqZx/candidate-source/develop-skill-with-evals/scripts/run_skill_evals.py`
- Expected result: identical hashes before candidate edits.
- Command: `find /tmp/persist-eval-evidence.auoqZx -maxdepth 2 -type d -printf '%m %p\n'`
- Expected result: baseline directories are read only and candidate directories are writable.

#### Acceptance Criteria

- The canonical skill is unchanged.
- No model session has run.

### Milestone 2: Add deterministic contracts before behavior

#### Goal

Define observable report, sanitization, pricing, rendering, comparison, cleanup, and compatibility behavior before implementation.

#### Changes

- Add focused unit tests under `/tmp/persist-eval-evidence.auoqZx/candidate-source/develop-skill-with-evals/scripts/tests/`.
- Add `execution-evidence-report` to the candidate suite with a minimal fake Codex fixture and a hidden complete oracle.
- Update candidate schemas only where the new canonical evidence contract requires it.

#### Validation

- Command: `PYTHONDONTWRITEBYTECODE=1 python3 -m unittest discover -s /tmp/persist-eval-evidence.auoqZx/candidate-source/develop-skill-with-evals/scripts/tests -v`
- Expected result before implementation: existing tests pass and focused new tests fail for missing behavior.
- Run focused new tests against `/tmp/persist-eval-evidence.auoqZx/baseline-evaluation/develop-skill-with-evals`.
- Expected result: specific RED without a real Codex invocation.

#### Acceptance Criteria

- Tests cover durations and usage fields, unavailable CLI metadata, sanitization and truncation, persistence before cleanup, deterministic Markdown, API reference semantics, comparison, and no flag compatibility.
- No model session has run.

### Milestone 3: Implement durable execution evidence

#### Goal

Produce durable, sanitized, deterministic reports without changing existing stdout behavior.

#### Changes

- Modify candidate `scripts/run_skill_evals.py`.
- Add candidate `scripts/render_eval_report.py`.
- Add candidate `scripts/compare_model_reports.py`.
- Update candidate `references/eval-result.schema.json`, `references/eval-plan.schema.json` only if needed, `references/eval-contract.md`, and `SKILL.md`.
- Capture eligible file state before and after execution, generate evidence before cleanup, write canonical JSON atomically, then render Markdown.
- Record consumption before evidence persistence so a report failure cannot deliberately undercount an executed session.

#### Validation

- Run focused unit tests.
- Regenerate Markdown twice from one JSON and compare bytes.
- Verify a `PASS` report survives workspace removal.
- Validate pricing behavior with explicit test snapshots and ChatGPT authentication.

#### Acceptance Criteria

- All deterministic tests pass.
- Existing behavior without report flags remains compatible.
- The fake Codex semantic case produces one baseline RED and three stable candidate GREEN results at zero real model cost.

### Milestone 4: Prepare and run the controlled pilot

#### Goal

Collect 18 fresh executor observations after a separate explicit approval.

#### Changes

- Create a dated pricing snapshot outside Git from current official API documentation.
- Generate an authoritative inventory for cases `load-skill-creator-first` and `explicit-runtime-promotion-workflow`; models `gpt-5.6-sol`, `gpt-5.6-terra`, and `gpt-5.6-luna`; effort `medium`; three fresh repetitions; no judge.
- Counterbalance model order and assign a unique report directory to every operation.
- Stop and request approval for the exact 18 session maximum before execution.
- After approval, run every inventory entry exactly once with provenance `executed`.
- Run `compare_model_reports.py` and inspect exactly 18 observations.

#### Validation

- Command: candidate runner plans and inventory validation commands recorded with the inventory.
- Expected result: 18 planned executor sessions, zero judge sessions, no reuse.
- Command: candidate comparator over pilot reports.
- Expected result: exactly 18 executed observations with complete status, runtime, usage completeness flags, duration, and report digests.

### Milestone 3b: Preserve normalized usage events and guard long context pricing

#### Goal

Preserve each sanitized Codex JSONL usage event without retaining raw JSONL, identify the event's scope, and prevent an aggregate turn total from being priced as though it were one API request. This milestone is deterministic and does not consume a model session.

#### Changes

- [x] Extend `scripts/run_skill_evals.py` usage parsing with ordered normalized events, source event type, scope, completeness, and aggregate compatibility fields.
- [x] Extend `scripts/eval_report.py` pricing validation and estimation with an optional machine readable long context rule.
- [x] Mark the monetary amount unavailable when a turn scoped aggregate crosses a request scoped pricing threshold; preserve a clearly labeled base rate reference instead.
- [x] Extend `references/eval-result.schema.json`, the renderer, comparator, tests, and concise skill guidance.
- [x] Update the external pricing snapshot with the documented threshold and multipliers.

#### Validation

- [x] Command: focused evidence report tests.
- [x] Expected result: normalized event evidence is persisted and long context ambiguity produces no complete monetary amount.
- [x] Command: full deterministic test suite and structural validation.
- [x] Expected result: all tests and schemas pass, with no real model invocation.

#### Acceptance Criteria

- [x] Raw JSONL and transcripts remain absent.
- [x] Existing aggregate token fields remain compatible.
- [x] A subthreshold event retains the existing estimate behavior.
- [x] An above threshold turn aggregate produces `amount: null`, an explicit indeterminate status, and a base rate reference.
- [x] A new pilot inventory is generated only after the candidate and pricing digests are refreshed.

#### Acceptance Criteria

- A model qualifies only with three stable `PASS` results for both cases.
- Results are described as directional and no runtime default changes.

### Milestone 5: Promotion and forward validation

#### Goal

Promote only a reviewed, byte equivalent patch after one approved cross cutting campaign and a clean fresh agent exercise.

#### Changes

- Repair renderer only through deterministic replay when possible.
- Generate an authoritative promotion plan with Sol executor and Terra judge at `medium`.
- Stop and request separate approval for the exact plan maximum.
- Run one approved `validate-change`.
- Run one fresh agent task using only the candidate path and a realistic reporting request.
- Review the patch and apply it file by file to canonical source only after all gates pass.

#### Validation

- Run the full deterministic suite, skill validation, JSON schema validation, `git diff --check`, promotion gate, and fresh agent exercise.
- Compare promoted files byte for byte with the candidate.

#### Acceptance Criteria

- Promotion status is `PASS` and stable.
- Fresh agent produces and inspects a report without leaked expected answers.
- Canonical patch is byte equivalent, contains no generated reports or model outputs, and leaves runtime defaults unchanged.

## Progress

- [x] ExecPlan created.
- [x] Canonical commit, skill tree, runner digest, CLI version, authentication mode, and worktree state recorded.
- [x] Immutable baseline source, immutable evaluated baseline, and writable candidate isolated.
- [x] Deterministic report contracts added before implementation.
- [x] Semantic `execution-evidence-report` case added before implementation.
- [x] Baseline RED demonstrated without real model use.
- [x] Evidence and reporting implemented in candidate.
- [x] Deterministic validation and fake Codex GREEN complete.
- [x] Authoritative 18 session pilot inventory generated and explicitly approved.
- [x] Pilot complete and comparison generated. Pilot v1 stopped after one pre-telemetry observation; pilot v2 completed 18 valid observations under the approved cumulative maximum of 19 invocations.
- [x] Normalized usage event telemetry and long context pricing guard implemented deterministically.
- [x] Replacement 18 session pilot inventory explicitly approved.
- [x] Replacement pilot complete. `run-v2-01` stopped on sandbox infrastructure; its approved replacement and the remaining fixed order produced 18 valid observations.
- [x] One replacement invocation approved, raising the v2 cumulative maximum from 18 to 19.
- [x] Promotion plan generated and explicitly approved for a maximum of 15 sessions.
- [x] First promotion attempt stopped on deterministic `runner-progress-output` regression after 11 sessions.
- [x] Python cache snapshot defect reproduced RED, corrected, and replayed GREEN with zero model sessions.
- [x] Second promotion completed with PASS using the explicitly approved maximum of 15 sessions.
- [x] Fresh agent validation complete.
- [x] Reviewed patch promoted byte for byte.
- [x] Runtime recommendation recorded without automatic runtime change.
- [x] Final audit dossier written, reconciled, and linked.

## Decisions

- Decision: Treat the change as cross cutting.
  Rationale: It changes the central runner, executor contract, schemas, cleanup order, and shared evaluation guidance.
  Date/Author: 2026-07-26 / Codex

- Decision: Measure API pricing only as an explicit dated reference.
  Rationale: ChatGPT authentication exposes plan credits and limits, not a per execution API charge.
  Date/Author: 2026-07-26 / Codex

- Decision: Compare only executor models in the pilot.
  Rationale: The selected cases use complete oracles and the executor accounts for the sessions being compared.
  Date/Author: 2026-07-26 / Codex

- Decision: Require three stable passes for each of two cases.
  Rationale: The pilot needs minimal repeatability evidence while remaining directional rather than statistically conclusive.
  Date/Author: 2026-07-26 / Codex

- Decision: Do not implement cache or change runtime defaults.
  Rationale: Evidence persistence and runtime policy are separable decisions, and two cases cannot justify an automatic global policy.
  Date/Author: 2026-07-26 / Codex

- Decision: Put nested self evaluation artifacts under `.eval-*`.
  Rationale: Report operation IDs are intentionally unique, so generated report paths must remain harness artifacts rather than outcome paths in stability signatures.
  Date/Author: 2026-07-26 / Codex

- Decision: Redact common credential patterns in addition to path exclusions and size limits.
  Rationale: An eligible changed file can still contain an API key, bearer token, password, or secret.
  Date/Author: 2026-07-26 / Codex

- Decision: Stop the pilot before `run-02`.
  Rationale: `run-01` reported 1,060,145 aggregate input tokens, exceeding the inventory's explicit 272,000 token stop threshold. The persisted aggregate does not establish whether any individual API request crossed the documented long context threshold, so applying the multiplier or claiming a complete monetary estimate would not be auditable.
  Date/Author: 2026-07-26 / Codex

- Decision: Preserve normalized usage events rather than raw JSONL.
  Rationale: Pricing diagnostics need event boundaries and scope, while raw event streams and transcripts exceed the evidence retention and security boundary.
  Date/Author: 2026-07-26 / Codex

- Decision: Treat `turn.completed` usage as turn scoped, not request scoped.
  Rationale: The current Codex CLI manual attaches usage to `turn.completed` and documents no request level usage event. It does not prove individual API request sizes, so a request threshold cannot be applied auditably to that event.
  Date/Author: 2026-07-26 / Codex

- Decision: Invalidate the first promotion observations after the snapshot correction.
  Rationale: The correction changes the candidate source fingerprint. Reusing observations from the prior fingerprint would violate the promotion contract even though the failure itself was deterministic.
  Date/Author: 2026-07-26 / Codex

## Risks and Mitigations

- Risk: API estimates could be mistaken for ChatGPT billing.
  Mitigation: Require an explicit pricing file and record billing mode, source, effective date, limitations, and `actual_charge: false`.

- Risk: Reports could leak installed skills, hidden oracles, raw transcripts, credentials, or irrelevant files.
  Mitigation: Use an evidence allowlist, explicit exclusions, text and byte limits, truncation markers, sanitized authentication, and no raw JSONL persistence.

- Risk: A structured explanation could be mistaken for private reasoning or contradict the changed files.
  Mitigation: Request concise decision records only, prohibit reconstructed private reasoning, and keep executor declarations separate from mechanical facts.

- Risk: A cheaper model could pass by chance or benefit from execution order.
  Mitigation: Require three fixed fresh repetitions, stable signatures, counterbalanced order, timestamps, and cached input measurement with no opportunistic retry.

- Risk: Report persistence could fail after a paid session.
  Mitigation: Record ledger consumption before attempting evidence persistence and surface report failure as an operation error.

- Risk: A renderer defect could waste model sessions.
  Mitigation: Make Markdown a deterministic replay from JSON and do not repeat model work for presentation defects.

- Risk: The total campaign can reach the pilot's 18 sessions plus the authoritative promotion maximum and one fresh agent.
  Mitigation: Use separate explicit approvals and stop immediately on any infrastructure or contract blocker.

- Risk: API pricing has a higher rate for prompts above 272,000 input tokens and separate cache write pricing.
  Mitigation: Record both limitations in the dated snapshot and do not claim cache write estimates when JSONL does not expose them. The attempt to keep the pilot below the threshold failed on `run-01`, so the campaign stopped before another session.

- Risk: Python imports can rewrite cache files inside an evaluated skill and create a false self modification failure.
  Mitigation: Exclude `__pycache__` and `*.pyc` consistently from workspace snapshots, source immutability checks, stability signatures, and persisted evidence while retaining checks for source files.

## Validation Strategy

Run from narrow to broad:

1. `PYTHONDONTWRITEBYTECODE=1 python3 -m unittest discover -s /tmp/persist-eval-evidence.auoqZx/candidate-source/develop-skill-with-evals/scripts/tests -v`
2. `python3 /home/renanfranca/.codex/skills/.system/skill-creator/scripts/quick_validate.py /tmp/persist-eval-evidence.auoqZx/candidate-source/develop-skill-with-evals`
3. `python3 -m json.tool /tmp/persist-eval-evidence.auoqZx/candidate-source/develop-skill-with-evals/references/eval-plan.schema.json >/dev/null`
4. `python3 -m json.tool /tmp/persist-eval-evidence.auoqZx/candidate-source/develop-skill-with-evals/references/eval-result.schema.json >/dev/null`
5. Regenerate Markdown twice from identical JSON and compare bytes.
6. Exercise a successful fake Codex operation and confirm its workspace is gone while both reports remain.
7. Inspect the candidate diff for leaked fixtures, transcripts, generated responses, reports, pricing snapshots, or caches.
8. Run `git diff --check` after promotion.

No real model operation occurs before its exact plan and approved maximum are shown to the user.

## Rollout and Recovery

Before promotion, recovery means abandoning `/tmp/persist-eval-evidence.auoqZx/candidate-source`; canonical source remains unchanged. After promotion, recovery means applying the reviewed inverse patch only to files in scope. Never use `git reset --hard`.

Generated reports, pricing snapshots, ledgers, and pilot comparisons remain outside version control. Only source, schemas, deterministic tests, minimal cases, hidden oracles, and concise skill guidance may be promoted.

## Lessons Learned

- Initial provenance confirms the environment uses ChatGPT authentication, so reference pricing must remain explicitly separate from actual billing.
- The existing worktree has unrelated untracked caches and a context repository. Isolation prevents them from contaminating source fingerprints or the eventual patch.
- Focused report tests fail specifically because executed commands do not yet recognize `--report-dir` and `--pricing-file`; renderer and comparator scripts are absent.
- A local fake outer executor exercised `execution-evidence-report` against both isolated sources. The baseline failed because the nested runner rejected the new flags, leaving no persisted report or replayed Markdown. This consumed two simulated invocations and zero real model sessions.
- The first isolation placed skill contents directly in directories named `baseline-eval` and `candidate`. Because repository-scoped installation uses the source directory name, self evaluation fixtures could not resolve `develop-skill-with-evals`. New container roots now preserve the canonical skill directory name; the original copies remain untouched as an audit trail.
- Read only directory modes are preserved by `copytree`. Successful cleanup now makes only paths inside the disposable operation root writable before retrying removal; the immutable baseline itself is unchanged.
- The semantic fake gate completed one baseline RED and three stable candidate GREEN observations. The outer and nested commands were local fakes, so the reported four sessions are simulated harness invocations, not real model consumption.
- Official model pages on 2026-07-26 list per million token API rates of 5.00/0.50/30.00 USD for Sol, 2.50/0.25/15.00 for Terra, and 1.00/0.10/6.00 for Luna. The snapshot records long prompt and cache write limitations.
- Common credential shapes require content redaction even when path exclusions and truncation are correct.
- The user approved the authoritative maximum of 18 sessions. The first real observation passed its mechanical oracle but used 1,060,145 aggregate input tokens, including 980,992 cached input tokens, plus 12,554 output tokens and 3,876 reasoning output tokens over 342,108 ms.
- Aggregate session usage is insufficient to apply a per request long context pricing rule. Raw JSONL was intentionally not retained, so `run-01` cannot be reconstructed into request level usage after the fact. Its 1.262881 USD value is a base rate API reference with an explicit limitation, not an auditable threshold adjusted estimate and not an actual ChatGPT charge.
- Exactly one real model session was consumed before the stop; `run-02` was not started.
- The current Codex manual documents usage on `turn.completed` and does not document request level token events. The new telemetry therefore records the event as turn scoped and refuses to infer request sizes.
- The report and comparator previously implemented the same base rate formula separately. The design review consolidated comparison through `api_reference_estimate`, preventing long context handling from diverging by output path.
- Focused tests first failed on the absent normalized event fields. After implementation, 57 deterministic tests pass, structure and schemas validate, and the fake public gate produces one baseline RED plus three stable candidate GREEN observations with zero real sessions.
- Pilot inventory v2 excludes the pre-telemetry `run-01`, requests 18 new executor sessions, uses no judge or reuse, and stops on missing or unexpected normalized usage. Completing it would bring total real pilot-related consumption to 19 sessions.
- The approved v2 preflight matched every digest, case, runtime, CLI, and authentication field. Its first executor invocation failed in 141 ms because the Codex CLI could not initialize the in-process app-server client on a read-only filesystem. The runner conservatively counted one executor invocation, while usage remained incomplete with zero events and no tokens. The campaign stopped before `run-v2-02`.
- Retrying outside the sandbox is an infrastructure correction, but it would be a nineteenth invocation under inventory v2. It requires both a revised model session maximum and separate elevated shell approval; neither can be inferred from the original 18 session approval.
- Pilot v2 completed with 16 PASS and 2 contract FAIL observations. Terra omitted a required session budget argument once and omitted the fake runner invocation ledger once; both failures remain in the comparison with no retries.
- No model qualified. Sol and Luna passed all six observations but each produced three distinct outcome signatures in `load-skill-creator-first`. Terra passed four of six and was unstable in both cases.
- Luna used 39.6% of Sol's input tokens, 46.3% of its duration, and 10.9% of its base rate reference. This makes Luna the strongest follow-up candidate, not a promotable runtime from this pilot.
- Seventeen of eighteen observations were long context indeterminate. Only one Luna observation remained below the threshold and produced a complete API reference estimate. No reported amount is an observed ChatGPT charge.
- The authoritative cross cutting promotion plan selects one affected baseline execution, three affected candidate executions, and eleven candidate regressions. It requires at most 12 Sol executor and 3 Terra judge sessions. Its only blocker is the default eight session operation limit; candidate, runtime, manifest, and evaluation fingerprints are complete.
- The first promotion attempt stopped after 8 Sol executor and 3 Terra judge sessions. Seven executed gates passed after the expected baseline RED; the deterministic `runner-progress-output` regression failed because a nested runner import rewrote `scripts/__pycache__/*.pyc` inside the materialized candidate while the immutability snapshot still included Python caches.
- A focused test reproduced the cache mismatch before the fix. Excluding `__pycache__` and `*.pyc` from the general snapshot made that test, the existing real source mutation test, the complete deterministic suite of 58 tests, and a zero session replay of `runner-progress-output` pass. The candidate fingerprint changed from `41ec968e...` to `19619522...`, so no prior promotion observation is eligible for reuse.
- The second promotion executed exactly 12 Sol executor and 3 Terra judge sessions with provenance `executed`. The expected baseline RED, three candidate GREEN observations, all eleven regressions, and structural validation passed. The canonical report contains 15 complete normalized usage events, 4,925,286 input tokens, 4,319,232 cached input tokens, 60,079 output tokens, 14,439 reasoning output tokens, and 1,771,521 ms total duration.
- The promotion report digest verified and deterministic Markdown replay was byte identical. Its 6.992256 USD value is only a base rate API reference; the exact amount remains unavailable because nine turn scoped events exceeded the request scoped long context threshold. `actual_charge` remains false under ChatGPT authentication.
- A separately approved fresh agent used only the public fixture and fake Codex. It produced one PASS report with one executed fake session, verified its digest, schema, sanitization, API reference limitations, removed workspace, and byte identical Markdown replay. It found no contradiction or leak; runner stdout named a sanitized harness path while canonical evidence correctly omitted it.
- The reviewed patch was applied to the canonical skill for exactly 20 files. Candidate and canonical bytes match for every promoted file, executable modes match for the fixture helpers and hidden oracle, and preexisting canonical fixtures absent from the evaluated candidate were preserved. On canonical source, all 58 tests, structural validation, both JSON schemas, and `git diff --check` pass.
- Runtime defaults remain unchanged. The directional recommendation remains to test Luna on a broader stable suite before any policy change; no model qualified in this pilot.
- The historical `7,002,781 ms` duration total is reproducible only by mixing pilot observation duration with operation duration from the other phases. The final dossier preserves that historical figure, documents its exact formula, and also records homogeneous operation-level and observation-level totals.
- Record during implementation which Codex JSONL usage fields are stable, how much structured explanation changes output tokens, whether report evidence supports future judge replay, and how model choice affects tokens, stability, duration, and effective reference cost.
