# Make Refactor Design Coverage Traceable

This ExecPlan is a living document. Keep `Progress`, `Decisions`, `Risks`, and `Lessons Learned` up to date as work advances.

## Purpose / Big Picture

Turn the `refactor-design` evaluation suite into an incremental, mechanically traceable coverage contract without changing the skill's behavior. A contributor can observe the result by running the zero-session `coverage-contract` case: it rejects stale source fingerprints, unmapped normative contracts, incomplete rubric sampling, invalid evidence declarations, duplicate coverage dimensions, and orphaned cases.

## Scope

In scope are `refactor-design/evals/`, the evaluation guidance in `EVALUATIONS.md`, Refactor Design examples in `README.md` and `CODEX_CLI.md`, and `refactor-design/agents/openai.yaml`. The skill behavior in `refactor-design/SKILL.md`, its two references, `AGENTS.md`, and `seed4j-execplan-tdd` are out of scope and must remain unchanged.

## Definitions

`coverage.json` is the source of truth that binds source fingerprints, normative skill contracts, rubric section families, case dimensions, evidence types, and guarantee levels.

A coverage dimension is the distinct observation mode for one contract, such as explicit invocation, implicit invocation, or Java technology.

A deterministic case consumes no executor or judge session and proves a complete contract with code.

A semantic case uses an executor and, here, a judge to assess context dependent behavior. It samples behavior but does not prove universal correctness.

## Existing Context

`refactor-design/evals/suite.json` currently lists six semantic cases. The general rubric has an introductory usage section plus fifteen named design risk sections. The Java rubric has an introductory scope section plus twelve named sections. The untouched baseline is `/tmp/refactor-design-baseline.06k8Ny/refactor-design`.

## Desired End State

The suite lists exactly twelve cases: one deterministic coverage contract and eleven semantic cases. `coverage.json` fingerprints `SKILL.md` and both references, maps every normative contract and every rubric section, and passes a checker that also demonstrates rejection of deliberately corrupted temporary copies. Documentation describes what the guarantees do and do not mean. Examples state the green entry state, authorized scope, public contract, smallest justified refactor, repeated validation, and exception gates.

## Milestones

### Milestone 1 - Establish the deterministic coverage contract

#### Goal

Create the manifest, deterministic case, checker, and twelve case suite shape.

#### Changes

- [ ] Add `refactor-design/evals/coverage.json`.
- [ ] Add `refactor-design/evals/cases/coverage-contract/case.json`.
- [ ] Add the deterministic checker fixture.
- [ ] Update `refactor-design/evals/suite.json`.

#### Validation

- [ ] Command: run the deterministic case against `/tmp/refactor-design-baseline.06k8Ny/refactor-design`.
- [ ] Expected result: baseline `FAIL` with zero model sessions.
- [ ] Command: run the deterministic case three times against `refactor-design`.
- [ ] Expected result: three `PASS` results with zero model sessions.

#### Acceptance Criteria

- [ ] Every source fingerprint, normative contract, rubric section, case, dimension, evidence type, and partial limitation is checked.
- [ ] Corrupted temporary copies are rejected for every requested negative condition.

### Milestone 2 - Strengthen and expand semantic cases

#### Goal

Represent the eight new or modified semantic scenarios with minimal generic fixtures and explicit false positives.

#### Changes

- [ ] Strengthen `hidden-invocation-state`.
- [ ] Replace the duplicated `implicit-trigger-smoke` fixture.
- [ ] Expand `no-self-modification`.
- [ ] Add `exception-gates`.
- [ ] Add lifecycle, boundary, and cohesion calibration cases.
- [ ] Add a compilable Java hexagonal mapping case.

#### Validation

- [ ] Command: validate every case manifest through runner planning.
- [ ] Expected result: all cases load and the diagnostic plan reports at most 32 model sessions.
- [ ] Command: inspect fixtures for generated responses, personal data, hidden answers, and build outputs.
- [ ] Expected result: none are present.

#### Acceptance Criteria

- [ ] The suite contains one deterministic and eleven semantic cases.
- [ ] Calibration prompts include meaningful false positives and require contextual judgment.
- [ ] Java fixture compiles and its public tests protect behavior.

### Milestone 3 - Update documentation and metadata

#### Goal

Explain coverage limits and align every Refactor Design invocation example.

#### Changes

- [ ] Update `EVALUATIONS.md`.
- [ ] Update Refactor Design examples in `README.md` and `CODEX_CLI.md`.
- [ ] Regenerate `refactor-design/agents/openai.yaml`.

#### Validation

