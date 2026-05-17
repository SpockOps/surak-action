# Surak Policy Engine

**GitHub Action — enforce policies on your GitHub Actions workflows.**

[![SpockOps](https://img.shields.io/badge/SpockOps-FFE200?style=flat&logoColor=black)](https://spockops.com)
[![License](https://img.shields.io/badge/license-Apache%202.0-blue.svg)](LICENSE)

Surak lets you define policies as YAML files and enforce them as a gate in any workflow — before builds run, before deploys happen, before anything you care about.

---

## Usage

```yaml
jobs:
  policy-gate:
    runs-on: ubuntu-22.04
    permissions:
      contents: read
      checks: read
    steps:
      - uses: actions/checkout@v4

      - name: Evaluate Surak policies
        uses: spockops/surak-action@v1
        with:
          policies-dir: .surak/
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}

  deploy:
    needs: policy-gate
    runs-on: ubuntu-22.04
    steps:
      - run: echo "Policy gate passed — deploying"
```

---

## Inputs

| Input | Description | Default |
|---|---|---|
| `policies-dir` | Path to your `.surak/` policy directory | `.surak/` |
| `version` | Surak package version to install | `latest` |
| `strict` | Exit 1 on failure. Set `false` for warn-only mode | `true` |

## Outputs

| Output | Description |
|---|---|
| `result` | Full JSON evaluation result |
| `passed` | `true` or `false` |

### Using outputs

```yaml
- name: Evaluate policies
  id: surak
  uses: spockops/surak-action@v1
  env:
    GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}

- name: Print result
  run: echo "${{ steps.surak.outputs.result }}"
```

---

## Policies

Create `.surak/` at your repo root and add policy YAML files. Each file targets one or more workflows via `applies-to`.

### Global runner governance

```yaml
# .surak/governance.yml
surak: v2
id: runner-governance
name: Runner Governance

applies-to: "*"

policies:
  runner_type:
    enabled: true
    require: github-hosted

  runner_os:
    enabled: true
    allow:
      - Linux
```

### Production deploy gate

```yaml
# .surak/deploy.yml
surak: v2
id: prod-deployment
name: Production Deployment Policy

applies-to:
  - .github/workflows/deploy.yml

policies:
  actor_allowlist:
    enabled: true
    allow:
      - your-github-username

  branch_protection:
    enabled: true
    require: main

  required_checks:
    enabled: true
    checks:
      - Tests
      - Security Scan
```

### Available policies

| Policy | Enforces |
|---|---|
| `actor_allowlist` | Who can trigger the workflow. Glob patterns: `bot-*` |
| `runner_type` | `github-hosted` or `self-hosted` |
| `runner_os` | `Linux`, `Windows`, `macOS` |
| `runner_arch` | `X64`, `ARM64` |
| `runner_image` | Specific image: `ubuntu-22.04`, `macos-14`. Supports `skip_if_self_hosted` |
| `branch_protection` | Required branch. Glob patterns: `release/*` |
| `required_checks` | Check suites that must have passed |

See [Policy Reference](https://github.com/SpockOps/surak/blob/main/docs/POLICIES.md) for full documentation.

---

## Versioning

| Ref | Use case |
|---|---|
| `spockops/surak-action@v1` | Stable — recommended |
| `spockops/surak-action@v1.0.0` | Pinned exact version |

Pin the underlying `surak` package independently if needed:

```yaml
- uses: spockops/surak-action@v1
  with:
    version: "0.2.0"
```

---

## Warn-only mode

Run policies without blocking — useful for rolling out new policies gradually:

```yaml
- uses: spockops/surak-action@v1
  with:
    strict: false
  env:
    GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

---

## Links

- [Surak on PyPI](https://pypi.org/project/surak/)
- [Policy Reference](https://github.com/SpockOps/surak/blob/main/docs/POLICIES.md)
- [CLI Usage](https://github.com/SpockOps/surak/blob/main/docs/CLI.md)
- [SpockOps](https://spockops.com)

---

```
"Logic over chaos,
Policies enforce the rules —
Wisdom guards your builds."
```