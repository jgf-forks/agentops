# GitHub Actions Workflows

This directory contains CI/CD workflows for the AgentOps project.

## Workflows

### `agents-md-check.yml` - AGENTS.md Validation
**Purpose**: Ensures AGENTS.md stays synchronized with project configuration files.

**Triggers**:
- Push to main affecting: `pyproject.toml`, `tach.yml`, `.github/workflows/**`, `.pre-commit-config.yaml`, `AGENTS.md`
- Pull requests affecting the same files
- Manual workflow dispatch

**What it validates**:
- Python version requirement matches `pyproject.toml`
- All core dependencies are documented
- Dev and CI dependencies are documented
- Tach module configuration is documented
- CLI entry points are documented
- Ruff configuration (line length, ignore rules) is documented

**Behavior on failure**:
- Posts a comment on PRs with instructions to update AGENTS.md
- Blocks merge until AGENTS.md is synchronized

### `python-testing.yml` - Python Tests
Tests across Python 3.7-3.12 using tox.

### `static-analysis.yaml` - Static Analysis
Runs pre-commit hooks including ruff linting and formatting.

### `tach-check.yml` - Module Boundary Enforcement
Validates module dependencies using Tach.

### `stale-check.yml` - Stale PR Management
Marks PRs stale after 14 days of inactivity, closes after 7 additional days.

### `test-notebooks.yml` - Notebook Testing
Tests example notebooks to ensure integrations work correctly.

### `codecov.yml` - Code Coverage
Uploads test coverage reports to CodeCov.

### `python-publish.yml` - PyPI Publishing
Publishes package to PyPI on release.

### `add-notebook-examples-to-docs.yml` - Documentation
Adds notebook examples to documentation.

## Maintaining AGENTS.md

When you modify project configuration files, update AGENTS.md accordingly:

1. **Dependencies** (`pyproject.toml` changes):
   - Update core, dev, or CI dependency lists
   - Update version requirements

2. **Module Structure** (`tach.yml` changes):
   - Update module dependency architecture section
   - Document any new module boundaries

3. **Workflows** (new `.github/workflows/*.yml` files):
   - Add workflow description to CI/CD section
   - Document triggers and purpose

4. **Code Style** (`.pre-commit-config.yaml` or ruff config changes):
   - Update linting rules
   - Update formatting requirements

The `agents-md-check.yml` workflow will validate your changes automatically.
