# AI Digital Twin

This project is a web application that serves as an **AI-powered digital** twin, providing users with a course companion to assist with AI deployment and related queries. The application features a frontend built with React and Tailwind CSS, and a backend API for handling chat interactions.

---

## Links

- Repo: https://github.com/Ngahu/twin
- Live app: https://d7o5h2kp8zvr60.cloudfront.net

---

## Table of Contents

- [Features](#features)
- [Project Structure](#project-structure)
- [Getting Started](#getting-started)
- [Environment Variables](#environment-variables)
- [Deployment](#deployment)
- [Destroying Infrastructure](#destroying-infrastructure)
- [License](#license)

---

## Features

- **Backend**: Python-based backend with FastAPI, AWS Lambda, and other utilities.
- **Frontend**: Next.js application for a modern, responsive user interface.
- **Infrastructure**: Managed with Terraform for AWS resources.
- **Environment Management**: `.env` files for configuration.
- **CI/CD**: GitHub Actions for automated deployment and destruction workflows.

---

## Project Structure

```plaintext
├── backend/                # Backend services and Lambda functions
│   ├── lambda-package/     # Lambda deployment package
│   ├── server.py           # FastAPI server entry point
│   ├── deploy.py           # Deployment script
│   └── requirements.txt    # Python dependencies
├── frontend/               # Frontend application (Next.js)
│   ├── pages/              # Next.js pages
│   ├── components/         # React components
│   └── package.json        # Node.js dependencies
├── terraform/              # Terraform configuration for AWS infrastructure
├── scripts/                # Utility scripts for deployment and destruction
├── .github/workflows/      # GitHub Actions workflows
└── README.md               # Project documentation
```

---

## Getting Started

### Prerequisites

- **Node.js**: v20 or higher
- **Python**: v3.10 or higher
- **Terraform**: v1.5 or higher
- **AWS CLI**: Configured with appropriate credentials

### Installation

1. **Clone the repository:**
   ```bash
   git clone https://github.com/adisco4420/ai-digital-twin.git
   cd twin
   ```

2. **Install backend dependencies:**
   ```bash
   cd backend
   uv add -r requirements.txt
   ```

3. **Install frontend dependencies:**
   ```bash
   cd ../frontend
   npm install
   ```

---

## Environment Variables

### Backend

Create a `.env` file in the `backend/` directory. Use `.env.example` as a template.

### Frontend

Create a `.env.local` file in the `frontend/` directory. Example:
```env
NEXT_PUBLIC_API_URL=https://your-api-url.com
```

---

## Deployment

### Local Development

1. **Start the backend:**
   ```bash
   cd backend
   uv run uvicorn server:app --reload
   ```

2. **Start the frontend:**
   ```bash
   cd frontend
   npm run dev
   ```

3. Open your browser at [http://localhost:3000](http://localhost:3000).

### Deployment

Run the deployment script:
```bash
scripts/deploy.sh
```


Deployments are **automated with GitHub Actions**, **declared with Terraform**, and **run on AWS**.

### GitHub Actions

- **Deploy** (`.github/workflows/deploy.yml`): runs on pushes to `main` and via **workflow dispatch** so you can choose **dev**, **test**, or **prod**. It checks out the repo, assumes an **IAM role over OIDC** (no long-lived AWS keys in the repo), sets up Python, Terraform, and Node, then runs **`scripts/deploy.sh`** for the selected environment.
- **Destroy** (`.github/workflows/destroy.yml`): manual workflow to tear down an environment after confirmation.

Repository **secrets** (for example `AWS_ROLE_ARN`, `AWS_ACCOUNT_ID`, `DEFAULT_AWS_REGION`, and optional `OPENROUTER_API_KEY` passed through as `TF_VAR_*`) supply credentials and Terraform variables the workflow must not hardcode.

### Terraform

- Configuration lives under **`terraform/`**. It defines AWS resources (for example Lambda, API Gateway, S3, CloudFront, IAM) for the Digital Twin stack.
- **Remote state** is stored in **S3** with **DynamoDB** for locking, matching the backend settings used by `deploy.sh` / `destroy.sh` (bucket and table names follow the `twin-terraform-*` convention for this project).
- **Workspaces** (or equivalent environment selection in scripts) separate **dev**, **test**, and **prod** state so each environment can be managed independently.

### AWS

- The running system is **serverless and static hosting on AWS**: API and compute on **Lambda**, assets and site delivery via **S3** and **CloudFront**, with supporting services as defined in Terraform.
- **First-time setup** (OIDC role for Actions, state bucket, lock table) is done in your AWS account outside the app workflow, then CI/CD applies and updates the stack from the repo.


---

## Destroying Infrastructure

To destroy the infrastructure, run:
```bash
scripts/destroy.sh
```

---

## License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.