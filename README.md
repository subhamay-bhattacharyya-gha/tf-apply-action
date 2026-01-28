# GitHub Action: Terraform Apply

![Release](https://github.com/subhamay-bhattacharyya-gha/tf-apply-action/actions/workflows/release.yaml/badge.svg)&nbsp;![Commit Activity](https://img.shields.io/github/commit-activity/t/subhamay-bhattacharyya-gha/tf-apply-action)&nbsp;![Last Commit](https://img.shields.io/github/last-commit/subhamay-bhattacharyya-gha/tf-apply-action)&nbsp;![Release Date](https://img.shields.io/github/release-date/subhamay-bhattacharyya-gha/tf-apply-action)&nbsp;![Repo Size](https://img.shields.io/github/repo-size/subhamay-bhattacharyya-gha/tf-apply-action)&nbsp;![File Count](https://img.shields.io/github/directory-file-count/subhamay-bhattacharyya-gha/tf-apply-action)&nbsp;![Issues](https://img.shields.io/github/issues/subhamay-bhattacharyya-gha/tf-apply-action)&nbsp;![Top Language](https://img.shields.io/github/languages/top/subhamay-bhattacharyya-gha/tf-apply-action)&nbsp;![Custom Endpoint](https://img.shields.io/endpoint?url=https://gist.githubusercontent.com/bsubhamay/f3ca17641b5662785958cc59042f0877/raw/tf-apply-action.json?)

A comprehensive GitHub composite action for running `terraform apply` with support for multiple backends (S3, HCP Terraform Cloud), multi-cloud authentication (AWS, Azure, GCP, Snowflake, Databricks), platform mode for multi-provider deployments, and automated artifact management.

## Features

- 🌐 **Multi-Cloud Support**: Works with AWS, Azure, GCP, Snowflake, and Databricks
- 🏗️ **Platform Mode**: Auto-detect and deploy to multiple cloud providers based on directory structure
- ☁️ **HCP Terraform Cloud**: Full support for Terraform Cloud remote backend
- 🔐 **Built-in OIDC Authentication**: Integrated authentication for AWS, Azure, and GCP
- 🔑 **Snowflake Authentication**: Private key-based authentication for Snowflake
- 🔓 **Databricks Authentication**: Personal access token authentication for Databricks
- 📊 **Detailed Summaries**: Generates comprehensive GitHub Step Summaries with resource changes and outputs
- 🔒 **Secure State Management**: Supports S3 backend with encryption and locking, plus HCP Terraform Cloud
- 🚀 **CI/CD Ready**: Configurable state keys for different deployment strategies
- 📋 **Resource Tracking**: Shows affected resources with actions and types
- 🐛 **Debug Support**: Built-in input debugging for troubleshooting

## Usage

### AWS S3 Backend

```yaml
- name: Apply Terraform (AWS)
  uses: subhamay-bhattacharyya-gha/tf-apply-action@main
  with:
    backend-type: 's3'
    cloud-provider: 'aws'
    s3-bucket: ${{ vars.AWS_TF_STATE_BUCKET }}
    s3-region: ${{ vars.AWS_REGION }}
    s3-key-prefix: 'environments/prod'  # optional
    aws-region: ${{ vars.AWS_REGION }}
    aws-role-to-assume: ${{ secrets.AWS_ROLE_ARN }}
    terraform-dir: 'infra/aws/tf'
    ci-pipeline: 'true'
```

> **Note:** Configure these values in your GitHub repository:
> - `AWS_REGION` (Variable): Your AWS region (e.g., `us-east-1`)
> - `AWS_TF_STATE_BUCKET` (Variable): Your S3 bucket name for Terraform state (e.g., `my-company-terraform-state`)
> - `AWS_ROLE_ARN` (Secret): Your IAM role ARN (e.g., `arn:aws:iam::123456789012:role/GitHubActionsRole`)
> 
> Never hardcode IAM role ARNs, bucket names, or regions in your workflow files. Store sensitive values like role ARNs as GitHub repository secrets and non-sensitive values like regions and bucket names as repository variables for security.

### Azure with S3 Backend

```yaml
- name: Apply Terraform (Azure)
  uses: subhamay-bhattacharyya-gha/tf-apply-action@main
  with:
    backend-type: 's3'  # Uses S3 backend - AWS auth is auto-configured
    cloud-provider: 'azure'
    s3-bucket: ${{ vars.AWS_TF_STATE_BUCKET }}  # Your backend bucket
    s3-region: ${{ vars.AWS_REGION }}  # Backend region
    aws-region: ${{ vars.AWS_REGION }}  # Required for S3 backend access
    aws-role-to-assume: ${{ secrets.AWS_ROLE_ARN }}  # Required for S3 backend access
    azure-client-id: ${{ secrets.AZURE_CLIENT_ID }}
    azure-tenant-id: ${{ secrets.AZURE_TENANT_ID }}
    azure-subscription-id: ${{ secrets.AZURE_SUBSCRIPTION_ID }}
    terraform-dir: 'infra/azure/tf'
    ci-pipeline: 'true'
```

> **Note:** Configure these values in your GitHub repository:
> - `AWS_REGION` (Variable): Your backend region (e.g., `us-east-1`)
> - `AWS_TF_STATE_BUCKET` (Variable): Your S3 backend bucket name (e.g., `my-company-terraform-state`)
> - `AWS_ROLE_ARN` (Secret): Your IAM role ARN for S3 backend access (e.g., `arn:aws:iam::123456789012:role/GitHubActionsRole`)
> - `AZURE_CLIENT_ID` (Secret): Your Azure application (client) ID
> - `AZURE_TENANT_ID` (Secret): Your Azure tenant ID
> - `AZURE_SUBSCRIPTION_ID` (Secret): Your Azure subscription ID
> 
> When using S3 backend with Azure, AWS authentication is automatically configured to access the S3 bucket. Never hardcode credentials, bucket names, or regions in your workflow files.

### GCP with S3 Backend

```yaml
- name: Apply Terraform (GCP)
  uses: subhamay-bhattacharyya-gha/tf-apply-action@main
  with:
    backend-type: 's3'  # Uses S3 backend - AWS auth is auto-configured
    cloud-provider: 'gcp'
    s3-bucket: ${{ vars.AWS_TF_STATE_BUCKET }}  # Your backend bucket
    s3-region: ${{ vars.AWS_REGION }}  # Backend region
    aws-region: ${{ vars.AWS_REGION }}  # Required for S3 backend access
    aws-role-to-assume: ${{ secrets.AWS_ROLE_ARN }}  # Required for S3 backend access
    gcp-wif-provider: ${{ secrets.GCP_WIF_PROVIDER }}
    gcp-service-account: ${{ secrets.GCP_SERVICE_ACCOUNT }}
    terraform-dir: 'infra/gcp/tf'
    ci-pipeline: 'true'
```

> **Note:** Configure these values in your GitHub repository:
> - `AWS_REGION` (Variable): Your backend region (e.g., `us-east-1`)
> - `AWS_TF_STATE_BUCKET` (Variable): Your S3 backend bucket name (e.g., `my-company-terraform-state`)
> - `AWS_ROLE_ARN` (Secret): Your IAM role ARN for S3 backend access (e.g., `arn:aws:iam::123456789012:role/GitHubActionsRole`)
> - `GCP_WIF_PROVIDER` (Secret): Your Workload Identity Federation provider (e.g., `projects/123456789/locations/global/workloadIdentityPools/my-pool/providers/my-provider`)
> - `GCP_SERVICE_ACCOUNT` (Secret): Your service account email (e.g., `my-service-account@my-project.iam.gserviceaccount.com`)
> 
> When using S3 backend with GCP, AWS authentication is automatically configured to access the S3 bucket. Never hardcode credentials, bucket names, or regions in your workflow files.

### HCP Terraform Cloud Backend

```yaml
- name: Apply Terraform (HCP Terraform Cloud)
  uses: subhamay-bhattacharyya-gha/tf-apply-action@main
  with:
    backend-type: 'remote'
    cloud-provider: 'aws'  # or 'azure', 'gcp' for resource authentication
    tfc-token: ${{ secrets.TF_API_TOKEN }}
    aws-region: ${{ vars.AWS_REGION }}  # Required for cloud provider authentication
    aws-role-to-assume: ${{ secrets.AWS_ROLE_ARN }}
    terraform-dir: 'infra/aws/tf'
    ci-pipeline: 'true'
```

> **Note:** Configure these values in your GitHub repository:
> - `AWS_REGION` (Variable): Your AWS region for resource authentication (e.g., `us-east-1`)
> - `TF_API_TOKEN` (Secret): Your HCP Terraform Cloud API token
> - `AWS_ROLE_ARN` (Secret): Your IAM role ARN for OIDC authentication (e.g., `arn:aws:iam::123456789012:role/GitHubActionsRole`)
> 
> Never hardcode API tokens, IAM role ARNs, or regions in your workflow files. Store sensitive values like API tokens and role ARNs as GitHub repository secrets and non-sensitive values like regions as repository variables for security. When using different cloud providers with HCP Terraform Cloud, replace the AWS authentication inputs with the appropriate Azure or GCP secrets as shown in the examples above.

### Snowflake with S3 Backend

```yaml
- name: Apply Terraform (Snowflake)
  uses: subhamay-bhattacharyya-gha/tf-apply-action@main
  with:
    backend-type: 's3'  # Uses S3 backend - AWS auth is auto-configured
    cloud-provider: 'snowflake'
    s3-bucket: ${{ vars.AWS_TF_STATE_BUCKET }}  # Your backend bucket
    s3-region: ${{ vars.AWS_REGION }}  # Backend region
    aws-region: ${{ vars.AWS_REGION }}  # Required for S3 backend access
    aws-role-to-assume: ${{ secrets.AWS_ROLE_ARN }}  # Required for S3 backend access
    snowflake-account: ${{ secrets.SNOWFLAKE_ACCOUNT }}
    snowflake-user: ${{ secrets.SNOWFLAKE_USER }}
    snowflake-role: ${{ secrets.SNOWFLAKE_ROLE }}
    snowflake-private-key: ${{ secrets.SNOWFLAKE_PRIVATE_KEY }}
    terraform-dir: 'infra/snowflake/tf'
    ci-pipeline: 'true'
```

> **Note:** Configure these values in your GitHub repository:
> - `AWS_REGION` (Variable): Your backend region (e.g., `us-east-1`)
> - `AWS_TF_STATE_BUCKET` (Variable): Your S3 backend bucket name (e.g., `my-company-terraform-state`)
> - `AWS_ROLE_ARN` (Secret): Your IAM role ARN for S3 backend access (e.g., `arn:aws:iam::123456789012:role/GitHubActionsRole`)
> - `SNOWFLAKE_ACCOUNT` (Secret): Your Snowflake account identifier (e.g., `xy12345.us-east-1`)
> - `SNOWFLAKE_USER` (Secret): Your Snowflake user name
> - `SNOWFLAKE_ROLE` (Secret): Your Snowflake role name
> - `SNOWFLAKE_PRIVATE_KEY` (Secret): Your Snowflake private key for authentication
> 
> When using S3 backend with Snowflake, AWS authentication is automatically configured to access the S3 bucket. Never hardcode credentials, bucket names, or regions in your workflow files.

### Databricks with S3 Backend

```yaml
- name: Apply Terraform (Databricks)
  uses: subhamay-bhattacharyya-gha/tf-apply-action@main
  with:
    backend-type: 's3'  # Uses S3 backend - AWS auth is auto-configured
    cloud-provider: 'databricks'
    s3-bucket: ${{ vars.AWS_TF_STATE_BUCKET }}  # Your backend bucket
    s3-region: ${{ vars.AWS_REGION }}  # Backend region
    aws-region: ${{ vars.AWS_REGION }}  # Required for S3 backend access
    aws-role-to-assume: ${{ secrets.AWS_ROLE_ARN }}  # Required for S3 backend access
    databricks-host: ${{ secrets.DATABRICKS_HOST }}
    databricks-token: ${{ secrets.DATABRICKS_TOKEN }}
    terraform-dir: 'infra/databricks/tf'
    ci-pipeline: 'true'
```

> **Note:** Configure these values in your GitHub repository:
> - `AWS_REGION` (Variable): Your backend region (e.g., `us-east-1`)
> - `AWS_TF_STATE_BUCKET` (Variable): Your S3 backend bucket name (e.g., `my-company-terraform-state`)
> - `AWS_ROLE_ARN` (Secret): Your IAM role ARN for S3 backend access (e.g., `arn:aws:iam::123456789012:role/GitHubActionsRole`)
> - `DATABRICKS_HOST` (Secret): Your Databricks workspace URL (e.g., `https://dbc-12345678-9abc.cloud.databricks.com`)
> - `DATABRICKS_TOKEN` (Secret): Your Databricks personal access token
> 
> When using S3 backend with Databricks, AWS authentication is automatically configured to access the S3 bucket. Never hardcode credentials, bucket names, or regions in your workflow files.

### Platform Mode (Multi-Provider Deployment)

Platform mode automatically detects cloud provider directories under `infra/` and validates/authenticates only for the detected providers. This is ideal for repositories that deploy to multiple cloud providers.

```yaml
- name: Apply Terraform (Platform Mode)
  uses: subhamay-bhattacharyya-gha/tf-apply-action@main
  with:
    backend-type: 's3'
    cloud-provider: 'platform'
    s3-bucket: ${{ vars.AWS_TF_STATE_BUCKET }}
    s3-region: ${{ vars.AWS_REGION }}
    # AWS authentication (if infra/aws exists)
    aws-region: ${{ vars.AWS_REGION }}
    aws-role-to-assume: ${{ secrets.AWS_ROLE_ARN }}
    # GCP authentication (if infra/gcp exists)
    gcp-wif-provider: ${{ secrets.GCP_WIF_PROVIDER }}
    gcp-service-account: ${{ secrets.GCP_SERVICE_ACCOUNT }}
    # Azure authentication (if infra/azure exists)
    azure-client-id: ${{ secrets.AZURE_CLIENT_ID }}
    azure-tenant-id: ${{ secrets.AZURE_TENANT_ID }}
    azure-subscription-id: ${{ secrets.AZURE_SUBSCRIPTION_ID }}
    # Snowflake authentication (if infra/snowflake exists)
    snowflake-account: ${{ secrets.SNOWFLAKE_ACCOUNT }}
    snowflake-user: ${{ secrets.SNOWFLAKE_USER }}
    snowflake-role: ${{ secrets.SNOWFLAKE_ROLE }}
    snowflake-private-key: ${{ secrets.SNOWFLAKE_PRIVATE_KEY }}
    # Databricks authentication (if infra/databricks exists)
    databricks-host: ${{ secrets.DATABRICKS_HOST }}
    databricks-token: ${{ secrets.DATABRICKS_TOKEN }}
    terraform-dir: 'infra'
    ci-pipeline: 'true'
```

> **Note:** Platform mode automatically detects which cloud providers are configured based on directory structure:
> - `infra/aws/` → Validates and uses AWS authentication
> - `infra/gcp/` → Validates and uses GCP authentication
> - `infra/azure/` → Validates and uses Azure authentication
> - `infra/snowflake/` → Validates and uses Snowflake authentication
> - `infra/databricks/` → Validates and uses Databricks authentication
> 
> Only provide authentication inputs for the cloud providers you have directories for. The action will validate that required inputs are provided for each detected provider directory.

## Inputs

### Common Inputs

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `backend-type` | Backend type (`s3` for S3-compatible backends, `remote` for HCP Terraform Cloud) | ❌ | `s3` |
| `cloud-provider` | Target cloud provider or platform (`aws`, `azure`, `gcp`, `snowflake`, `databricks`, `platform`) | ✅ | - |
| `terraform-dir` | Relative path to Terraform configuration directory | ❌ | `tf` |
| `release-tag` | Git release tag to check out | ❌ | `""` |
| `ci-pipeline` | Include commit SHA in state key for CI/CD | ❌ | `false` |
| `tf-plan-name` | Name of the Terraform plan file | ❌ | `terraform-plan` |
| `tf-plan-file` | File to save the Terraform plan output | ❌ | `tfplan` |
| `tf-vars-file` | Optional Terraform variable file | ❌ | `terraform.tfvars` |

### S3 Backend Inputs (for backend-type: 's3')

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `s3-bucket` | S3 bucket name for state storage | ✅ (for S3 backend) | - |
| `s3-region` | AWS region for S3 bucket | ✅ (for S3 backend) | - |
| `s3-key-prefix` | Optional prefix for S3 state key | ❌ | `""` |

### HCP Terraform Cloud Inputs (for backend-type: 'remote')

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `tfc-token` | HCP Terraform Cloud API token | ✅ (for remote backend) | - |

### AWS Authentication Inputs (for cloud-provider: 'aws' or backend-type: 's3')

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `aws-region` | AWS region for authentication | ✅ (for AWS or S3 backend) | - |
| `aws-role-to-assume` | AWS IAM role ARN to assume | ✅ (for AWS or S3 backend) | - |

> **Note:** AWS authentication is automatically configured when using `backend-type: 's3'`, regardless of the `cloud-provider` setting. This ensures proper access to the S3 bucket for state storage.

### Azure Authentication Inputs (for cloud-provider: 'azure')

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `azure-client-id` | Azure client ID for authentication | ✅ (for Azure) | - |
| `azure-tenant-id` | Azure tenant ID for authentication | ✅ (for Azure) | - |
| `azure-subscription-id` | Azure subscription ID for authentication | ✅ (for Azure) | - |

### GCP Authentication Inputs (for cloud-provider: 'gcp')

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `gcp-wif-provider` | GCP Workload Identity Federation provider | ✅ (for GCP) | - |
| `gcp-service-account` | GCP service account email for authentication | ✅ (for GCP) | - |

### Snowflake Authentication Inputs (for cloud-provider: 'snowflake')

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `snowflake-account` | Snowflake account identifier | ✅ (for Snowflake) | - |
| `snowflake-user` | Snowflake user name | ✅ (for Snowflake) | - |
| `snowflake-role` | Snowflake role name | ✅ (for Snowflake) | - |
| `snowflake-private-key` | Snowflake private key for authentication | ✅ (for Snowflake) | - |

### Databricks Authentication Inputs (for cloud-provider: 'databricks')

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `databricks-host` | Databricks workspace URL | ✅ (for Databricks) | - |
| `databricks-token` | Databricks personal access token | ✅ (for Databricks) | - |

## Prerequisites

### OIDC Authentication Setup

This action uses OpenID Connect (OIDC) for secure authentication with cloud providers. You need to set up OIDC trust relationships in your cloud accounts.

#### AWS OIDC Setup
1. Create an IAM role with the necessary permissions
2. Configure the role to trust GitHub's OIDC provider
3. Add the role ARN to your repository secrets

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Federated": "arn:aws:iam::ACCOUNT-ID:oidc-provider/token.actions.githubusercontent.com"
      },
      "Action": "sts:AssumeRoleWithWebIdentity",
      "Condition": {
        "StringEquals": {
          "token.actions.githubusercontent.com:aud": "sts.amazonaws.com",
          "token.actions.githubusercontent.com:sub": "repo:YOUR-ORG/YOUR-REPO:ref:refs/heads/main"
        }
      }
    }
  ]
}
```

#### Azure OIDC Setup
1. Create a service principal in Azure AD
2. Configure federated credentials for GitHub Actions
3. Add the client ID, tenant ID, and subscription ID to repository secrets

#### GCP OIDC Setup
1. Create a Workload Identity Pool and Provider
2. Configure the provider to trust GitHub's OIDC
3. Create a service account and bind it to the workload identity
4. Add the provider and service account details to repository secrets

#### Snowflake Authentication Setup
1. Generate an RSA key pair for authentication
2. Assign the public key to your Snowflake user
3. Store the private key securely in GitHub repository secrets
4. Configure the Snowflake account, user, and role in repository secrets

```bash
# Generate RSA key pair
openssl genrsa -out snowflake_key.pem 2048
openssl rsa -in snowflake_key.pem -pubout -out snowflake_key.pub

