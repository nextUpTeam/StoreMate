# StoreMate Web Deployment Recommendation

## Decision

Deploy StoreMate on **Microsoft Azure** for the first client release:

```text
Admin browser (Blazor WebAssembly) -- HTTPS --> Azure Static Web Apps
Android app ------------------------- HTTPS --> Azure App Service (API)
                                                   |
                                                   v
                                             Azure SQL Database
```

This is the best fit for StoreMate because the system already uses .NET, ASP.NET Core, EF Core, and SQL Server. It keeps operations simple for a first solo project while leaving room to scale.

## Recommended Services

| Concern | Service | Initial approach |
|---|---|---|
| API | Azure App Service | Linux B1 for pilot; scale when metrics require it |
| Database | Azure SQL Database | Basic/S0 or equivalent small tier; confirm regional pricing |
| Admin portal | Azure Static Web Apps | Free tier for pilot if Blazor WebAssembly is used |
| Files | Azure Blob Storage | Add only for invoices, images, or documents |
| Secrets | App Service settings initially; Key Vault for production secrets | Never commit credentials |
| Monitoring | Application Insights | Enable request, failure, and availability monitoring |
| Deployment | GitHub Actions | Build, test, and deploy from protected branches |

Use the latest supported .NET LTS for a new implementation. The repository currently targets .NET 8, so migrate and test the solution before changing the production runtime target.

## Indicative Cost

Prices vary by Azure region, currency, tax, storage, traffic, and usage. Confirm the final amount in the Azure Pricing Calculator before presenting a quotation.

| Stage | Typical monthly budget |
|---|---:|
| Development | $0 cloud cost; run locally |
| Small pilot | $30-$55 (approximately INR 2,500-5,000) |
| Small production setup | $90-$160+ (approximately INR 8,000-15,000+) |

The pilot estimate assumes one API, one small SQL database, a low-volume admin site, limited monitoring, and no always-on staging environment. The client should own the Azure subscription and billing account.

## Delivery Phases

### Development

- Run the API and SQL Server locally.
- Build and test the admin portal and Android application.
- Keep all secrets in local user secrets or environment variables.

### Pilot

- Create one Azure resource group.
- Deploy the API to App Service with HTTPS.
- Deploy the Blazor WebAssembly portal to Static Web Apps.
- Create the Azure SQL database and apply reviewed EF Core migrations.
- Configure a custom API domain, CORS, authentication, backups, monitoring, and budget alerts.

### Production

- Use separate staging and production resources.
- Scale App Service and Azure SQL based on measured usage.
- Store production secrets in Key Vault or managed application settings.
- Restrict deployment to protected GitHub branches and review database migrations.
- Test backup restoration and document support ownership.

## Engineering Boundaries

- Mobile and admin clients communicate only with the API.
- Clients never connect directly to Azure SQL.
- Inventory changes and sale creation are validated and committed transactionally by the API.
- Start as a modular monolith. Do not add Kubernetes, microservices, Redis, Service Bus, or multi-region infrastructure without a measured requirement.
- Add API versioning and health checks before the first client release.

## Barcode and Hardware Readiness

Store barcode data in the domain/application model and expose operations through the API. The client flow is:

```text
Camera or hardware scanner -> barcode string -> API product lookup -> sale/inventory operation
```

Keep device-specific code behind interfaces such as `IBarcodeScanner` and `IReceiptPrinter`. This allows camera scanning now and Bluetooth, USB, dedicated scanners, or printers later without changing sale and inventory rules.

## Client Pitch

Azure gives StoreMate a managed, professional deployment with HTTPS, backups, monitoring, automated releases, and a clear upgrade path. The first release can stay within a small monthly budget, while the same architecture can support additional stores, users, scanners, printers, and reporting as the client grows.

## Required Client Decisions

- Confirm the Azure region and subscription owner.
- Confirm whether the admin portal is Blazor WebAssembly or Blazor Web App.
- Confirm expected stores, users, traffic, and data retention.
- Confirm backup, uptime, support, and recovery expectations.
- Confirm whether the client requires offline mobile operation.
