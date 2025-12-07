# AgentOps - AI Agent Development Guide

This guide provides essential information for AI coding agents working on the AgentOps project.

## Project Overview

**AgentOps** is an observability and DevTool platform for AI Agents, providing session replays, LLM cost management, agent benchmarking, and framework integrations.

- **Language**: Python
- **Python Version**: >=3.7
- **License**: MIT
- **Package Manager**: pip, uv
- **Build System**: setuptools

## Project Structure

```
agentops/
├── agentops/              # Main SDK package
│   ├── llms/             # LLM provider integrations
│   │   └── providers/    # Individual provider implementations
│   ├── partners/         # Framework integrations (AutoGen, LangChain)
│   ├── client.py         # Core client implementation
│   ├── session.py        # Session management
│   ├── decorators.py     # @track_agent, @record_tool, @record_action
│   └── event.py          # Event tracking system
├── tests/                # Test suite
│   ├── core_manual_tests/
│   └── openai_handlers/
├── examples/             # Integration examples (notebooks)
├── docs/                 # Documentation (Mintlify-based)
└── .github/workflows/    # CI/CD workflows
```

## Dependencies

### Core Dependencies
- `requests>=2.0.0,<3.0.0` - HTTP client
- `psutil>=5.9.8,<6.1.0` - System monitoring
- `termcolor>=2.3.0,<2.5.0` - Terminal colors
- `PyYAML>=5.3,<7.0` - YAML parsing
- `opentelemetry-api>=1.22.0,<2.0.0` - Telemetry API
- `opentelemetry-sdk>=1.22.0,<2.0.0` - Telemetry SDK
- `opentelemetry-exporter-otlp-proto-http>=1.22.0,<2.0.0` - OTLP exporter

### Dev Dependencies
- `pytest==7.4.0` - Testing framework
- `pytest-depends` - Test dependencies
- `pytest-asyncio` - Async test support
- `pytest-mock` - Mocking
- `pytest-vcr` - HTTP interaction recording
- `pyfakefs` - Filesystem mocking
- `requests_mock==1.11.0` - Request mocking
- `ruff` - Linting and formatting
- `vcrpy>=6.0.0` (Python >= 3.8)

### CI Dependencies
- `tach~=0.9` - Module boundary enforcement

### Optional Dependencies
- `langchain==0.2.14` (Python >= 3.8.1) - LangChain integration

## Development Setup

### Installation
```bash
# Standard installation
pip install agentops

# With optional dependencies
pip install agentops[langchain]

# Development installation
pip install -e ".[dev]"

# Using uv (recommended for CI)
uv sync --group dev
uv sync --group ci
```

### Environment Variables

Key environment variables:
- `AGENTOPS_API_KEY` - Required for SDK functionality
- `OPENAI_API_KEY` - For OpenAI integration tests
- `ANTHROPIC_API_KEY` - For Anthropic integration tests
- `MISTRAL_API_KEY` - For Mistral integration tests
- `COHERE_API_KEY` - For Cohere integration tests

## Code Style and Linting

### Ruff Configuration
- **Line Length**: 120 characters
- **Tool**: Ruff for linting and formatting
- **Ignored Rules**:
  - `F401` - Unused imports
  - `E712` - Comparison to True/False
  - `E711` - Comparison to None
  - `E722` - Bare except
  - `F821` - Undefined names
  - `F841` - Unused variables

### Excluded Paths
Ruff excludes: `.git`, `.github`, `docs`, `examples`, `tests/core_manual_tests`, `venv`, `build`, `dist`, `node_modules`

### Pre-commit Hooks
```bash
# Install pre-commit
pip install pre-commit

# Run pre-commit
pre-commit run --all-files
```

Pre-commit runs:
- `ruff` - Linting with auto-fix
- `ruff-format` - Code formatting

## Testing

### Running Tests

```bash
# Run all tests with pytest
pytest

# Run with coverage
coverage run --source . -m pytest
coverage report -m

# Run with tox (tests on multiple Python versions)
tox
```

### Test Configuration
- **Framework**: pytest 7.4.0
- **Async Mode**: strict
- **Import Mode**: importlib
- **Test Paths**: `tests/`
- **Python Versions Tested**: 3.7, 3.8, 3.9, 3.10, 3.11, 3.12

### Test Types
- Unit tests in `tests/`
- Integration tests in `tests/core_manual_tests/`
- Provider-specific canary tests in `tests/core_manual_tests/providers/`
- Example notebooks tested via GitHub Actions

## Build and Release

### Building Package
```bash
# Build package
python -m build

# Install locally
pip install -e .
```

### CLI Entry Point
```bash
# CLI is available after installation
agentops
```

Entry point defined in `pyproject.toml`:
```toml
[project.scripts]
agentops = "agentops.cli:main"
```

### Publishing
Publishing is automated via GitHub Actions on push to PyPI.

## CI/CD Workflows

### AGENTS.md Validation (`agents-md-check.yml`)
- **Trigger**: Push/PR to main affecting configuration files
- **Purpose**: Ensures AGENTS.md stays synchronized with project configuration
- **Validates**:
  - Python version requirements
  - Core, dev, and CI dependencies
  - Tach module configuration
  - CLI entry points
  - Ruff configuration (line length, ignore rules)
- **Behavior**: Posts PR comment with update instructions on failure

