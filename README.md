# GitHub Actions

A collection of reusable GitHub Actions for common development workflows.

## Available Actions

### 🔄 bump_version
Automatically bump versions using commit messages with python-semantic-release. Analyzes conventional commits to determine version bumps and creates git tags.

### ✅ check_code
Install dependencies and run comprehensive code quality checks including commit message validation, linting, formatting, type checking, and tests for Python projects.

### 🐳 publish_ecr
Build and push Docker images to Amazon ECR with caching and OIDC authentication.

## Usage

Reference these actions in your workflows using:

```yaml
- uses: cahido/actions/[action_name]@main
```

Each action has its own README with detailed usage instructions and examples.