# Assign public key to Snowflake user (run in Snowflake)
ALTER USER your_username SET RSA_PUBLIC_KEY='<public_key_content>';
```

#### Databricks Authentication Setup
1. Create a personal access token in your Databricks workspace
2. Navigate to User Settings > Access Tokens
3. Generate a new token with appropriate permissions
4. Store the token and workspace URL in GitHub repository secrets

### Platform Mode

Platform mode (`cloud-provider: 'platform'`) enables automatic detection and deployment to multiple cloud providers based on your repository's directory structure.

#### Directory Structure
```
your-repo/
├── infra/
│   ├── aws/           # AWS Terraform configurations
│   │   └── tf/
│   ├── gcp/           # GCP Terraform configurations
│   │   └── tf/
│   ├── azure/         # Azure Terraform configurations
│   │   └── tf/
│   ├── snowflake/     # Snowflake Terraform configurations
│   │   └── tf/
│   └── databricks/    # Databricks Terraform configurations
│       └── tf/
```

#### How It Works
1. The action scans for directories under `infra/`
2. For each detected provider directory, it validates the required authentication inputs
3. Only providers with existing directories require authentication configuration
4. The GitHub Step Summary shows all detected platforms and their configurations

#### Benefits
- **Simplified Configuration**: One workflow for multiple cloud providers
- **Automatic Detection**: No need to manually specify which providers to deploy
- **Flexible**: Add or remove provider directories without changing the workflow
- **Validation**: Ensures all required inputs are provided for detected providers

### Backend Configuration

#### AWS S3 Backend
The action configures the S3 backend automatically using the provided inputs. When using `backend-type: 's3'`, AWS authentication is automatically configured regardless of the `cloud-provider` setting, ensuring proper access to the S3 bucket for state storage.

Your Terraform configuration should include:

```hcl
terraform {
  backend "s3" {
    # Configuration will be provided via -backend-config flags
  }
}
```

#### Azure/GCP/Snowflake/Databricks with S3 Backend
When deploying to non-AWS cloud providers while using S3 for state storage, the action automatically configures both:
1. AWS authentication for S3 bucket access
2. The respective cloud provider authentication for resource deployment

This dual authentication ensures seamless state management across all supported cloud providers.

#### HCP Terraform Cloud Backend
For HCP Terraform Cloud, define your backend configuration in Terraform files:

```hcl
terraform {
  backend "remote" {
    organization = "your-org"
    workspaces {
      name = "your-workspace"
    }
  }
}
```

## Complete Workflow Examples

### AWS S3 Backend Example

```yaml
name: Terraform Deploy (AWS)

