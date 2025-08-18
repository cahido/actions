# Poetry Version Bump Action

A reusable GitHub Action that bumps the version in `pyproject.toml` using Poetry, commits the change, creates a git tag, and pushes to the repository.

## Description

This action automatically handles semantic versioning for Python projects using Poetry. It includes safety checks to prevent duplicate version bumps and handles all git operations automatically.

## Features

- ✅ Bumps version using Poetry's semantic versioning
- ✅ Prevents duplicate version bumps by checking the last commit message
- ✅ Creates git tags with version prefix (e.g., `v1.2.3`)
- ✅ Automatically commits and pushes changes
- ✅ Configurable git user for commits

## Inputs

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `bump` | Which bump to apply (patch, minor, major) | Yes | `"patch"` |
| `git_user_name` | Git user.name to use for commits | No | `"github-actions[bot]"` |
| `git_user_email` | Git user.email to use for commits | No | `"github-actions[bot]@users.noreply.github.com"` |

## Outputs

| Output | Description |
|--------|-------------|
| `version` | The new version after bump (e.g., `1.2.3`) |

## Usage

```yaml
- name: Bump version
  id: version_bump
  uses: ./.github/actions/bump_version
  with:
    bump: 'minor'  # or 'patch', 'major'
    git_user_name: 'Your Name'
    git_user_email: 'your.email@example.com'

- name: Use new version
  run: |
    echo "New version: ${{ steps.version_bump.outputs.version }}"
    # You can use this version for further steps like creating releases, updating documentation, etc.
```

## Requirements

- Python project with Poetry configuration (`pyproject.toml`)
- Repository with write permissions
- The calling workflow must have `contents: write` permission

```yaml
permissions:
  contents: write
```

## What it does

1. **Safety check**: Examines the last commit message to prevent duplicate version bumps
2. **Install Poetry**: Ensures Poetry is available in the environment
3. **Version bump**: Uses `poetry version [bump]` to update version in `pyproject.toml`
4. **Git operations**: 
   - Configures git user
   - Commits the version change
   - Creates a git tag with `v` prefix (e.g., `v1.2.3`)
   - Pushes both commit and tag to origin

## Skip behavior

The action will automatically skip execution if the last commit message starts with "Bump version to". This prevents infinite loops in automated workflows.

## Example workflow

```yaml
name: Release
on:
  workflow_dispatch:
    inputs:
      bump_type:
        description: 'Version bump type'
        required: true
        default: 'patch'
        type: choice
        options:
          - patch
          - minor
          - major

jobs:
  release:
    runs-on: ubuntu-latest
    permissions:
      contents: write
    steps:
      - uses: actions/checkout@v4
        with:
          token: ${{ secrets.GITHUB_TOKEN }}
          
      - name: Bump version
        uses: ./.github/actions/bump_version
        with:
          bump: ${{ github.event.inputs.bump_type }}
```