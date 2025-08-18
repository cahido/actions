# GitHub Actions

A collection of reusable GitHub Actions for common development workflows.

## Available Actions

### 🔄 bump_version
Automatically bump Poetry version, commit changes, and create git tags.

### ✅ check_code
Install dependencies and run linting and tests for Python projects.

### 🐳 publish_image
Build and push Docker images to Amazon ECR.

## Usage

Reference these actions in your workflows using:

```yaml
- uses: cahido/actions/[action_name]@main
```

Each action has its own README with detailed usage instructions and examples.