on:
  push:
    branches: [main]

permissions:
  id-token: write   # Required for OIDC
  contents: read    # Required for checkout

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Apply Terraform (AWS)
        uses: subhamay-bhattacharyya-gha/tf-apply-action@main
        with:
          backend-type: 's3'
          cloud-provider: 'aws'
          s3-bucket: ${{ vars.AWS_TF_STATE_BUCKET }}
          s3-region: ${{ vars.AWS_REGION }}
          s3-key-prefix: 'environments/prod'
          aws-region: ${{ vars.AWS_REGION }}
          aws-role-to-assume: ${{ secrets.AWS_ROLE_ARN }}
          terraform-dir: 'infra/aws/tf'
          ci-pipeline: 'true'
```

> **Note:** Configure these values in your GitHub repository:
> - `AWS_REGION` (Variable): Your AWS region (e.g., `us-east-1`)
> - `AWS_TF_STATE_BUCKET` (Variable): Your S3 bucket name for Terraform state (e.g., `my-company-terraform-state`)
> - `AWS_ROLE_ARN` (Secret): Your IAM role ARN (e.g., `arn:aws:iam::123456789012:role/GitHubActionsRole`)
> 
> Never hardcode IAM role ARNs, bucket names, or regions in your workflow files. Store sensitive values like role ARNs as GitHub repository secrets and non-sensitive values like regions and bucket names as repository variables for security.

### Multi-Cloud with HCP Terraform Cloud

```yaml
name: Multi-Cloud Terraform Deploy

