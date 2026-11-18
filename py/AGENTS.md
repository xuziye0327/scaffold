# Agent Development Guidelines

## Core Rules

- **NEVER** create separate documentation files unless explicitly requested, including design documents, architecture proposals, implementation plans, work summaries, usage guidelines, or benchmark reports; **DO** provide a brief explanation directly in your response instead.

## Use uv

- Use `uv` for Python environments, dependencies, and command execution. Do not introduce another package manager or lockfile.
- Install existing dependencies with `uv sync --locked`. If the manifest and lockfile are inconsistent, investigate before updating the lockfile.
- Run project tools with `uv run --locked` to use the project's declared dependencies without silently updating the lockfile.
- Use uv commands for dependency changes:
  ```sh
  uv add <dependency>
  uv add --dev <dependency>
  uv remove <dependency>
  uv remove --dev <dependency>
  ```
- Obtain approval as described below before changing dependencies.
- Edit project metadata and tool configuration directly in `pyproject.toml` when needed. Use uv commands for dependency changes whenever supported.
- Preserve dependency constraints, dependency groups, extras, markers, and registry or Git sources unrelated to the task.
- Let uv generate and maintain `uv.lock`; never edit it by hand. Review manifest and lockfile changes to ensure they stay within the authorized scope.
- Do not use bare `pip`, `pip3`, or `python -m pip`. If a virtual environment must be created explicitly, use `uv venv`.
- Run Python scripts with `uv run --locked python <script>`. For isolated tooling unrelated to the project, prefer `uv run --no-project`, without adding project dependencies.

## Dependency Changes

- Obtain user approval before adding or replacing a third-party dependency, or changing its version requirement, dependency type, registry, or Git source. An explicit request naming the dependency and the change counts as approval.
- Keep approval scoped to the requested packages and changes. Do not include unrelated dependency or lockfile updates.
- Remove a dependency only when requested or when the authorized change makes it unused. Ask before removing it for any other reason.