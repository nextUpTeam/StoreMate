# Multi-Store Security

Every authenticated request must be evaluated in the context of the user's role and permitted store scope.

- **Admin**: access is controlled by explicit tenant/store permissions.
- **Clerk**: access is limited to assigned stores and operational actions.
- Store scope must come from trusted server-side identity and authorization data, never from an untrusted client-only value.
- Every store-owned query and command must apply the store filter before reading or changing data.
- A user must not infer access by changing a store ID in a URL or request body.
- Audit sensitive actions such as stock adjustments, sales voids, and permission changes.
- Return generic authorization failures and avoid leaking data from another store.

Authorization policies belong at the API boundary, while the application layer must still enforce business and store-scope rules so non-HTTP callers cannot bypass them.