on:
  workflow_dispatch:
    inputs:
      cloud_provider:
        description: 'Cloud provider to deploy to'
        required: true
        default: 'aws'
        type: choice
        options:
        - aws
        - azure
        - gcp
        - snowflake
        - databricks
        - platform

permissions:
  id-token: write   # Required for OIDC
  contents: read    # Required for checkout

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Apply Terraform (HCP + Multi-Cloud)
        uses: subhamay-bhattacharyya-gha/tf-apply-action@main
        with:
          backend-type: 'remote'
          cloud-provider: ${{ github.event.inputs.cloud_provider }}
          tfc-token: ${{ secrets.TF_API_TOKEN }}
          # AWS authentication (if aws selected)
          aws-region: ${{ vars.AWS_REGION }}
          aws-role-to-assume: ${{ secrets.AWS_ROLE_ARN }}
          # Azure authentication (if azure selected)
          azure-client-id: ${{ secrets.AZURE_CLIENT_ID }}
          azure-tenant-id: ${{ secrets.AZURE_TENANT_ID }}
          azure-subscription-id: ${{ secrets.AZURE_SUBSCRIPTION_ID }}
          # GCP authentication (if gcp selected)
          gcp-wif-provider: ${{ secrets.GCP_WIF_PROVIDER }}
          gcp-service-account: ${{ secrets.GCP_SERVICE_ACCOUNT }}
          # Snowflake authentication (if snowflake selected)
          snowflake-account: ${{ secrets.SNOWFLAKE_ACCOUNT }}
          snowflake-user: ${{ secrets.SNOWFLAKE_USER }}
          snowflake-role: ${{ secrets.SNOWFLAKE_ROLE }}
          snowflake-private-key: ${{ secrets.SNOWFLAKE_PRIVATE_KEY }}
          # Databricks authentication (if databricks selected)
          databricks-host: ${{ secrets.DATABRICKS_HOST }}
          databricks-token: ${{ secrets.DATABRICKS_TOKEN }}
          terraform-dir: 'infra/gcp/tf'
