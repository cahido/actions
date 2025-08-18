# Check Code Action

A reusable GitHub Action for Python code quality checks using Poetry, Ruff, Black, Mypy, and pytest.

## Description

This action sets up a Python environment, installs project dependencies using Poetry, and runs a comprehensive suite of code quality checks including linting, formatting, type checking, and testing.

## Features

- ✅ Configurable Python version
- ✅ Automatic Poetry dependency installation
- ✅ Ruff linting
- ✅ Black formatting verification
- ✅ Mypy type checking
- ✅ pytest test execution

## Inputs

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `python-version` | Python version to use | No | `"3.11"` |

## Usage

```yaml
- name: Check code quality
  uses: ./.github/actions/check_code
  with:
    python-version: '3.11'
```

## Requirements

- Python project with Poetry configuration (`pyproject.toml`)
- Poetry dependencies configured with dev dependencies for:
  - `ruff` (linting)
  - `black` (formatting)
  - `mypy` (type checking)
  - `pytest` (testing)

## What it does

1. **Python setup**: Installs the specified Python version
2. **Dependencies**: Installs Poetry and project dependencies
3. **Linting**: Runs `ruff check .` to validate code style and catch potential issues
4. **Formatting**: Runs `black --check .` to ensure consistent code formatting
5. **Type checking**: Runs `mypy .` to verify type annotations
6. **Testing**: Runs `pytest` to execute the test suite

## Expected pyproject.toml structure

Your `pyproject.toml` should include the development dependencies:

```toml
[tool.poetry.group.dev.dependencies]
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
- Ruff finds linting violations
- Black detects formatting inconsistencies
- Mypy discovers type errors
- pytest encounters test failures

## Local development

Test locally with act:

```bash
act -j test
```
