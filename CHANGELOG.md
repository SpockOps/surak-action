# Changelog

## v1.0.0

Initial release.

- Composite action wrapping the `surak` PyPI package
- Inputs: `policies-dir`, `version`, `strict`
- Outputs: `result` (JSON), `passed` (boolean)
- Supports all prebuilt policies: `actor_allowlist`, `runner_type`, `runner_os`, `runner_arch`, `runner_image`, `branch_protection`, `required_checks`