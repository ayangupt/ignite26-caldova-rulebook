# Policies

These are acceptance criteria for workshop baseline 1.0. Apply them to the
selected modernization work, using the contract definition in
[charter.md](charter.md) and the technology choices in [targets.md](targets.md).
Report what was actually checked; generated assertions are not validation.

## Execution Boundaries

| ID | Boundary |
|----|----------|
| LAB-01 | Do not create, change or delete cloud resources, identity assignments, grants, firewall/network rules or deployments in this application exercise |
| LAB-02 | Do not migrate, seed, rename or alter a shared database or its schema; consume the database team's supplied settings and data |
| LAB-03 | Do not install tools, upgrade the supplied toolchain or change machine-wide settings to make a task pass; ordinary dependency restores and normal caches from supplied tools and approved feeds are allowed |
| LAB-04 | Use only the assigned lab identity and approved resource scope; do not use personal credentials or an inherited account merely because it works |
| LAB-05 | Do not fabricate endpoints, resource names, credentials, test results or rulebook-loading evidence; do not edit the rulebook, weaken tests or rewrite prepared checkpoints to obtain a passing result |

If a prerequisite is missing, name it and record the affected check as **Not
verified**. Use the documented prepared-result fallback when instructed, clearly
labelled as prepared evidence. Missing prerequisites are not permission to
expand the task's scope. Optional infrastructure or deployment work belongs to
its separately authorized lab section.

Supplied local tests may create and clean up their own isolated disposable test
fixtures using already available tools. This does not authorize changes to the
shared lab database, installation of a container runtime or cloud provisioning.

## Security and Personal Data

| ID | Requirement | Evidence to inspect |
|----|-------------|---------------------|
| SEC-01 | Deployed Azure/database access uses Microsoft Entra ID with managed identity wherever supported; remove production credential-based connections | Identity/connection construction and configuration; separately report an authorized live access test |
| GRD-10 | Do not commit credentials, keys or connection secrets, including `machineKey`; use managed identity, then runtime Key Vault access where a secret remains necessary | Source, configuration and generated artifacts; no real secret values in reports or screenshots |
| SEC-02 | Database connections encrypt traffic and validate the server certificate; application defaults must not set `TrustServerCertificate=true` | Connection builders and configuration tests |
| SEC-03 | Customer names, SSNs, account/card/IBAN values and other personal data must not appear in logs, telemetry attributes or error details | Review logging/telemetry/error sites and test representative success and failure paths with synthetic data |

Prefer omitting personal fields from diagnostics. Where masking is required,
use a tested helper and non-sensitive correlation IDs. Do not redact legitimate
business data merely to avoid reviewing its logging. The PII masking skill is
implementation guidance, **not an enforcement mechanism or proof of compliance**.
Never write a real credential or token to diagnostic evidence.

Authorized local checks may use the assigned learner/developer Entra identity.
An isolated disposable test fixture may instead use synthetic data, credentials
generated in memory, and a self-signed SQL certificate. Keep these allowances
explicitly in test-only code/configuration, never in application defaults or
deployment configuration. Such tests do not establish production identity, TLS,
key-management or shared-database readiness.

## Prohibited Technologies and Patterns

| ID | Do not retain in the modernized output | Required direction/check |
|----|--------------------------------------|--------------------------|
| GRD-01 | Struts 1.x or 2.x | Use the Spring Boot target |
| GRD-02 | `System.Data.SqlClient` | Use `Microsoft.Data.SqlClient` |
| GRD-03 | Mono or a non-Microsoft **final runtime image** | Use the runtime target; official non-Microsoft build/toolchain stages are permitted |
| GRD-04 | Unresolved High/Critical dependency vulnerabilities | Use a compatible patched version and verify the resolved graph under VAL-03 |
| GRD-05 | Log4j 1.x or Log4j 2.x below 2.17.1 | Use the target framework's logging or a compatible patched release; a version floor alone does not satisfy VAL-03 |
| GRD-11 | SQL built by concatenating untrusted values | Use parameterized queries |
| GRD-12 | Production debug/detail settings or raw internal exception details returned to callers | Use generic error responses and sanitized structured diagnostics |
| GRD-13 | Silent exception swallowing or success-shaped responses to failed operations | Handle/report the failure explicitly and test the caller-visible result; do not introduce unrelated behavior changes |
| GRD-14 | Durable or cross-instance state relying on ephemeral per-instance memory/files | Configure an appropriately durable/shared store for sessions, persisted tokens, uploads, job state and Data Protection keys |

