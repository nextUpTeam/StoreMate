# StoreMate

StoreMate is a multi-store enterprise point-of-sale, inventory, sales, and stock management platform. It is designed as a scalable, production-ready modular monolith using Onion/Clean Architecture, so future capabilities can be added without a major architectural rewrite.

## Architecture Overview

StoreMate follows Onion/Clean Architecture with a strict dependency direction:

```text
Domain (core models and rules)
		<- Application (use cases)
		<- Infrastructure (EF Core, Azure integrations)
		<- API (ASP.NET Core)
```

### Layers

- **Domain**: Entities, value objects, domain events, domain services, and invariants.
- **Application**: Use cases, commands and queries, DTOs, interfaces, validation policies, and application services.
- **Infrastructure**: EF Core `DbContext`, repository implementations, persistence mappings, and implementations for external services such as Azure Key Vault and Blob Storage.
- **API**: ASP.NET Core controllers, DTO mapping, API versioning, middleware, authentication, and the public API surface.

Admin Portal and Mobile are independent frontend clients. They communicate exclusively with `StoreMate.API` and must not reference Infrastructure.

### Dependency Rules

- Domain has zero references to Infrastructure, EF Core, ASP.NET Core, Blazor, MAUI, HTTP, or database providers.
- Application may reference Domain.
- Infrastructure references Application and Domain.
- API references Application and Infrastructure.
- There must be no circular project dependencies.
- StoreMate is a modular monolith. Microservices and Kubernetes are intentionally out of scope.

## Tech Stack

### Backend

- .NET 8 and C#
- ASP.NET Core Web API
- Entity Framework Core 8
- Microsoft SQL Server
- JWT Bearer authentication and ASP.NET Core authorization
- FluentValidation
- Swagger/OpenAPI
- `Microsoft.Extensions.Logging` or Serilog for structured logging
- Built-in dependency injection
- REST APIs with route-based API versioning

### Frontend

- **Admin Portal**: Blazor (WASM or Server; team decision pending)
- **Shopkeeper Mobile App**: .NET MAUI (future offline synchronization)

Frontend clients communicate only with `StoreMate.API`.

### Infrastructure and Deployment

- Azure App Service
- Azure SQL
- Azure Key Vault
- Azure Blob Storage
- GitHub Actions for CI/CD

Local development does not require Azure resources. Use LocalDB, SQL Server Developer Edition, or a containerized SQL Server instance.

## Project Layout

```text
src/
	StoreMate.Domain/                 # Domain models and business rules
	StoreMate.Application/            # Use cases, DTOs, and interfaces
	StoreMate.Infrastructure/         # EF Core, persistence, and integrations
	StoreMate.API/                    # ASP.NET Core Web API
	StoreMate.AdminPortal/            # Independent Blazor client
	StoreMate.Mobile/                 # Independent .NET MAUI client
tests/
	StoreMate.Application.Tests/
	StoreMate.Domain.Tests/
	StoreMate.API.IntegrationTests/
docs/
.github/
	workflows/
```

## Getting Started (Local)

### Prerequisites

- .NET 8 SDK
- SQL Server: LocalDB, Developer Edition, or a containerized instance
- Git

### Quick Start

Clone the repository and enter the project directory:

```bash
git clone https://github.com/nextUpTeam/StoreMate.git
cd StoreMate
```

Restore dependencies and build the solution:

```bash
dotnet restore
dotnet build
```

Configure the local database in `src/StoreMate.API/appsettings.Development.json` or through environment variables. A LocalDB connection string example is:

```json
{
	"ConnectionStrings": {
		"DefaultConnection": "Server=(localdb)\\mssqllocaldb;Database=StoreMate.Dev;Trusted_Connection=True;MultipleActiveResultSets=true"
	}
}
```

Apply EF Core migrations:

```bash
dotnet ef database update \
	--project src/StoreMate.Infrastructure \
	--startup-project src/StoreMate.API
```

Run the API:

```bash
dotnet run --project src/StoreMate.API
```

## Database and Migrations

- Keep EF Core migrations in source control under `StoreMate.Infrastructure`.
- Use code-first EF Core mappings in Infrastructure.
- Map Domain models to persistence models within Infrastructure.
- Do not leak EF Core types into Domain.

## Running in Azure

Production deployment targets are Azure App Service and Azure SQL. Use Azure Key Vault for secrets in production, replacing local secrets and appsettings values with Key Vault references where appropriate.

GitHub Actions will provide the CI/CD build, test, and deployment stages. Sprint 0 includes the workflow skeleton.

## API Documentation and Authentication

- Swagger/OpenAPI is enabled in Development. Secure Swagger before enabling it in staging or production.
- Authentication uses JWT Bearer tokens.
- Sprint 1 includes the `Admin` and `Clerk` roles in seed data.
- Prefer route-based API versioning, such as `/api/v1/`.
- Logging uses `Microsoft.Extensions.Logging` or Serilog for structured application diagnostics.

## Sprint Scope

### Sprint 0: Setup

- Solution and project scaffolding following Onion Architecture
- CI skeleton in `.github/workflows` that builds and runs tests
- Basic README, CONTRIBUTING, MIT License, and `.gitignore`

### Sprint 1: MVP

- Domain models for `Store`, `Product`, `StockLocation`, `InventoryItem`, `Sale`, and a simple `Invoice`
- JWT authentication and authorization with `Admin` and `Clerk` roles
- CRUD endpoints for products and stores
- Sale creation that decrements inventory atomically
- FluentValidation for input validation
- Application-layer unit tests for core use cases
- Swagger and structured logging

### Out of Scope for Sprint 1

The following are designed for future releases but are not implemented in Sprint 1:

- Mobile offline synchronization
- Background synchronization
- Thermal printing
- Advanced reporting
- Automated replenishment
- Push notifications

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md) for the contribution workflow, branch rules, commit message conventions, and migration guidance.

## License

StoreMate is released under the MIT License. See [LICENSE](LICENSE) for the full text.

## Maintainers

nextUpTeam
