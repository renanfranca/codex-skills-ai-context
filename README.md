# Codex Skills Audit Memory

This repository is the auditable project memory for Codex Skills. It preserves self contained ExecPlans and concise durable records of decisions, justifications, validation evidence, risks, lessons, and reusable project context.

Historical files are complementary context, not a complete transcript or a substitute for reading the current source repository. Every ExecPlan must remain understandable and executable without relying on undocumented conversation history.

Name new ExecPlans `<YYYY-MM-DD>_<TYPE>_<short-kebab-title>-exec-plan.md`, where `TYPE` is a concise uppercase category such as `FEATURE`, `FIX`, `REFACTOR`, `DOCS`, or `TEST`. Existing files and files under `done/` retain their current names.

## Content Boundary

Keep records concise and necessary for future implementation, review, recovery, or audit. Record observable evidence and decisions, not hidden deliberation.

Never store:

- credentials, tokens, secrets, or private keys;
- personal data or proprietary source;
- full conversation transcripts or complete generated model responses;
- private reasoning or reconstructed chain of thought;
- hidden evaluation oracles, judge criteria, or answer keys.

Do not commit or push changes unless the user explicitly requests it.
