# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repo is

A **GitHub composite action** that runs `terraform apply` against a pre-generated plan artifact, with built-in OIDC auth for AWS/Azure/GCP and support for two backends (S3 or HCP Terraform Cloud). The entire action is a single file: `action.yaml`. The Node/npm tooling is **only** for semantic-release — there is no application code to build, test, or lint.

## Commands

- `npm ci` — install dev dependencies (only needed for release tooling).
- `npm run release` / `npx semantic-release` — invoked by `.github/workflows/release.yaml` on push to `main`. Don't run locally unless debugging the release pipeline.
- There are **no** build, test, or lint commands. Changes are validated by consumers using the action in their workflows.

Commits must follow Conventional Commits: `feat:` → minor bump, `fix:` → patch bump (see `scripts/plugins/analyze-commits.js`). Anything else produces no release.

## Architecture

### The two-dimensional configuration matrix

Every invocation picks one value along each axis:

- **`backend-type`**: `s3` (default) or `remote` (HCP Terraform Cloud).
- **`cloud-provider`**: `aws`, `azure`, `gcp`, `snowflake`, `databricks`, or `platform`.

These axes are independent. Example: `backend-type: s3` + `cloud-provider: azure` stores state in S3 while deploying Azure resources.

Two non-obvious rules enforced by `action.yaml`:

1. **AWS auth is auto-configured whenever `backend-type: s3`**, regardless of `cloud-provider`. The S3 bucket needs AWS credentials even when the workload targets Azure/GCP/Snowflake/Databricks. See the `Configure AWS Authentication` step's `if:` expression.
2. **Snowflake and Databricks auth is NOT taken as action inputs** — the calling workflow must set `SNOWFLAKE_*` / `DATABRICKS_*` env vars before invoking the action. The validation step only prints a reminder; it does not check env vars.

### Platform mode (`cloud-provider: platform`)

Auto-detects which cloud providers are in use by looking for `infra/aws/`, `infra/gcp/`, `infra/azure/`, `infra/snowflake/`, `infra/databricks/` directories **in the consumer's repo at action runtime**. For each directory found, the corresponding auth inputs become required and the matching auth step runs. Missing auth inputs for a detected directory fail validation with a combined error message. The `tf-config-path` input in platform mode is typically `infra` (the parent).

### Step sequence (all in `action.yaml`)

1. Debug-print inputs → 2. Validate config → 3. Resolve checkout ref (release-tag or current ref) → 4. Checkout → 5. Setup Terraform → 6. Download plan artifact (`actions/download-artifact@v8` with name = `tf-plan-name`) → 7. Cloud auth (AWS/Azure/GCP conditionals) → 8. If `remote`: write `~/.terraform.d/credentials.tfrc.json` + `terraform init` → 9. If `s3`: compute state key + `terraform init -backend-config=...` → 10. `terraform apply tfplan.out` + write GitHub Step Summary.

### Coupling to the plan action

This action consumes an artifact produced by a companion plan action (the `tf-plan-name` input — default `terraform-plan`). The apply step reads `<tf-plan-file>.binary` (default `tfplan.binary`) from `tf-config-path` — the plan-action uploads both `<tf-plan-file>.binary` and `<tf-plan-file>.json`. Keep the filename in sync on both sides; don't reintroduce a hardcoded name.

### State key construction (S3 backend)

- `ci-pipeline: true` → `<s3-key-prefix>/<repo>/<sha>/terraform.tfstate` (unique per commit).
- `ci-pipeline: false` → `<s3-key-prefix>/<repo>/terraform.tfstate` (shared).

Backend config is passed via `-backend-config=` flags at `init` time, so Terraform files should declare an empty `backend "s3" {}` block.

## Release config duplication

Both `.releaserc.json` and `package.json`'s `release.extends` → `scripts/plugins/release.config.js` define semantic-release config with identical content. `package.json`'s `extends` takes precedence. Keep the two in sync or consolidate if modifying release behavior.

## When editing

- `action.yaml` is the entire product. Changes here are the only ones end-users see.
- Validation logic lives in the `Validate Input Configuration` step. Add new required-input checks there, matching the existing per-provider `case` pattern.
- Adding a new cloud provider: update the `cloud-provider` validation regex, add a validation `case`, add a conditional auth step, and extend the summary `case` block at the bottom of the apply step. Update platform-mode directory detection (three places: validate, auth-step `if:`, and summary section) if it should also work under `platform`.
- README examples drive adoption; keep them consistent with any input name changes.