GRD-14 permits request-local data, temporary processing files and rebuildable
caches whose loss does not affect correctness. A protected shared persistent
volume can satisfy a storage requirement; it is not the same as ephemeral
container storage. Test-only ephemeral keys are permitted as fixtures, not as
production configuration. If the required store/key configuration is absent,
report it as an outstanding deployment dependency rather than provisioning it
or claiming restart/scale-out behavior was verified.

## Required Application Elements

| ID | Requirement | Evidence to inspect |
|----|-------------|---------------------|
| GRD-20 | Separate liveness and readiness endpoints; readiness reflects required dependencies and must not report success for a missing/unavailable database | Endpoint code and positive/negative route tests; liveness alone is not readiness |
| GRD-21 | Structured logging and OpenTelemetry tracing/metrics, with Azure Monitor export configured through environment settings | Instrumentation/exporter wiring; report missing exporter configuration explicitly and verify ingestion separately |
| GRD-22 | Environment-based configuration without baked-in environment-specific addresses or secrets | Configuration binding, non-functional placeholders and missing-setting behavior |
| GRD-23 | A production-target Dockerfile matching the target artifacts | Dockerfile review plus build/run evidence when the container prerequisites exist |
| GRD-24 | Container Apps readiness: configurable HTTP port, health routes, graceful shutdown, no host-specific runtime paths and the Linux container target | Source/configuration review and applicable local container checks, not a claim of Azure deployment |

## Validation and Quality Gates

For every applicable gate report **Satisfied**, **Not satisfied**, or **Not
verified**. Use **Not applicable** only with an explicit reason. An unavailable
tool, feed, endpoint or permission is **Not verified**, never a pass.
Prepared evidence must name its source/revision and must not be presented as a
test of the current run.

| ID | Gate |
|----|------|
| VAL-01 | Build the selected application and affected callers after each major task; investigate failures within the authorized scope |
| VAL-02 | Run applicable unit/smoke/contract tests; establish a baseline before changing behavior when tests are absent. Planner-added regression tests are required work, not optional. Report service-dependent tests not run and their missing prerequisites separately |
| VAL-03 | Audit resolved direct/transitive application and test dependencies with an available advisory-backed scanner; record date, source and scope. List pre-existing findings separately, but do not automatically waive them. A dependency listing is not a vulnerability scan; no scanner/feed means Not verified |
| VAL-04 | Review the Dockerfile against the targets. Build and run it using the supplied container runtime when available; otherwise report those checks as Not verified. Do not install a runtime or change machine settings |
| VAL-05 | Check the externally observable contracts defined in the charter, including cross-application sign-in and SOAP behavior where relevant; record any approved deviations and any unavailable integration checks |
| VAL-06 | Produce a compliance report mapping applicable rule IDs/requirements to files, tests or other evidence, with separate observed results, prepared evidence, exceptions and environment-dependent checks |
| VAL-07 | Test managed-identity database access, telemetry delivery, durable key/state configuration and deployment only when the separately authorized environment and prerequisites are supplied; local fixtures are not substitutes for these results |

A compliance report is a review aid, not proof by itself. A plan describes work
to do; it cannot establish that the implementation already satisfies a rule.
Classroom progress may use an explicit prepared fallback, but unverified gates
prevent claiming that the current run is fully compliant or deployment-ready.

## Exceptions

The facilitator or designated lab content owner, **not the agent**, may approve
a workshop exception. Participants do not independently change the common
baseline during the core exercise.

Record the rule, affected scope/files, reason, residual risk, approver and date
in the application's compliance report. An unapproved deviation is **Not
satisfied**. An approved exception stays visible as an exception, not evidence
that the original requirement passed. A missing prerequisite is **Not verified**,
not an automatically granted exception. Policy exceptions do not grant access
or authorize infrastructure changes.

## Coding Conventions

- .NET: enable nullable reference types, use asynchronous I/O where supported,
  and use `ILogger` for sanitized structured logging.
- Java: use constructor injection and SLF4J; avoid wildcard imports.
- Keep changes focused on the selected modernization goals and compatibility.
