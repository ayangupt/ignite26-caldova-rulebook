# Charter

## Metadata

| Field | Value |
|-------|-------|
| Rulebook Name | Caldova Bank Modernization Workshop |
| Version | 1.0 |
| Date | 2026-10-29 |
| Status | Finalized workshop content baseline |
| Maintainers | Application-modernization lab authors |

Caldova Bank is a fictional scenario. This rulebook defines the common training
baseline, not an approved corporate policy or production-security certification.
All learners use the same baseline; rulebook authoring is not a prerequisite.

## Scope

This rulebook applies to the .NET Framework and Java applications selected by the
supplied application-modernization track and goals. It does not apply to the
separate Caldova SRE workload.

Rules describe the intended modernized output. Legacy inputs and deliberately
flawed review checkpoints are examples to inspect, not compliant final results.
Do not rewrite those checkpoints or weaken a rule to make an example pass.
Historical prepared results retain their original provenance; they are not
evidence that a learner's current run followed this baseline.

Database provisioning, migration, schema/seed changes, service-tier selection,
cloud deployment, identity administration and SRE operations are outside this
application exercise. Application code consumes the endpoint, database name and
access supplied by the responsible section. See the execution boundaries in
[policies.md](policies.md).
Record missing platform inputs as handoff dependencies, not executable
provisioning, database-migration or deployment tasks in the application plan.

## Modernization Strategy

This exercise uses **Refactor** and **Replatform**. Rehost-only work is out of
scope: leaving an application unchanged does not satisfy this rulebook.

| Application | Default strategy |
|-------------|------------------|
| .NET Framework web application or service | Refactor to .NET 10 and the supplied container target |
| Java application using Struts | Refactor to the Spring Boot target |
| Java servlet application or service | Replatform to the Java target; refactor where framework compatibility or a security fix requires it |

Both strategies must satisfy the same applicable policies. Replatform means
limiting unnecessary architectural change, not skipping required code changes.
Targets are defined in [targets.md](targets.md). Departures from this baseline
need a recorded exception under the policy's exception procedure.

## Principles

1. Modernize providers before their callers, following the supplied dependency
   map. Consider the selected applications as a connected business flow.
2. Preserve externally observable contracts: HTTP/SOAP routes and aliases, verbs,
   query parameters, request/response shapes, status codes, SOAP operations and
   data-contract namespaces, and the agreed cookie/token and sign-in contracts.
   Framework and internal type changes required by the targets are expected;
   they are not, by themselves, external contract changes. Adding REST routes
   does not justify removing a required SOAP endpoint.
3. Preserve legitimate business behavior. Security hardening may reject unsafe
   input or remove information leaks; document those changes and test the
   supported flows. Do not mask failures or invent successful saves/sign-ins.
4. Establish the relevant baseline before changing behavior. Build and run the
   applicable tests after each major task. Record unavailable checks honestly.
5. Apply the same security and reliability intent to .NET and Java using each
   ecosystem's appropriate libraries. Do not make unrelated changes outside the
   selected application or track.
6. A plan or compliance report is a claim to review, not proof. Distinguish
   observed results, prepared evidence and checks that still need an environment.
