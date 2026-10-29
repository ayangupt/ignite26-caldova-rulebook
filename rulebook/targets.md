# Targets

These are the fixed framework lines and deployment choices for workshop baseline
1.0, not instructions to install tools or deploy resources. Follow the scope in
[charter.md](charter.md) and validation gates in [policies.md](policies.md).

## Framework and Dependency Baseline

| Component | Workshop target |
|-----------|-----------------|
| .NET | .NET 10 LTS, `net10.0`, SDK-style projects and `PackageReference` |
| Java runtime | OpenJDK 21 LTS |
| Java web framework | Spring Boot 3.5.x, no earlier than 3.5.14 |
| SQL Server JDBC driver | A pinned JDK-21-compatible release, no earlier than `12.8.2.jre11`; the `jre11` classifier is compatible with JDK 21 |
| Build tools | The supplied .NET SDK and Gradle installation or repository wrapper; do not install or upgrade machine tooling |

Keep the supplied application's exact dependency pins unless the modernization
or a demonstrated security/compatibility issue requires a change. The prepared
Ledger uses Spring Boot `3.5.14` and JDBC `12.8.2.jre11`; PayGateway uses `3.5.16`
and `13.6.1.jre11`. These identify rehearsed inputs, not blanket approval of their
current vulnerability status.

For a new Spring Boot/JDBC adoption without an existing target pin, use `3.5.16`
and `13.6.1.jre11` respectively as the starting baseline. Pin resolved versions
in build files and retain lock files where the supplied project supports them.
Do not use floating, preview or snapshot versions, or pursue unrelated upgrades
to whatever is latest. A framework-major change such as .NET 11, Java 25 or
Spring Boot 4 requires a facilitator-approved baseline exception.

Every selected version still needs the applicable advisory checks. If a target
cannot satisfy the security gates, report the conflict for a common baseline
update; do not quietly change the framework target or waive the finding. Lab
authors recheck version support and advisories when preparing the event release.

## Compute and Data

| Service | Use |
|---------|-----|
| Azure Container Apps | Container target for the selected modernized web applications and services |
| Azure SQL Database | Application connection target where the supplied source uses SQL Server |

The database section owns migration, schema/data preparation, sizing and service
tier. Application plans only consume the supplied database and access. Read
endpoints and names from environment settings; do not invent, discover outside
the assigned scope, or provision a replacement target.

Deployed application access uses Microsoft Entra ID with managed identity.
Assigned learner/developer Entra credentials may be used for authorized local
checks; that is not proof of deployed managed-identity access. Disposable local
test fixtures have the limited allowances defined in the security policies.

## Library and Framework Mappings

| Source | Target |
|--------|--------|
| `System.Data.SqlClient` | `Microsoft.Data.SqlClient` |
| `packages.config` | `PackageReference` |
| Classic .NET project files | SDK-style projects |
| Web Forms and legacy HTTP/SOAP hosting | ASP.NET Core, with MVC/Razor and CoreWCF where needed to preserve the supplied public contracts |
| Environment-specific `web.config` values | Environment settings and non-secret `appsettings.json` defaults |
| Struts | Spring Boot 3.5.x |
| `javax.servlet` APIs | Compatible `jakarta.servlet` APIs |
| Legacy SQL Server JDBC driver | The pinned JDBC target above |
| Stored Azure/database credentials | `Azure.Identity` / `DefaultAzureCredential` for .NET, or the corresponding Java identity support |

## Container Artifacts

Provide one production-target Dockerfile per application, using that application
as its build context. It must support the supplied Linux/amd64 target, use
separate build/runtime stages, run the final application as a non-root user and
expose a configurable HTTP port.

The **final runtime stage** uses a Microsoft-published .NET or OpenJDK image
matching the framework target. This is a workshop target choice, not a claim that
other publishers' images are inherently unsafe. Build stages may use official
language SDK or toolchain images, including Gradle. Avoid `latest` tags.

For .NET, the baseline image tags are `mcr.microsoft.com/dotnet/sdk:10.0` and
`mcr.microsoft.com/dotnet/aspnet:10.0`. For Java, the baseline runtime image is
`mcr.microsoft.com/openjdk/jdk:21-ubuntu`, as used by the prepared Ledger.
Record the image versions/digests used in validation.

Include configuration hooks and deployment-readiness artifacts, not automatic
deployment. A Dockerfile review, a successful image build and an actual Azure
deployment are three different results and must be reported separately.
