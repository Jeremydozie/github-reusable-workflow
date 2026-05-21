# github-reusable-workflow

Shared [GitHub Actions reusable workflows](https://docs.github.com/en/actions/using-workflows/reusing-workflows) for Node.js CI.

## Workflows

| Workflow | File | Purpose |
|----------|------|---------|
| Node.js CI | [`.github/workflows/nodejs-ci.yml`](.github/workflows/nodejs-ci.yml) | Lint, unit tests, optional integration tests, Docker build |

## Release a version

Tag this repository so callers can pin a ref:

```bash
git tag v1.0.0
git push origin v1.0.0
```

## Call from another repository

In your app repo (e.g. `Jeremydozie/my-app`), add `.github/workflows/pr.yml`:

```yaml
name: Pull request

on:
  pull_request:
    branches: [main]

jobs:
  ci:
    uses: Jeremydozie/github-reusable-workflow/.github/workflows/nodejs-ci.yml@v1.0.0
    with:
      node-version: "20"
      working-directory: "."
      run-integration-tests: false
    secrets:
      CODECOV_TOKEN: ${{ secrets.CODECOV_TOKEN }}
```

### Inputs

| Input | Type | Default | Description |
|-------|------|---------|-------------|
| `node-version` | string | `"20"` | Node.js version |
| `working-directory` | string | `"."` | Path to `package.json` |
| `run-integration-tests` | boolean | `false` | Run Postgres-backed integration job |

### Secrets

| Secret | Required | Description |
|--------|----------|-------------|
| `CODECOV_TOKEN` | No | Codecov upload token |

### Outputs

| Output | Description |
|--------|-------------|
| `image-tag` | Docker tags from the build job (for deploy workflows) |

## Caller requirements

The calling repository must define npm scripts used by this workflow:

- `lint`
- `test:unit`
- `test:integration` (if `run-integration-tests: true`)

Include a `package-lock.json` at `working-directory` and a `Dockerfile` at the repo root if you use the build job.

## Repository access

For private repos, allow the caller to use workflows from this repository under **Settings → Actions → General → Access**.