- [ ] Command: regenerate metadata with `generate_openai_yaml.py`.
- [ ] Expected result: short metadata remains consistent with the skill and examples.

#### Acceptance Criteria

- [ ] Documentation separates mechanical guarantees, semantic judgment, normative completeness, and representative rubric sampling.
- [ ] Examples include entry state, scope, public contract, minimal refactor, repeated validation, and exception gates.

### Milestone 4 - Validate and preserve evidence

#### Goal

Run structural checks, validate archive projections, and determine whether the planned model backed diagnostic is authorized and feasible.

#### Changes

- [ ] Record deterministic RED and three GREEN results.
- [ ] Plan the requested semantic diagnostic with Sol medium and Terra medium.
- [ ] Run the diagnostic only after separate model session cost authorization and healthy preflight.
- [ ] Validate skill structure, JSON, diff whitespace, archive, and fixture hygiene.

#### Validation

- [ ] Command: `python3 .system/skill-creator/scripts/quick_validate.py ./refactor-design`
- [ ] Command: `git diff --check`
- [ ] Command: archive rebuild and validation.
- [ ] Expected result: all structural checks pass; any omitted paid diagnostic is disclosed as an unexecuted gate.

#### Acceptance Criteria

- [ ] No behavior file changed.
- [ ] Deterministic evidence is stable.
- [ ] Final report distinguishes executed evidence from planned or blocked evidence.

### Milestone 5 - Clarify coverage and execution status

#### Goal

Make the evaluation guidance distinguish declared coverage, mechanical integrity, executed semantic evidence, and rubric sampling, then record the observed `refactor-design` execution state without implying that unexecuted cases passed.

#### Changes

- [x] Replace the conceptual coverage prose in `EVALUATIONS.md` with a compact guarantee and evidence table.
- [x] State how `complete` and `partial` differ from execution statuses and how representative rubric coverage affects case design but not required suite execution.
- [x] Add a dated `refactor-design` state table linked only to durable repository evidence.

#### Validation

- [x] Command: `git diff --check`
- [x] Command: `git diff -- EVALUATIONS.md _temporary/codex-skills-ai-context/2026-07-27-refactor-design-coverage-execplan.md`
- [x] Expected result: no whitespace errors; the diff does not claim that the eight new or modified semantic cases passed and the new state table contains no ephemeral `/tmp` links.

#### Acceptance Criteria

- [x] No coverage declaration is presented as execution evidence.
- [x] Links resolve to `coverage.json`, `suite.json`, and the archived six case report.
- [x] Future semantic results can update the state table without rewriting the conceptual explanation.

### Milestone 6 - Ground coverage guidance in `refactor-design`

#### Goal

Make `refactor-design` the running example inside the conceptual coverage section so a reader can connect each evidence layer to the real suite before reaching the later detailed status table.

#### Changes

- Name `refactor-design` in the section introduction.
- Give every conceptual table row a concrete `refactor-design` application.
- End with the current high level conclusion and direct readers to the dated example for detailed execution state.

#### Validation

- Command: `git diff --check`
- Command: `git diff -- EVALUATIONS.md _temporary/codex-skills-ai-context/2026-07-27-refactor-design-coverage-execplan.md`
- Expected result: `refactor-design` appears in the introduction, every table row, and the conclusion without presenting declared coverage as semantic qualification.

#### Acceptance Criteria

- The conceptual section is understandable without first reading the later example.
- The dated table remains the only detailed execution status source.
- The eight new or modified semantic cases remain explicitly unqualified.

## Progress

- [x] Baseline preserved.
- [x] Milestone 1 started.
- [x] Milestone 1 completed.
- [x] Milestone 2 started.
- [x] Milestone 2 completed.
- [x] Milestone 3 started.
- [x] Milestone 3 completed.
- [x] Milestone 4 started.
- [x] Milestone 4 completed.
- [x] Milestone 5 started.
- [x] Milestone 5 completed.
- [x] Milestone 6 started.
- [x] Milestone 6 completed.

## Decisions

- Decision: Keep the working ExecPlan in `/tmp` during execution, then copy the completed record into `_temporary/codex-skills-ai-context` at the user's request.
  Rationale: The repository guidelines exclude auxiliary process documentation from normal skill contributions, while the implementation skill requires a living plan. The context repository is the explicit destination for the completed record.
  Date/Author: 2026-07-27 / Codex

- Decision: Treat all headings in both rubric references as coverage units, including their introductory usage or scope sections.
  Rationale: The requirement says every rubric section must be mapped exactly once; excluding introductory normative guidance would create an undocumented exception.
  Date/Author: 2026-07-27 / Codex

