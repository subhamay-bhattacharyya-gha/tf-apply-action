# Multi-Cloud Terraform Apply Action

A GitHub Action that applies Terraform plans and generates detailed summaries with support for AWS S3 and HCP Terraform Cloud backends with built-in OIDC authentication.

## Features

- 🌐 **Multi-Cloud Support**: Works with AWS, Azure, and GCP using their respective backends
- ☁️ **HCP Terraform Cloud**: Full support for Terraform Cloud remote backend
- � **DBuilt-in OIDC Authentication**: Integrated authentication for AWS, Azure, and GCP
- � ***Detailed Summaries**: Generates comprehensive GitHub Step Summaries with resource changes and outputs
- � ***Secure State Management**: Supports S3 backend with encryption and locking, plus HCP Terraform Cloud
- � **CIs/CD Ready**: Configurable state keys for different deployment strategies
- 📋 **Resource Tracking**: Shows affected resources with actions and types
- 🐛 **Debug Support**: Built-in input debugging for troubleshooting

## Usage

## Usage

### AWS S3 Backend

```yaml
- name: Apply Terraform (AWS)
  uses: subhamay-bhattacharyya-gha/tf-apply-action@main
  with:
    backend-type: 's3'
    cloud-provider: 'aws'
    s3-bucket: 'my-terraform-state-bucket'
    s3-region: 'us-east-1'
    s3-key-prefix: 'environments/prod'  # optional
    aws-region: ${{ secrets.AWS_REGION }}
    aws-role-to-assume: ${{ secrets.AWS_ROLE_ARN }}
    terraform-dir: 'infrastructure'
    ci-pipeline: 'true'
```

> **Note:** Configure these AWS secrets in your GitHub repository:
> - `AWS_REGION`: Your AWS region (e.g., `us-east-1`)
> - `AWS_ROLE_ARN`: Your IAM role ARN (e.g., `arn:aws:iam::123456789012:role/GitHubActionsRole`)
> 
> Never hardcode IAM role ARNs or regions in your workflow files. Always store sensitive values as GitHub repository secrets for security.

### Azure with S3-Compatible Backend

```yaml
- name: Apply Terraform (Azure)
  uses: subhamay-bhattacharyya-gha/tf-apply-action@main
  with:
    backend-type: 's3'  # Uses S3-compatible backend configuration
    cloud-provider: 'azure'
    s3-bucket: 'my-terraform-state-bucket'  # Your backend bucket
    s3-region: 'us-east-1'  # Backend region
    azure-client-id: ${{ secrets.AZURE_CLIENT_ID }}
    azure-tenant-id: ${{ secrets.AZURE_TENANT_ID }}
    azure-subscription-id: ${{ secrets.AZURE_SUBSCRIPTION_ID }}
    terraform-dir: 'infrastructure'
    ci-pipeline: 'true'
```

> **Note:** Configure these Azure secrets in your GitHub repository:
> - `AZURE_CLIENT_ID`: Your Azure application (client) ID
> - `AZURE_TENANT_ID`: Your Azure tenant ID
> - `AZURE_SUBSCRIPTION_ID`: Your Azure subscription ID
> 
> These values are required for OIDC authentication with Azure and should never be hardcoded in workflow files.

### GCP with S3-Compatible Backend

```yaml
- name: Apply Terraform (GCP)
  uses: subhamay-bhattacharyya-gha/tf-apply-action@main
  with:
    backend-type: 's3'  # Uses S3-compatible backend configuration
    cloud-provider: 'gcp'
    s3-bucket: 'my-terraform-state-bucket'  # Your backend bucket
    s3-region: 'us-east-1'  # Backend region
    gcp-wif-provider: ${{ secrets.GCP_WIF_PROVIDER }}
    gcp-service-account: ${{ secrets.GCP_SERVICE_ACCOUNT }}
    terraform-dir: 'infrastructure'
    ci-pipeline: 'true'
```

> **Note:** Configure these GCP secrets in your GitHub repository:
> - `GCP_WIF_PROVIDER`: Your Workload Identity Federation provider (e.g., `projects/123456789/locations/global/workloadIdentityPools/my-pool/providers/my-provider`)
> - `GCP_SERVICE_ACCOUNT`: Your service account email (e.g., `my-service-account@my-project.iam.gserviceaccount.com`)
> 
> These values are required for OIDC authentication with Google Cloud and should be stored securely as repository secrets.

### HCP Terraform Cloud Backend

```yaml
- name: Apply Terraform (HCP Terraform Cloud)
  uses: subhamay-bhattacharyya-gha/tf-apply-action@main
  with:
    backend-type: 'remote'
    cloud-provider: 'aws'  # or 'azure', 'gcp' for resource authentication
    tfc-token: ${{ secrets.TF_API_TOKEN }}
    aws-region: ${{ secrets.AWS_REGION }}  # Required for cloud provider authentication
    aws-role-to-assume: ${{ secrets.AWS_ROLE_ARN }}
    terraform-dir: 'infrastructure'
    ci-pipeline: 'true'
```

