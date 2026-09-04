# Deployment

## Environments

| Environment | Purpose | Data and access |
|---|---|---|
| Development | Local feature work | Local SQL Server; no cloud required |
| QA | Automated and tester validation | Isolated test database and test secrets |
| Staging | Production-like release verification | Sanitized data, restricted access |
| Production | Client operations | Protected data, backups, monitoring, approvals |

## Target Production Architecture

```text
Admin Portal -> Azure App Service
StoreMate API -> Azure App Service -> Azure SQL
Secrets -> Azure Key Vault
Documents/invoices -> Azure Blob Storage
CI/CD -> GitHub Actions
Mobile -> Android/iOS app stores
```

Use HTTPS everywhere. Clients communicate with the API only; they never connect directly to SQL Server. Create separate resource groups/configuration for environments and keep credentials out of source control.

## CI/CD

`build.yml` restores and builds the solution. `test.yml` runs unit tests and the available integration tests. Pull requests must pass restore, build, and tests before merging.

Future environment workflows are `deploy-dev.yml`, `deploy-staging.yml`, and `deploy-production.yml`. They should use protected environments and approvals. Production must never deploy automatically from every commit; require an approved release from a protected branch/tag.

## Local Development

Local development uses the .NET SDK and local SQL Server, LocalDB, SQL Server Developer Edition, or a container. Azure resources are not required until an environment is intentionally provisioned.
