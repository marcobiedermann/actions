# actions

Reusable GitHub Actions and Workflows for private Node.js repositories.

> **Opinionated by design.** This repository is intentionally non-configurable. It targets Node.js projects using `npm` and is not intended for general-purpose use.

## Prerequisites

All consuming repositories must satisfy the following requirements:

- A `.nvmrc` file specifying the Node.js version
- `npm` as the package manager
- A `package.json` defining the following scripts (missing scripts are skipped):
  - `lint`
  - `build`
  - `coverage`
- A [Codecov](https://codecov.io) account with a `CODECOV_TOKEN` repository secret (required for the CI workflow)

## Usage

### Reusable workflows

#### CI

Runs `lint`, `build`, and `coverage` in sequence, then uploads the coverage report to Codecov.

```yaml
name: CI
on:
  push:
  pull_request:

jobs:
  build:
    uses: marcobiedermann/actions/.github/workflows/ci.yml@main
    secrets:
      CODECOV_TOKEN: ${{ secrets.CODECOV_TOKEN }}
```

#### Dependabot auto-merge

Automatically approves and rebases Dependabot pull requests. Semver-major updates are not auto-merged and require manual review. Production dependencies are labelled `production`.

```yaml
name: Dependabot auto-merge
on:
  pull_request:
    types: [opened, synchronize]

jobs:
  dependabot:
    uses: marcobiedermann/actions/.github/workflows/dependabot-auto-merge.yml@main
```
