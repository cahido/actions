# Publish Docker Image to ECR Action

A reusable GitHub Action for building and publishing Docker images to Amazon Elastic Container Registry (ECR).

## Description

This action builds a Docker image from the current repository context and publishes it to Amazon ECR with both a specific tag and the `:latest` tag. It includes Docker layer caching for improved build performance.

## Features

- ✅ Builds Docker images using Docker Buildx
- ✅ Publishes to Amazon ECR
- ✅ Supports OIDC authentication with AWS
- ✅ Docker layer caching for faster builds
- ✅ Automatically tags with both specific version and `:latest`

## Inputs

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `image_name` | Name of the Docker image | Yes | - |
| `aws_region` | AWS region for ECR | Yes | - |
| `aws_role_arn` | ARN of the IAM role to assume | Yes | - |
| `image_tag` | Tag for the Docker image | Yes | - |

## Usage

```yaml
- name: Publish Docker image
  uses: ./.github/actions/publish_image
  with:
    image_name: 'my-app'
    aws_region: 'eu-central-1'
    aws_role_arn: 'arn:aws:iam::123456789012:role/GitHubECRPushRole'
    image_tag: 'v1.2.3'
```

## Requirements

- Repository with a `Dockerfile`
- AWS IAM role configured for GitHub OIDC
- Amazon ECR repository created
- The calling workflow must have `id-token: write` permission

```yaml
permissions:
  id-token: write
```

## What it does

1. **Docker setup**: Configures Docker Buildx for enhanced build capabilities
2. **AWS authentication**: Uses OIDC to assume the specified IAM role
3. **ECR login**: Authenticates with Amazon ECR
4. **Image build**: Builds the Docker image using the repository context
5. **Image push**: Publishes the image with two tags:
   - Specific tag (from `image_tag` input)
   - `:latest` tag for caching and convenience
6. **Layer caching**: Uses registry caching to speed up subsequent builds

## AWS Setup Requirements

### IAM Role
Create an IAM role with the following trust policy:

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
          "token.actions.githubusercontent.com:sub": "repo:OWNER/REPO:ref:refs/heads/BRANCH"
        }
      }
    }
  ]
}
```

### IAM Permissions
Attach a policy with ECR permissions:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "ecr:GetAuthorizationToken",
        "ecr:BatchCheckLayerAvailability",
        "ecr:GetDownloadUrlForLayer",
        "ecr:BatchGetImage",
        "ecr:InitiateLayerUpload",
        "ecr:UploadLayerPart",
        "ecr:CompleteLayerUpload",
        "ecr:PutImage"
      ],
      "Resource": "*"
    }
  ]
}
```

## Example workflow

```yaml
name: Build and Deploy
on:
  push:
    branches: [main]

jobs:
  deploy:
    runs-on: ubuntu-latest
    permissions:
      id-token: write
    steps:
      - uses: actions/checkout@v4
      
      - name: Publish to ECR
        uses: ./.github/actions/publish_image
        with:
          image_name: 'my-application'
          aws_region: 'eu-central-1'
          aws_role_arn: '${{ vars.AWS_ROLE_ARN }}'
          image_tag: '${{ github.sha }}'
```

## Caching behavior

The action uses Docker layer caching with the `:latest` tag as the cache source. This means:
- First build: Full build (no cache)
- Subsequent builds: Reuses layers from the previous `:latest` image
- Each successful build updates the `:latest` tag for future caching