### Python Testing (`python-testing.yml`)
- **Trigger**: Push/PR to main affecting Python files
- **Matrix**: Python 3.7, 3.8, 3.9, 3.10, 3.11, 3.12
- **Tool**: tox

### Static Analysis (`static-analysis.yaml`)
- **Trigger**: Push/PR to main affecting Python files
- **Tool**: pre-commit with ruff
- **Python Version**: 3.11.10
- **Package Manager**: uv

### Tach Check (`tach-check.yml`)
- **Trigger**: PR affecting Python files
- **Purpose**: Enforce module boundaries
- **Tool**: Tach ~0.9

### Stale PR Check (`stale-check.yml`)
- **Trigger**: Daily cron + manual
- **Stale After**: 14 days of inactivity
- **Close After**: 7 additional days
- **Label**: `stale`

### Notebook Testing (`test-notebooks.yml`)
- Tests example notebooks to ensure integrations work

### CodeCov (`codecov.yml`)
- Uploads test coverage reports

## Module Architecture (Tach)

Module dependencies enforced by Tach:
```yaml
modules:
  - path: agentops
    depends_on:
      - agentops.enums
      - agentops.log_config
  - path: agentops.enums
    depends_on: []
  - path: agentops.log_config
    depends_on: []
```

**Key Rule**: Core enums and logging config have no dependencies. Main agentops module depends on them.

## Supported LLM Providers

### Native Integrations
- **OpenAI** - Full support including async, streaming, vision
- **Anthropic** - Claude models, sync/async, streaming
- **Cohere** - Chat and streaming
- **Mistral** - Sync, async, streaming
- **Groq** - Chat completions
- **AI21** - AI21 Studio models
- **Ollama** - Local models
- **LiteLLM** - 100+ model proxy
- **Llama Stack** - Meta's Llama Stack Client (>=0.0.53)

### Framework Integrations
- **CrewAI** - Native integration via `crewai[agentops]`
- **AutoGen** - Via `agentops.init()`
- **LangChain** - Via `LangchainCallbackHandler`
- **Camel AI** - Full observability support
- **LlamaIndex** - Via global handler

## Decorators and API Patterns

### Core Decorators
```python
from agentops import track_agent, record_tool, record_action

@track_agent(name='CustomAgent')
class MyAgent:
    pass

@record_tool('ToolName')
def my_tool():
    pass

@record_action('action_name')
def my_function():
    pass
```

### Session Management
```python
import agentops

# Initialize
agentops.init(api_key="...")

# Work happens here

# End session
agentops.end_session('Success')
```

## Key Files for Modification

### Adding New LLM Provider
1. Create provider file in `agentops/llms/providers/`
2. Extend `InstrumentedProvider` base class
3. Implement tracking methods for sync/async/streaming
4. Update `agentops/llms/__init__.py`

### Adding New Framework Integration
1. Create handler in `agentops/partners/`
2. Follow pattern from `langchain_callback_handler.py`
3. Add documentation in `docs/v1/integrations/`
4. Add example notebook in `examples/`

### Modifying Core Client
- **Client Logic**: `agentops/client.py`
- **Session Logic**: `agentops/session.py`
- **Event System**: `agentops/event.py`
- **HTTP Layer**: `agentops/http_client.py`

## Documentation

### Structure
- **Docs Engine**: Mintlify
- **Location**: `docs/v1/`
- **Build**: Node.js-based (package.json in docs/)

### Doc Sections
- `docs/v1/integrations/` - Provider integrations
- `docs/v1/examples/` - Code examples
- `docs/v1/concepts/` - Core concepts
- `docs/v1/usage/` - Usage guides

## Contributing Guidelines

### Issue Tags
- `good first issue` - Beginner-friendly
- `help wanted` - Community contributions welcome

### PR Guidelines
- Follow code style (Ruff)
- Add tests for new features
- Update documentation
- Ensure CI passes (tests, static analysis, tach)

### Community
- **Discord**: Primary communication channel
- **Contact Form**: agentops.ai/contact

### Stale PR Policy
- PRs stale after 14 days of inactivity
- Auto-closed after 7 additional days
- Can be reopened if work continues

## Common Commands

```bash
# Development
pip install -e ".[dev]"
pytest
ruff check --fix .
ruff format .

# CI Tools
uv sync --group dev
uvx pre-commit run --all-files
uvx tach check

# Testing
tox
coverage run -m pytest
coverage report

# Building
python -m build
```

## Architecture Notes

### Backend and UI
- SDK is **open source**
- Backend and UI are **proprietary** (source available)
- On-prem/VPC hosting available upon request

### Observability Pipeline
- Uses OpenTelemetry for instrumentation
- OTLP HTTP exporter for data transmission
- Custom event tracking system for agent-specific events

### Session Model
- Sessions track complete agent workflows
- Events are associated with sessions
- Multi-session and cross-session analytics available

## Best Practices for AI Agents

1. **Always run tests** after making changes
2. **Use Ruff** for formatting before commits
3. **Check Tach** for module boundary violations
4. **Test integrations** with example notebooks when modifying providers
5. **Keep line length** to 120 characters max
6. **Avoid modifying** excluded paths (docs, examples, tests/core_manual_tests)
7. **Update documentation** when adding features
8. **Follow existing patterns** when adding new providers/integrations