> **Note:** Configure these secrets for HCP Terraform Cloud with AWS authentication:
> - `TF_API_TOKEN`: Your HCP Terraform Cloud API token
> - `AWS_REGION`: AWS region for resource authentication
> - `AWS_ROLE_ARN`: IAM role ARN for OIDC authentication
> 
> When using different cloud providers with HCP Terraform Cloud, replace the AWS authentication inputs with the appropriate Azure or GCP secrets as shown in the examples above.

## Inputs

## Inputs

### Common Inputs

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `backend-type` | Backend type (`s3` for S3-compatible backends, `remote` for HCP Terraform Cloud) | ❌ | `s3` |
| `cloud-provider` | Cloud provider for authentication (`aws`, `azure`, `gcp`) | ✅ | - |
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

### AWS Authentication Inputs (for cloud-provider: 'aws')

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `aws-region` | AWS region for authentication | ✅ (for AWS) | - |
| `aws-role-to-assume` | AWS IAM role ARN to assume | ✅ (for AWS) | - |

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

### Backend Configuration

#### AWS S3 Backend
The action configures the S3 backend automatically using the provided inputs. Your Terraform configuration should include:

```hcl
terraform {
  backend "s3" {
    # Configuration will be provided via -backend-config flags
  }
}
```

#### Azure/GCP with S3-Compatible Backend
For Azure and GCP, you can use S3-compatible storage services. Configure your Terraform backend accordingly:

```hcl
terraform {
  backend "s3" {
    # Your S3-compatible storage configuration
    bucket = "your-state-bucket"
    key    = "terraform.tfstate"
    region = "your-region"
    # Additional S3-compatible configuration
  }
}
```

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
          s3-bucket: 'my-terraform-state-bucket'
          s3-region: 'us-east-1'
          s3-key-prefix: 'environments/prod'
          aws-region: ${{ secrets.AWS_REGION }}
          aws-role-to-assume: ${{ secrets.AWS_ROLE_ARN }}
          terraform-dir: 'infrastructure'
          ci-pipeline: 'true'
```

> **Note:** Configure the following secrets in your GitHub repository:
> - `AWS_REGION`: Your AWS region (e.g., `us-east-1`)
> - `AWS_ROLE_ARN`: Your IAM role ARN (e.g., `arn:aws:iam::123456789012:role/GitHubActionsRole`)

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
          aws-region: ${{ secrets.AWS_REGION }}
          aws-role-to-assume: ${{ secrets.AWS_ROLE_ARN }}
          # Azure authentication (if azure selected)
          azure-client-id: ${{ secrets.AZURE_CLIENT_ID }}
          azure-tenant-id: ${{ secrets.AZURE_TENANT_ID }}
          azure-subscription-id: ${{ secrets.AZURE_SUBSCRIPTION_ID }}
          # GCP authentication (if gcp selected)
          gcp-wif-provider: ${{ secrets.GCP_WIF_PROVIDER }}
          gcp-service-account: ${{ secrets.GCP_SERVICE_ACCOUNT }}
          terraform-dir: 'infrastructure'
```

> **Note:** Configure these secrets in your GitHub repository settings:
> - **AWS**: `AWS_REGION`, `AWS_ROLE_ARN`
> - **Azure**: `AZURE_CLIENT_ID`, `AZURE_TENANT_ID`, `AZURE_SUBSCRIPTION_ID`
> - **GCP**: `GCP_WIF_PROVIDER`, `GCP_SERVICE_ACCOUNT`
> - **HCP Terraform Cloud**: `TF_API_TOKEN`
> 
> Never commit sensitive values directly in your workflow files. Always use GitHub repository secrets for authentication credentials and resource identifiers.

## Action Workflow

The action follows this optimized sequence:

1. **🐛 Debug - Print All Inputs** - Shows all input values for troubleshooting
2. **✅ Validate Configuration** - Validates required inputs based on backend and cloud provider
3. **📋 Set Checkout Ref** - Determines which Git reference to checkout
4. **📥 Checkout Repo** - Checks out the repository code
5. **🔧 Setup Terraform** - Installs and configures Terraform
6. **📦 Download Terraform Plan Artifact** - Downloads the plan file from previous workflow
7. **🔐 Configure Cloud Authentication** - Sets up OIDC authentication for the selected cloud provider
8. **☁️ Setup TFC Credentials** - Configures HCP Terraform Cloud credentials (if using remote backend)
9. **🚀 Initialize Remote Backend** - Initializes HCP Terraform Cloud backend (if using remote backend)
10. **🔑 Generate State Key** - Creates S3 state key with optional prefix (if using S3 backend)
11. **📡 Initialize S3 Backend** - Initializes S3 backend with full configuration (if using S3 backend)
12. **⚡ Terraform Apply with Summary** - Applies the plan and generates detailed summary

## Output

The action generates a comprehensive GitHub Step Summary that includes:

- 🔧 **Backend Configuration**: Shows backend type and configuration details
- 🔐 **Authentication Details**: Displays authentication method and key identifiers
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