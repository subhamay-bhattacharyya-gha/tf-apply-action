# GitHub Action: Terraform Apply and Summary

![Release](https://github.com/subhamay-bhattacharyya-gha/tf-apply-action/actions/workflows/release.yaml/badge.svg)&nbsp;![Commit Activity](https://img.shields.io/github/commit-activity/t/subhamay-bhattacharyya-gha/tf-apply-action)&nbsp;![Last Commit](https://img.shields.io/github/last-commit/subhamay-bhattacharyya-gha/tf-apply-action)&nbsp;![Release Date](https://img.shields.io/github/release-date/subhamay-bhattacharyya-gha/tf-apply-action)&nbsp;![Repo Size](https://img.shields.io/github/repo-size/subhamay-bhattacharyya-gha/tf-apply-action)&nbsp;![File Count](https://img.shields.io/github/directory-file-count/subhamay-bhattacharyya-gha/tf-apply-action)&nbsp;![Issues](https://img.shields.io/github/issues/subhamay-bhattacharyya-gha/tf-apply-action)&nbsp;![Top Language](https://img.shields.io/github/languages/top/subhamay-bhattacharyya-gha/tf-apply-action)&nbsp;![Custom Endpoint](https://img.shields.io/endpoint?url=https://gist.githubusercontent.com/bsubhamay/448ffc4605f92f186e1347b403749746/raw/tf-apply-action.json?)

A GitHub Composite Action to run `terraform apply` using a binary plan file and display a detailed summary including resource actions, IDs, and timestamps in the GitHub Actions Step Summary.

---

## 🔧 Features

- Runs `terraform apply` using a pre-generated plan (`tfplan.out`)
- Summarizes changes in Markdown table format
- Displays:
  - Added, changed, and destroyed resource counts
  - Resource type, name, action, ID, and timestamp
- Useful for audit trails and readability in CI/CD pipelines

---

## 📥 Inputs

| Name               | Description                                          | Required | Default     |
|--------------------|------------------------------------------------------|----------|-------------|
| `terraform-dir`    | Directory where Terraform code is located            | No       | `.`         |
| `terraform-plan-file` | Terraform binary plan file to apply              | No       | `tfplan.out` |

---

## 🚀 Example Usage

```yaml
name: Terraform Apply Workflow

on:
  push:
    branches: [main]

jobs:
  terraform-apply:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout Code
        uses: actions/checkout@v4

      - name: Terraform Apply with Summary
        uses: subhamay-bhattacharyya-gha/tf-apply-action@main
        with:
          terraform-dir: 'tf'
          terraform-plan-file: 'tfplan.out'
```

## License

MIT