- Decision: Exclude only `Contents` navigation headings from rubric coverage.
  Rationale: Navigation contains no review instruction, while every substantive `##` section is extracted and checked dynamically.
  Date/Author: 2026-07-27 / Codex

- Decision: Make ambiguous empty workflow state an exception gate in boundary calibration.
  Rationale: Making that state observable would require an unauthorized public contract; rewarding an internal rewrite would conflict with the skill.
  Date/Author: 2026-07-27 / Codex

- Decision: Stop the semantic diagnostic after the strengthened negative control still passed `hidden-invocation-state`.
  Rationale: Sol independently corrected the concrete code smell despite deliberately weakened instructions. Further prompt manipulation would make the control artificial, consume more sessions, and would not provide trustworthy RED evidence.
  Date/Author: 2026-07-27 / Codex

- Decision: Separate coverage declarations from execution results in both the conceptual guidance and the dated example.
  Rationale: `complete` and `partial` describe the reach claimed by manifest mappings, while `PASS`, `FAIL`, and other runner statuses describe observed executions. Combining them makes an unexecuted semantic contract look qualified.
  Date/Author: 2026-07-27 / Codex

- Decision: Use `refactor-design` as the running example in the conceptual table while keeping detailed dated results in the later example.
  Rationale: A concrete example makes the layers understandable at first reading, while a single detailed status table avoids competing sources of execution truth.
  Date/Author: 2026-07-27 / Codex

## Risks and Mitigations

- Risk: A manifest can give a false impression of universal semantic proof.
  Mitigation: Label guarantee levels explicitly, require limitations for partial coverage, and document representative sampling.

- Risk: Public checker logic could accidentally encode hidden semantic answers.
  Mitigation: Restrict it to structural traceability and consistency; keep contextual judgments in judge criteria.

- Risk: Model backed diagnostic can consume up to 32 sessions.
  Mitigation: Plan first, require separate explicit cost authorization, run `codex doctor --json`, and do not equate shell approval with cost approval.

- Risk: A highly capable executor can solve a focused fixture despite a weakened skill, invalidating a semantic RED based only on instruction degradation.
  Mitigation: Preserve the partial artifacts, report `INVALID_RED` honestly, stop the campaign, and do not claim semantic qualification.

- Risk: Existing untracked files belong to the user.
  Mitigation: Do not modify or delete `_temporary/` or Python caches and scope validation to intended paths.

- Risk: Readers may interpret `complete` as semantically approved.
  Mitigation: Define it as a declared coverage level, place execution in a separate evidence row, and require applicable gates to execute and pass before semantic qualification.

- Risk: Repeating current execution details in two sections can let the documentation drift into contradictory states.
  Mitigation: Keep only a high level conclusion in the conceptual section and retain case level, dated results in `Example: evaluating refactor-design`.

## Validation Strategy

1. Exercise the checker directly and through the zero-session runner case.
2. Validate every case manifest with the runner plan.
3. Compile and run fixture tests locally where possible.
4. Run quick validation, JSON parsing, `git diff --check`, archive rebuild and archive validation.
5. Inspect the final diff and fixture tree for leakage and build artifacts.

## Rollout and Recovery

The change is repository local and has no deployment step. Recovery is a normal Git revert of the suite, documentation, and metadata changes. The untouched baseline under `/tmp` can be used for comparison during this session but is not a durable backup.

## Lessons Learned

- The existing suite has six semantic cases, so the requested additions produce exactly twelve total cases without removing the three explicitly preserved cases.
- The integrated deterministic gate can use the candidate case fixture against an older baseline that lacks `coverage.json`; this produces the intended RED and then three stable candidate GREEN results with zero sessions.
- The requested eight case diagnostic plans exactly 32 maximum sessions: eight baseline executors, eight baseline judges, eight candidate executors, and eight candidate judges.
- The first external execution failed before model output because the outer sandbox made app-server initialization read only. The escalated boundary resolved that infrastructure issue.
- The negative control passed `hidden-invocation-state` twice. The second pass occurred after adding a protected unrelated risk, removing the explicit scope hint, adding a structural oracle, and making the negative instruction directly oppose invocation-local state. This demonstrates that degraded instructions are not a reliable semantic RED for a sufficiently obvious fixture.
- Five sessions were recorded cumulatively: one failed infrastructure invocation and two executor/judge pairs for the invalid negative observations.
- Conceptual prose without a verifiable execution state left normative completeness open to being read as semantic approval. A dated evidence table makes the boundary inspectable without weakening the coverage contract.
- Separating the generic framework from its concrete example made the reader reconstruct their relationship across distant sections. A running example is needed at the point where each abstraction is introduced.
