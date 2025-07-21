# 📦 Taskly DynamoDB SAM Stack

This repository contains the AWS SAM template and GitHub Actions workflow to provision a **DynamoDB table** for the **Taskly Todo App**. It supports automatic deployment via CI/CD and includes advanced configurations such as composite Global Secondary Indexes (GSIs).

---

## 📚 Project Overview

The goal of this project is to provision a DynamoDB table backend for a containerized **Taskly Todo App** running on ECS. This setup includes:

- DynamoDB table with partition + sort keys for better query flexibility
- GSIs for querying by `status` and `dueDate`
- GitHub Actions workflow for CI/CD
- SAM CLI-based infrastructure deployment
- Uses CloudFormation execution role and private S3 bucket

---

## 🛠️ Technologies Used

- **AWS SAM (Serverless Application Model)**
- **AWS DynamoDB**
- **GitHub Actions**
- **AWS CloudFormation**
- **IAM OIDC Role for GitHub**
- **S3 Bucket for artifact storage**

---

## 🗃️ DynamoDB Table Design

| Field       | Type | Description                     |
|-------------|------|---------------------------------|
| `id`        | `S`  | Unique identifier (PK)          |
| `createdAt` | `S`  | ISO timestamp (SK for sorting)  |
| `title`     | `S`  | Title of the task               |
| `description` | `S`| Details about the task          |
| `status`    | `S`  | Status (e.g., pending, done)    |
| `dueDate`   | `S`  | Task deadline (sortable)        |

### Global Secondary Indexes (GSIs)

- `StatusIndex`: (`status` as HASH, `dueDate` as RANGE)
- `DueDateIndex`: (`dueDate` as HASH, `status` as RANGE)

---

## 🚀 Deployment

### 1. 🔧 Prerequisites

- AWS account
- Pre-created:
  - **S3 bucket** for deployment artifacts
  - **GitHub OIDC role** for CI/CD
  - **CloudFormation Execution Role**

Add the following secrets to your GitHub repo:

| Secret Name        | Description                             |
|--------------------|-----------------------------------------|
| `AWS_REGION`       | AWS region (e.g., `eu-central-1`)       |
| `AWS_ACCOUNT_ID`   | Your 12-digit AWS account ID            |
| `SAM_S3_BUCKET`    | S3 bucket for deployment artifacts      |

---

### 2. 📁 Repository Structure
.
├── .github
│   └── workflows
│       └── deploy.yaml       # GitHub Actions Workflow
├── template.yaml             # SAM Template for DynamoDB
├── samconfig.toml            # Optional SAM config (for local testing)
└── README.md

---

### 3. ⚙️ CI/CD Pipeline

The GitHub Actions workflow:
- Lints your template with `cfn-lint`
- Assumes your GitHub OIDC role
- Installs the AWS SAM CLI
- Deploys the DynamoDB stack using `sam deploy`

✅ Auto-triggered on pushes to `main`.

---

## 🔒 Security Considerations

- Uses **OIDC authentication** (no long-term secrets)
- Uses **CloudFormation execution role** to scope permissions
- SAM stack deploys with **`CAPABILITY_NAMED_IAM`**

---

## 🧼 Cleanup

To remove the stack from AWS:

```bash
sam delete --stack-name taskly-dynamodb

```

---

## 🧠 Additional Notes
- createdAt helps in sorting todos by recency
- GSIs allow efficient filtering of todos by due date or status