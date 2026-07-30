# uv Learnings

## [2026-07-30] Lesson: `uv sync --locked` skips optional-dependencies groups unless told to include them
**ID**: d5e6f7a8
**Category**: tool
**Context**: CI workflow running a Python test suite via uv
**Learning**: `uv sync --locked` only installs the base `[project.dependencies]` list. Packages declared under `[project.optional-dependencies]` (e.g. `dev = ["pytest>=8.0"]`) are NOT installed unless the sync command explicitly requests them with `--extra <name>` (or `--all-extras`), or via `--group <name>` for a `[dependency-groups]` table. A CI step that runs `uv sync --locked` followed by `uv run pytest` will fail with `error: Failed to spawn: pytest` if pytest lives only in an optional-dependencies extra.
**Evidence**: `codeatlas-cpp`'s "Graph IR suite (Python)" CI job failed on every run since the workflow was introduced (`7bc7ace`) because `.github/workflows/ci.yml` ran `uv sync --locked` without `--extra dev`, while `graph/pyproject.toml` declared `pytest` under `[project.optional-dependencies] dev`. Fixed by changing the CI step to `uv sync --locked --extra dev`.
**Confidence**: high
**Validations**: 1
**Projects**: codeatlas-cpp
