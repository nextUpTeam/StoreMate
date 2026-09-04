# API Versioning

Expose public endpoints under a route-based version such as `/api/v1/`. Contracts are owned by the API and Contracts projects; clients must not depend on Infrastructure or database models.

- Make breaking changes in a new version.
- Prefer additive, backward-compatible changes within a version.
- Keep response and error shapes consistent.
- Validate input at the API/application boundary.
- Use authentication and authorization policies on protected endpoints.
- Document endpoints with Swagger/OpenAPI in Development and secured non-production environments.
- Include correlation IDs and structured logs for diagnosable failures.

Mobile and admin clients should declare the API version they support and handle an unsupported version explicitly.
