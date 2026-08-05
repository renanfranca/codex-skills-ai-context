# Fix Netlify root routes

This ExecPlan is a living document. Keep `Progress`, `Decisions`, `Risks and Mitigations`, and `Lessons Learned` up to date as work advances.

## Purpose / Big Picture

Make the site work from the root of its Netlify preview and production domains. Clicking “Explore the skills” from the home page will open `/skills/`, rather than the GitHub Pages-only `/codex-skills/skills/` path that returns Netlify’s 404 page.

## Scope

In scope: deployment-aware VitePress and generated-content base paths, behavior coverage for Netlify links, and public deployment documentation. Out of scope: changing GitHub Pages URLs, generated files, the catalog content, committing, pushing, or deploying.

Safety boundary: This task is limited to authorized, defensive maintenance of this repository.

## Existing Context

`website/content-config.json` records `/codex-skills/` as the GitHub Pages base. `website/scripts/generate-content.mjs` embeds that base into generated HTML links, including the home-page “Explore the skills” link. The new `netlify.toml` passes `--base /` to VitePress, but this does not change the generator’s embedded links, which causes Netlify previews to navigate to `/codex-skills/skills/`.

## Desired End State

GitHub Pages builds retain `/codex-skills/` links. Netlify builds recognize Netlify’s build environment and generate root-relative links, use a root VitePress base, and make the home-page catalog path `/skills/`.

## Milestones

### Milestone 1 - Make base paths deployment-aware

#### Goal

Generate and configure the correct site base for GitHub Pages and Netlify.

#### Changes

- [ ] Add a failing deterministic behavior assertion for Netlify-generated home links.
- [ ] Update the canonical base-path logic used by generated content and VitePress configuration so Netlify uses `/` and GitHub Pages retains `/codex-skills/`.
- [ ] Update `website/README.md` with the Netlify deployment behavior.
- [ ] Do not edit `website/.generated/`; it is a disposable projection.

#### Validation

- [ ] Command: `npm test`
- [ ] Expected result: the new Netlify assertion first fails, then the full unit suite passes after the implementation.
- [ ] Command: `NETLIFY=true npm run build -- --base /`
- [ ] Expected result: the Netlify build completes and its generated home page links to `/skills/`.
- [ ] Commands, in order: `npm test`, `npm run prettier:check`, `npm run build`, and `npm run test:e2e`.
- [ ] Expected result: the GitHub Pages build and public checkpoint remain valid.

#### Acceptance Criteria

- [ ] Netlify-generated home-page “Explore the skills” link has `href="/skills/"`.
- [ ] GitHub Pages-generated home-page link remains `href="/codex-skills/skills/"`.
- [ ] The Netlify preview URL no longer routes through `/codex-skills/`.

## Progress

- [x] Applicable repository and website instructions read; memory worktree and origin verified.
- [x] Diagnosed the Netlify 404 as a generated-link base-path mismatch.
- [x] Milestone 1 started.
- [x] Demonstrated RED: the Netlify home-link assertion failed while generated links retained `/codex-skills/`.
- [x] Added the shared deployment-aware base-path helper and used it in both generated content and VitePress configuration.
- [x] Added Netlify deployment documentation.
- [x] GREEN: `npm test` passed all 38 tests, including the preserved GitHub Pages and Netlify root-link assertions.
- [x] Netlify build passed with `NETLIFY=true npm run build -- --base /`; generated home links use `/skills/`.
- [x] GitHub Pages build passed with `npm run build`; generated home links retain `/codex-skills/skills/`.
- [x] Final validation completed: `npm test`, `npm run prettier:check`, `npm run build`, and `npm run test:e2e` all passed.

## Decisions

- Decision: Select the root base only when Netlify provides its standard `NETLIFY` environment marker.
  Rationale: GitHub Pages remains a supported publishing target with a project subpath, while Netlify serves this repository at a domain root.
  Date/Author: 2026-08-04 / Codex

## Risks and Mitigations

- Risk: changing the configured base globally would break the existing GitHub Pages deployment.
  Mitigation: retain the configured `/codex-skills/` default and select `/` only for Netlify builds.
- Risk: generated content may be edited directly and later overwritten.
  Mitigation: alter the canonical generator only and validate through a fresh build.

## Validation Strategy

1. Add a Netlify-specific generator assertion and run `npm test` to demonstrate RED.
2. Implement the environment-aware base path and run `npm test` for GREEN.
3. Build with `NETLIFY=true` to verify the deployed target’s output.
4. Run the website’s required final sequence and inspect the resulting links.

## Documentation Impact

`website/README.md` is the canonical documentation for website deployments. It will retain the GitHub Pages deployment details and add the Netlify root-path behavior. Generated content remains a disposable projection and requires no direct documentation edit.

## Rollout and Recovery

Netlify rebuilds previews from `netlify.toml` after this branch is deployed. To recover, revert the canonical base-path change and its test together, then rebuild both target configurations.

## Lessons Learned

- The VitePress CLI `--base /` option does not rewrite absolute links already embedded by the content generator. Both the generator and VitePress configuration must select the deployment base.
- `NETLIFY` provides a narrow deployment signal that preserves the existing GitHub Pages path by default.
