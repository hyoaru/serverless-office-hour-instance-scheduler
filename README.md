# EC2 Instance Scheduler

A highly modular, containerized AWS Lambda function designed to manage EC2 instance states (Start/Stop). Built with Python 3.12+, Pydantic, and AWS SDK (Boto3).

This repository is a component of the [hyoacloud-aws-infrastructure](https://github.com/hyoaru/hyoacloud-aws-infrastructure) ecosystem.

## Architecture

This project uses a Strategy Design Pattern to allow for flexible instance selection and state management.

- Strategies: Define how we choose to act (e.g., start_instances_by_filters).
- Commands: Low-level wrapper around Boto3 calls for standardizing EC2 actions.
- Helper: Orchestrates the communication between strategies and commands.

## Repository Structure

```
.
├── app
│ ├── services
│ │ └── ec2_instance_state_manager
│ │ ├── helper/ # Orchestration logic
│ │ ├── commands/ # Boto3 wrappers (Start/Stop/Filter)
│ │ └── strategies/ # Business logic (Factory pattern)
│ └── utilities
│ └── logger/ # Standardized JSON logging
├── Dockerfile # Lambda-optimized container config
├── pyproject.toml # Project metadata & dependencies (uv/pip)
└── uv.lock # Deterministic dependency locking
```

## Getting Started

### Prerequisites

- Docker installed locally.
- AWS CLI configured with appropriate permissions.
- uv (For local dependency management).

### Local Development

1. Clone the repo:

```Bash
git clone <repo-url>
cd serverless-office-hour-instance-scheduler
```

2. Install dependencies (using uv):

```Bash
uv sync
```

3. Run the Lambda locally: You can execute the handler directly using uv but configure the code to mock the event and context

```Bash
uv run lambda
```

## CI/CD & Deployment

This repository is decoupled from the main monorepo. Deployment is handled via GitHub Actions and Amazon ECR.

### Deployment Workflow

1. Push to main: Triggers the GitHub Action.
2. Docker Build: Builds the Lambda-ready image using the provided Dockerfile.
3. ECR Push: Authenticates via OIDC and pushes the image to Amazon ECR.
4. Lambda Update: Updates the CloudFormation stack/Lambda function with the new :latest or :sha image tag.
