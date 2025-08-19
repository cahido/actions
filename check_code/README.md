# Check Code Action

A reusable GitHub Action for Python code quality checks using Poetry, commitizen, Ruff, Black, Mypy, and pytest.

## Description

This action sets up a Python environment, installs project dependencies using Poetry, and runs a comprehensive suite of code quality checks including commit message validation, linting, formatting, type checking, and testing.

## Features

- ✅ Configurable Python version
- ✅ Automatic Poetry dependency installation
- ✅ Commit message convention checking with commitizen
- ✅ Ruff linting
- ✅ Black formatting verification
- ✅ Mypy type checking
- ✅ pytest test execution

## Inputs

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `python-version` | Python version to use | Yes | None |
| `branch` | The git branch that commitizen checks against | No | `"main"` |

## Usage

```yaml
- name: Check code quality
  uses: ./.github/actions/check_code
  with:
    python-version: '3.11'
    branch: 'main'  # Optional, defaults to 'main'
```

## Requirements

- Python project with Poetry configuration (`pyproject.toml`)
- Poetry dependencies configured with dev dependencies for:
  - `commitizen` (commit message validation)
  - `ruff` (linting)
  - `black` (formatting)
  - `mypy` (type checking)
  - `pytest` (testing)

## What it does

1. **Python setup**: Installs the specified Python version
2. **Dependencies**: Installs Poetry and project dependencies
3. **Commit validation**: Runs `cz check --rev-range [branch]..HEAD` to validate commit message conventions
4. **Linting**: Runs `ruff check .` to validate code style and catch potential issues
5. **Formatting**: Runs `black --check .` to ensure consistent code formatting
6. **Type checking**: Runs `mypy .` to verify type annotations
7. **Testing**: Runs `pytest` to execute the test suite

## Expected pyproject.toml structure

Your `pyproject.toml` should include the development dependencies:

```toml
[tool.poetry.group.dev.dependencies]
commitizen = "^3.0.0"
ruff = "^0.1.0"
black = "^23.0.0"
mypy = "^1.0.0"
pytest = "^7.0.0"
```

## Example workflow

```yaml
name: CI
on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Run code quality checks
        uses: ./.github/actions/check_code
        with:
          python-version: '3.11'
```

## Failure behavior

The action will fail if any of the following tools report issues:
- Commitizen finds commit message convention violations
- Ruff finds linting violations
- Black detects formatting inconsistencies
- Mypy discovers type errors
- pytest encounters test failures

## Local development

Test locally with act:

```bash
act -j test
```
