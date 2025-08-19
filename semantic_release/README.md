# Semantic Release Action

A reusable GitHub Action that automatically bumps versions using commit messages, updates `pyproject.toml`, commits changes, creates git tags, and pushes to the repository using python-semantic-release.

## Description

This action automatically handles semantic versioning for Python projects based on conventional commit messages. It analyzes commit messages to determine the appropriate version bump (patch, minor, major) and handles all git operations automatically.

## Features

- ✅ Automatic version bumping based on conventional commit messages
- ✅ Updates version in `pyproject.toml` 
- ✅ Creates git tags with version prefix (e.g., `v1.2.3`)
- ✅ Automatically commits and pushes changes
- ✅ Configurable git user for commits
- ✅ Outputs whether a release was created and the current version

## Inputs

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `github_token` | GitHub token for pushing commits/tags | Yes | None |
| `git_user_name` | Git user.name to use for commits | No | `"github-actions[bot]"` |
| `git_user_email` | Git user.email to use for commits | No | `"github-actions[bot]@users.noreply.github.com"` |

## Outputs

| Output | Description |
|--------|-------------|
| `released` | Whether a new release was created (e.g., `true` or `false`) |
| `version` | The latest version (e.g., `1.2.3`) |

## Usage

```yaml
- name: Semantic Release
  uses: ./.github/actions/semantic_release
  with:
    github_token: ${{ secrets.GITHUB_TOKEN }}
    git_user_name: 'Your Name'
    git_user_email: 'your.email@example.com'
```

### Using the outputs

```yaml
- name: Semantic Release
  id: release
  uses: ./.github/actions/semantic_release
  with:
    github_token: ${{ secrets.GITHUB_TOKEN }}

- name: Use release info
  if: steps.release.outputs.released == 'true'
  run: |
    echo "Version: ${{ steps.release.outputs.version }}"
    # You can use these outputs for further steps like creating releases, notifications, etc.
```

## Requirements

- Python project with Poetry configuration (`pyproject.toml`)
- Poetry configuration must contain settings for semantic release

```toml
[tool.semantic_release]
version_toml = ["pyproject.toml:project.version"]
commit_message = "chore(release): {version}"
```

- Repository with write permissions
- The calling workflow must have `contents: write` permission
- **Important**: Checkout must be done with full git history (`fetch-depth: 0`)

```yaml
permissions:
  contents: write

steps:
  - uses: actions/checkout@v4
    with:
      fetch-depth: 0  # Required for python-semantic-release
      token: ${{ secrets.GITHUB_TOKEN }}
```

## What it does

1. **Git setup**: Configures git user credentials
2. **Install tool**: Installs python-semantic-release
3. **Version analysis**: Analyzes commit messages since last release to determine version bump
4. **Release creation**: If changes warrant a release:
   - Updates version in `pyproject.toml`
   - Commits the version change
   - Creates a git tag with `v` prefix (e.g., `v1.2.3`)
   - Pushes both commit and tag to origin
5. **Output**: Sets outputs indicating whether a release was created and the current version

## Commit message conventions

This action uses conventional commits. Examples:

- `feat: add new feature` → minor version bump
- `fix: resolve bug` → patch version bump  
- `feat!: breaking change` → major version bump
- `docs: update README` → no version bump

## Example workflow

```yaml
name: Release
on:
  push:
    branches: [main]

jobs:
  release:
    runs-on: ubuntu-latest
    permissions:
      contents: write
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0
          token: ${{ secrets.GITHUB_TOKEN }}
          
      - name: Semantic Release
        id: release
        uses: ./.github/actions/bump_version
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          
      - name: Create GitHub Release
        if: steps.release.outputs.released == 'true'
        uses: actions/create-release@v1
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
        with:
          tag_name: v${{ steps.release.outputs.version }}
          release_name: Release ${{ steps.release.outputs.version }}
```
