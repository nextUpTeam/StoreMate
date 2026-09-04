# Architecture Overview

StoreMate is a modular monolith using Onion/Clean Architecture. The dependency direction points inward toward business rules:

```text
Domain <- Application <- Infrastructure <- API
                                      ^
                         Admin Portal and Mobile use API only
```

## Responsibilities

- **Domain**: entities, value objects, invariants, and domain rules. It has no framework or database dependencies.
- **Application**: commands, queries, use cases, DTOs, validation, and ports/interfaces. It depends on Domain.
- **Infrastructure**: EF Core, SQL Server mappings, repositories, storage, and external service adapters.
- **API**: HTTP endpoints, authentication, authorization, versioning, middleware, and composition-root configuration.
- **Admin Portal**: browser client for administration. It communicates through the API.
- **Mobile**: .NET MAUI client for shopkeepers, scanning, sales, and future offline work. It communicates through the API.

Keep the system as one deployable backend until scale or team ownership provides a concrete reason to split it.
