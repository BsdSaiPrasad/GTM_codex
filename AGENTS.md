# Repository Guidelines

## Project Structure & Module Organization

This repository is currently an empty project scaffold. As code is added, keep the root focused on project metadata and documentation. Use `src/` for application code, `tests/` for automated tests, `assets/` for static resources, and `docs/` for design or operational notes. Mirror source paths in tests where practical; for example, `src/pipeline/loader.py` should be covered by `tests/pipeline/test_loader.py`.

Avoid committing generated output, local environments, editor settings, credentials, or large datasets. Add these paths to `.gitignore` when the relevant tooling is introduced.

## Build, Test, and Development Commands

No build system or runtime has been configured yet. When adding one, provide a small, documented command surface—preferably through a `Makefile` or package scripts. Recommended targets are:

- `make setup` — install development dependencies.
- `make test` — run the complete automated test suite.
- `make lint` — run formatting and static checks.
- `make run` — start the project locally.

Keep this section synchronized with the actual commands; contributors should not need to inspect CI configuration to discover basic workflows.

## Coding Style & Naming Conventions

Use the formatter and linter standard for the chosen language, and commit their configuration with the first implementation. Default to spaces, UTF-8, LF line endings, and a final newline. Use descriptive names: `snake_case` for Python modules and functions, `kebab-case` for documentation filenames, and language-standard conventions elsewhere. Favor small modules with explicit responsibilities over broad utility files.

## Testing Guidelines

Add tests with every behavior change and bug fix. Place unit tests under `tests/`, name them after the behavior or module under test, and keep fixtures minimal. Tests must be deterministic and must not depend on live credentials, production services, or execution order. Document any coverage threshold once a test framework is selected.

## Commit & Pull Request Guidelines

There is no repository-local commit history yet. Use concise, imperative commit subjects such as `Add campaign ingestion pipeline`; optional prefixes like `feat:`, `fix:`, and `docs:` are encouraged when applied consistently. Keep commits focused.

Pull requests should explain the problem, summarize the solution, list verification performed, and link relevant issues. Include screenshots or sample output for user-visible changes, and call out configuration changes, migrations, or follow-up work explicitly.
