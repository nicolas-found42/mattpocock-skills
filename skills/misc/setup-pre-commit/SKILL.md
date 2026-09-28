---
name: setup-pre-commit
description: Set up pre-commit hooks (formatting, linting, type checking, tests) in the current repo, in any language. Use when user wants to add pre-commit hooks, set up Husky, lint-staged, lefthook, or the pre-commit framework, or add commit-time checks.
---

# Setup Pre-Commit Hooks

A pre-commit hook runs **fast, staged-only** checks first (format, lint the changed files), then **whole-repo** checks (type check, tests). Every check calls the repo's own commands, so the hook stays in step with how the project already builds.

## Steps

### 1. Survey the repo

Record, from the environment rather than guesswork:

- **Languages and toolchains**: the manifests present (`package.json`, `pyproject.toml`, `go.mod`, `Cargo.toml`, `Gemfile`, `pom.xml`/`build.gradle`, `*.csproj`, `mix.exs`, `composer.json`, ...), including ones in subdirectories of a monorepo.
- **Existing commands**: scripts in `package.json`, a `Makefile`, `justfile`, `Taskfile.yml`, tox/nox config, CI workflow steps. CI is the best record of which checks the project already trusts.
- **Existing tooling configs**: formatter and linter configs already committed (`.prettierrc`, `ruff.toml`, `rustfmt.toml`, `.golangci.yml`, `.rubocop.yml`, ...).
- **Existing hook manager**: `.husky/`, `.pre-commit-config.yaml`, `lefthook.yml`, `core.hooksPath` in git config, or hand-written scripts in `.git/hooks/`.

Done when every language in the repo has a formatter, a lint/type check, and a test command identified, or is marked as having none.

### 2. Choose the hook manager

- **A manager already exists**: extend it. One repo, one manager.
- **Pure JS/TS repo** (a root `package.json` and no other language): Husky + lint-staged. Follow [JS-TS.md](./JS-TS.md) for steps 3 and 4, then continue at step 5.
- **Anything else** (other languages, or polyglot repos including ones with JS): the [pre-commit framework](https://pre-commit.com). Continue at step 3.

### 3. Install pre-commit

Install it through the repo's toolchain where one fits (`uv add --dev pre-commit`, `poetry add --group dev pre-commit`), otherwise `pipx install pre-commit` or `brew install pre-commit`. Then:

```bash
pre-commit install
```

### 4. Write `.pre-commit-config.yaml`

Use `repo: local` hooks with `language: system` so each hook runs the project's own installed tool at the project's own version. Per language, fill the three slots from what step 1 found; the defaults below apply only where the repo has nothing:

| Language | Format (staged files) | Lint / type check | Tests |
| --- | --- | --- | --- |
| Python | `ruff format` | `ruff check --fix`, then `mypy` or `pyright` if configured | `pytest` |
| Go | `gofmt -w` | `go vet ./...`, `golangci-lint run` if configured | `go test ./...` |
| Rust | `rustfmt` | `cargo clippy -- -D warnings` | `cargo test` |
| Ruby | `rubocop -a` | `rubocop` | `bundle exec rspec` or `rake test` |
| Java/Kotlin | Spotless via Gradle/Maven if configured | build tool's check task | build tool's test task |
| C# | `dotnet format --include` | `dotnet build` | `dotnet test` |
| Shell | `shfmt -w` | `shellcheck` | |
| JS/TS inside a polyglot repo | `prettier --write --ignore-unknown` | the `typecheck`/`lint` script | the `test` script |

Shape of the config:

```yaml
repos:
  - repo: local
    hooks:
      # Staged-only: pre-commit passes the staged filenames.
      - id: ruff-format
        name: ruff format
        entry: ruff format
        language: system
        types: [python]
      # Whole-repo: runs once, ignores filenames.
      - id: pytest
        name: pytest
        entry: pytest -q
        language: system
        pass_filenames: false
        always_run: true
```

Order hooks format, then lint/type check, then tests. Prefix `entry` with the runner the repo uses (`uv run`, `poetry run`, `bundle exec`, `npx`). In a monorepo, scope each hook with `files: ^path/to/package/` and run its command from there (`entry: bash -c 'cd path/to/package && go test ./...'`).

Formatters a repo has never run will rewrite untouched files the first time they are staged. If the repo has no formatter config, create one with the tool's defaults and tell the user.

### 5. Verify

Run the hooks against the whole repo once (`pre-commit run --all-files`, or `npx lint-staged` plus the Husky hook's commands on the JS path). Done when every hook passes. A hook that fails on code you did not touch is a finding: report it to the user rather than weakening or removing the hook.

### 6. Commit

Stage the new and changed files and commit with `Add pre-commit hooks (<manager>)`. The commit runs the new hook, which is the smoke test.

Tell the user what each hook runs, and which languages have no check and why.
