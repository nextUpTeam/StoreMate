# Contributing to StoreMate

Thank you for contributing to StoreMate. This project is a modular monolith built with Onion/Clean Architecture, so changes should preserve clear dependency boundaries and keep the platform ready for future clients and capabilities.

## Development Workflow

1. Create a focused branch from the current default branch.
2. Make the smallest change that fully addresses the issue or feature.
3. Add or update tests for behavior that changed.
4. Run restore, build, and tests locally.
5. Open a pull request with a clear summary, test results, and migration notes when applicable.
6. Address review feedback before merging.

## Branches

Use descriptive branch names with one of these prefixes:

- `feature/` for new functionality
- `fix/` for bug fixes
- `docs/` for documentation-only changes
- `refactor/` for behavior-preserving code changes
- `test/` for test-only changes
- `chore/` for maintenance and tooling

Do not commit directly to the default branch. Keep branches focused and up to date before requesting review.

## Commit Messages

Use imperative, concise commit subjects. Follow this format:

```text
<type>: <short description>
```

Recommended types include `feat`, `fix`, `docs`, `refactor`, `test`, and `chore`.

Examples:

```text
feat: add product search endpoint
fix: prevent overselling inventory
```

Keep unrelated changes in separate commits. Do not include secrets, local database files, generated build output, or IDE metadata in commits.

## Architecture Rules

- Keep Domain independent of EF Core, ASP.NET Core, Infrastructure, HTTP, UI frameworks, and database providers.
- Application may depend on Domain only.
- Infrastructure may depend on Application and Domain.
- API may depend on Application and Infrastructure.
- Frontend clients must communicate through `StoreMate.API` and must not reference Infrastructure.
- Do not introduce microservices or Kubernetes for Sprint 0 or Sprint 1 work.

## Validation Before a Pull Request

Run these commands from the repository root:

```bash
dotnet restore
dotnet build
dotnet test
```

If a command cannot be run locally, explain why in the pull request. Include relevant API, unit, or integration test coverage for the change.

## Database and Migrations

- Keep EF Core migrations under `src/StoreMate.Infrastructure` and commit them with the related schema change.
- Review generated migrations before committing them.
- Use a local SQL Server or LocalDB instance for development.
- Never commit connection strings containing credentials or other secrets.
- Document any required migration or seed-data steps in the pull request.

## Pull Requests

A pull request should include:

- What changed and why
- Tests run and their results
- Any API contract or configuration changes
- Database migration and deployment considerations
- Known limitations or follow-up work

Keep pull requests reviewable and update documentation when setup, behavior, or public API contracts change.
