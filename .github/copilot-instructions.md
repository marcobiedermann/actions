# Copilot Instructions for this repo

- **Purpose**: This repository hosts reusable GitHub composite actions and workflows to standardize CI across projects. There is no application code here; focus on the action inputs, outputs, and defaults.

## Composite actions

- **Checkout**: [ .github/actions/checkout/action.yml](.github/actions/checkout/action.yml) wraps `actions/checkout@v6` with no extra inputs.
- **Setup Node**: [ .github/actions/setup-node/action.yml](.github/actions/setup-node/action.yml) wraps `actions/setup-node@v6` with `cache: npm` and `node-version-file: .nvmrc`. Callers must provide `.nvmrc` in their repo; keep this file in sync when bumping Node.
- **Test**: [ .github/actions/test/action.yml](.github/actions/test/action.yml) runs `npm ci`, then `npm run lint|build|coverage --if-present`, and finally uploads coverage via `codecov/codecov-action@v5`. It requires an input `codecov_token` and assumes the consuming repo defines the npm scripts.

## Reusable workflows

- **CI workflow**: [ .github/workflows/ci.yml](.github/workflows/ci.yml) is `workflow_call`-only. It chains the three composite actions and forwards `CODECOV_TOKEN` secret to the test action. Keep secrets wired at the caller level.
- **Dependabot auto-merge**: [ .github/workflows/dependabot-auto-merge.yml](.github/workflows/dependabot-auto-merge.yml) auto-approves and auto-merges Dependabot PRs (non-major) using `gh` CLI. It labels direct production dependency bumps with "production". It requires `contents` and `pull-requests` write permissions; callers should supply `GITHUB_TOKEN` via default secrets.

## Usage patterns

- Reference these actions with `marcobiedermann/actions/.github/actions/<name>@main` in downstream workflows; the reusable workflows are invoked via `workflow_call` with `uses: marcobiedermann/actions/.github/workflows/<file>@main`.
- The test action expects npm projects. If scripts are missing, the `--if-present` guards prevent failures, but coverage upload still runs—ensure tests emit coverage to avoid empty reports.
- Keep timeouts short (5 minutes) per current defaults; adjust in callers if your pipeline routinely exceeds this.

## Maintenance notes

- When updating Node versions, adjust consuming repos' `.nvmrc`; no hardcoded version exists here.
- Codecov: callers must pass `CODECOV_TOKEN` secret; confirm coverage report paths match defaults (no path overrides in action).
- `gh` CLI is required for Dependabot auto-merge; `ubuntu-latest` includes it. If switching runners, ensure `gh` is available.
