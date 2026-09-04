# Database Schema Guidance

Use SQL Server through EF Core in Infrastructure. Domain models must not contain EF Core attributes or provider-specific types.

Core planned aggregates include `Store`, `Product`, `StockLocation`, `InventoryItem`, `Sale`, and `Invoice`. Store-owned records must carry a store relationship and have indexes supporting store-scoped queries.

## Rules

- Use primary keys, foreign keys, unique constraints, and non-nullable columns to enforce invariants.
- Normalize product barcodes and enforce uniqueness according to the business scope.
- Use UTC timestamps and a consistent audit strategy.
- Protect concurrent inventory updates with a transaction and an appropriate concurrency token.
- Configure mappings explicitly in Infrastructure.
- Add and review EF Core migrations; never edit production schema manually without recording the change.
- Avoid storing secrets in the database or repository.
