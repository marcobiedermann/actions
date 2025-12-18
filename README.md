# actions

Reusable GitHub Actions and Workflows

## Usage

### Reusable workflows

Add a workflow that calls the reusable CI workflow:

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

Add a workflow to enable Dependabot auto-merge:

```yaml
name: Dependabot auto-merge
on:
  pull_request:
    types: [opened, synchronize]

jobs:
  dependabot:
    uses: marcobiedermann/actions/.github/workflows/dependabot-auto-merge.yml@main
    secrets:
      GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```