```

> **Note:** Configure these secrets in your GitHub repository settings:
> - **AWS**: `AWS_ROLE_ARN`
> - **GCP**: `GCP_WIF_PROVIDER`, `GCP_SERVICE_ACCOUNT`
> - **Snowflake**: `SNOWFLAKE_ACCOUNT`, `SNOWFLAKE_USER`, `SNOWFLAKE_ROLE`, `SNOWFLAKE_PRIVATE_KEY`
> - **Databricks**: `DATABRICKS_HOST`, `DATABRICKS_TOKEN`
> - **HCP Terraform Cloud**: `TF_API_TOKEN`
> 
> Also configure these GitHub repository variables:
> - **AWS**: `AWS_TF_STATE_BUCKET`, `AWS_REGION`
> 
> Never commit sensitive values directly in your workflow files. Use GitHub repository secrets for authentication credentials and variables for non-sensitive configuration values.

## Action Workflow

The action follows this optimized sequence:

1. **🐛 Debug - Print All Inputs** - Shows all input values for troubleshooting
2. **✅ Validate Configuration** - Validates required inputs based on backend and cloud provider
3. **📋 Set Checkout Ref** - Determines which Git reference to checkout
4. **📥 Checkout Repo** - Checks out the repository code
5. **🔧 Setup Terraform** - Installs and configures Terraform
6. **📦 Download Terraform Plan Artifact** - Downloads the plan file from previous workflow
7. **🔐 Configure Cloud Authentication** - Sets up OIDC authentication for AWS/Azure/GCP (runs for single provider, platform mode when inputs provided, or automatically when using S3 backend)
8. **❄️ Configure Snowflake Authentication** - Sets up environment variables for Snowflake provider (runs for snowflake or platform mode when inputs provided)
9. **🧱 Configure Databricks Authentication** - Sets up environment variables for Databricks provider (runs for databricks or platform mode when inputs provided)
10. **☁️ Setup TFC Credentials** - Configures HCP Terraform Cloud credentials (if using remote backend)
11. **🚀 Initialize Remote Backend** - Initializes HCP Terraform Cloud backend (if using remote backend)
12. **🔑 Generate State Key** - Creates S3 state key with optional prefix (if using S3 backend)
13. **📡 Initialize S3 Backend** - Initializes S3 backend with full configuration (if using S3 backend)
14. **⚡ Terraform Apply with Summary** - Applies the plan and generates detailed summary

## Output

The action generates a comprehensive GitHub Step Summary that includes:

- 🔧 **Backend Configuration**: Shows backend type and configuration details
- 🔐 **Authentication Details**: Displays authentication method and key identifiers
- 🏗️ **Platform Detection**: In platform mode, shows all detected cloud providers and their configurations
- 📌 **Affected Resources**: Table of resources being created, updated, or destroyed
- 📤 **Terraform Outputs**: All output values from your Terraform configuration
- ⏱️ **Timing Information**: Start and completion timestamps

## Debugging

The action includes built-in debugging features:

- **Input Validation**: Comprehensive validation with clear error messages
- **Debug Output**: All inputs are printed (with sensitive values redacted)
- **Step-by-Step Logging**: Each step provides detailed logging for troubleshooting
- **Error Handling**: Clear error messages for common configuration issues

## Security Features

- 🔐 **OIDC Authentication**: No long-lived credentials required
- 🔒 **S3 Encryption**: Automatic encryption for S3 state files
- 🔑 **Token Redaction**: Sensitive tokens are never exposed in logs
- 🛡️ **Least Privilege**: Uses minimal required permissions for each operation

## Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Add tests if applicable
5. Submit a pull request

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.