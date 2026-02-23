# HRS - Spec-Driven Development Workshop

This is the companion application for the [**Spec-Driven Development (SDD) Workshop**](https://martinelli.ch/sdd).<br>
It serves as a hands-on project where participants learn to build applications by driving implementation from
specifications.

## Tech Stack

- **Java 25** with **Spring Boot 4.0**
- **Vaadin 25** for the UI (Java-based views)
- **jOOQ** for type-safe SQL and data access
- **Flyway** for database migrations
- **PostgreSQL 18** as the database
- **Testcontainers** for database provisioning (dev and test)
- **Karibu Testing** for server-side UI unit tests
- **Playwright** for browser-based integration tests

## Prerequisites

- **Java 25** (or later)
- **Docker** (required for Testcontainers to start a PostgreSQL container)
- **Maven** (or use the included `mvnw` wrapper)

## Running the Application

The application uses Testcontainers to automatically start a PostgreSQL database — no manual database setup required.
Docker must be running.

**Run `TestHrsApplication` (not `HrsApplication`):**

```bash
./mvnw spring-boot:test-run
```

Or run `TestHrsApplication.main()` directly from your IDE.

This uses the `TestcontainersConfiguration` class which starts a PostgreSQL container and wires it as the datasource.
Running the regular `HrsApplication` will fail unless you provide your own PostgreSQL instance.

## Build and Code Generation

The Maven build uses a three-step pipeline during `generate-sources`:

1. **Groovy script** starts a PostgreSQL Testcontainer and captures its JDBC URL
2. **Flyway** runs database migrations against the container
3. **jOOQ** generates type-safe Java code from the live database schema

```bash
./mvnw compile
```

## Running Tests

Unit tests (Karibu) are run by **Surefire** with `mvnw test`. Integration tests (Playwright) use the `*IT` suffix
and are run by the **Failsafe** plugin, which is bound to the `integration-test` and `verify` phases.

**Unit tests only:**

```bash
./mvnw test
```

**All tests (unit + integration):**

```bash
./mvnw verify
```

The `verify` phase ensures that the Failsafe plugin picks up all `*IT` classes (e.g. `PlaywrightIT` subclasses)
and reports their results correctly. Always use `verify` instead of `integration-test` directly, so that test
failures are properly detected.
