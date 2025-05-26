# GitHub Action: Terraform Apply and Summary

![Release](https://github.com/subhamay-bhattacharyya-gha/tf-apply-action/actions/workflows/release.yaml/badge.svg)&nbsp;![Commit Activity](https://img.shields.io/github/commit-activity/t/subhamay-bhattacharyya-gha/tf-apply-action)&nbsp;![Last Commit](https://img.shields.io/github/last-commit/subhamay-bhattacharyya-gha/tf-apply-action)&nbsp;![Release Date](https://img.shields.io/github/release-date/subhamay-bhattacharyya-gha/tf-apply-action)&nbsp;![Repo Size](https://img.shields.io/github/repo-size/subhamay-bhattacharyya-gha/tf-apply-action)&nbsp;![File Count](https://img.shields.io/github/directory-file-count/subhamay-bhattacharyya-gha/tf-apply-action)&nbsp;![Issues](https://img.shields.io/github/issues/subhamay-bhattacharyya-gha/tf-apply-action)&nbsp;![Top Language](https://img.shields.io/github/languages/top/subhamay-bhattacharyya-gha/tf-apply-action)&nbsp;![Custom Endpoint](https://img.shields.io/endpoint?url=https://gist.githubusercontent.com/bsubhamay/448ffc4605f92f186e1347b403749746/raw/tf-apply-action.json?)

A GitHub Composite Action to run `terraform apply` using a binary plan artifact and display a detailed summary including resource actions, IDs, outputs, and timestamps in the GitHub Actions Step Summary.

---

## 🔧 Features

- Downloads and uses a pre-generated Terraform plan (`tfplan.out`) from an artifact
- Initializes Terraform backend with S3 and DynamoDB
- Dynamically computes S3 key based on the CI pipeline context
- Executes `terraform apply` and captures a readable output
- Displays:
  - Resource action summary (added, changed, destroyed)
  - Affected resource table with type, name, ID, and timestamp
  - Output variables in table format
- Ideal for traceability in CI/CD pipelines using OIDC-authenticated AWS access

---

## 📥 Inputs

| Name                  | Description                                                 | Required | Default          |
|-----------------------|-------------------------------------------------------------|----------|------------------|
| `artifact-name`       | Name of the uploaded Terraform plan artifact                | Yes      | `terraform-plan` |
| `s3-bucket`           | S3 bucket for Terraform backend                             | Yes      | —                |
| `s3-region`           | AWS region where the S3 bucket is located                   | Yes      | —                |
| `dynamodb-table`      | DynamoDB table name for state locking                       | Yes      | —                |
| `terraform-plan-file` | Path to the Terraform binary plan file inside the artifact  | Yes      | `tfplan.out`     |
| `terraform-dir`       | Directory where Terraform code is located                   | No       | `tf`             |
| `ci-pipeline`         | Whether this is a CI pipeline (adds commit SHA to state key) | No       | `true`           |

---

## 🚀 Example Usage

```yaml
name: Terraform Apply Workflow

on:
  workflow_dispatch:

jobs:
  terraform-apply:
    runs-on: ubuntu-latest
    permissions:
      id-token: write
      contents: read

    steps:
      - name: Configure AWS credentials via OIDC
        uses: aws-actions/configure-aws-credentials@v2
        with:
          role-to-assume: arn:aws:iam::123456789012:role/GitHubActionsTerraformRole
          aws-region: us-east-1

      - name: Terraform Apply with Summary
        uses: subhamay-bhattacharyya-gha/tf-apply-action@main
        with:
          artifact-name: terraform-plan
          terraform-dir: tf
          terraform-plan-file: tfplan.out
          s3-bucket: gha-terraform-state
          s3-region: us-east-1
          dynamodb-table: gha-terraform-lock
```

## License

MIT
