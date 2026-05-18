# Changelog

## v1.1.0

- Surak banner printed to logs on every run via `surak haiku`
- `SURAK_ENVIRONMENT` input documented — pass this explicitly to enforce environment policies (GitHub does not expose `environment:` as a shell variable)
- Requires `surak>=0.2.1`

## v1.0.0

Initial release.

- Composite action wrapping the `surak` PyPI package
- Inputs: `policies-dir`, `version`, `strict`
- Outputs: `result` (JSON), `passed` (boolean)
- Supports all prebuilt policies: `actor_allowlist`, `runner_type`, `runner_os`, `runner_arch`, `runner_image`, `branch_protection`, `required_checks`, `environments`