# Onion Architecture Rules

## Allowed Dependencies

| Project | May reference |
|---|---|
| Domain | .NET base libraries only |
| Application | Domain |
| Infrastructure | Application, Domain, provider libraries |
| API | Application, Infrastructure, ASP.NET Core |
| Admin Portal | Contracts/API client code, not Infrastructure |
| Mobile | Contracts/API client code, not Infrastructure |

Dependencies must not point from Domain toward outer layers. Use interfaces in Application when an outer-layer capability is required. Register implementations in API/Infrastructure composition roots.

## Working Rules

- Keep business decisions in Domain or Application, not controllers.
- Keep EF Core types and SQL concerns in Infrastructure.
- Keep transport models separate from domain entities.
- Use dependency inversion for persistence, clock, identity, file storage, and device services.
- Keep cross-cutting policies centralized and testable.
