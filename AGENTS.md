# AGENTS.md — homescholling

## Project Overview

| Property | Value |
|----------|-------|
| Spring Boot | 4.1.1 |
| Java | 25 |
| Packaging | `war` |
| Dependencies | Spring Web, SpringDoc OpenAPI |
| Build | Maven Wrapper (`mvnw`/`mvnw.cmd`) |

**No JPA, no database, no hexagonal modules yet** — minimal starter project.

---

## Common Commands

Run from project root (`C:\my-projects\homescholling`):

```bash
# Build & test
./mvnw clean verify          # Full build + tests
./mvnw test                  # Unit tests only
./mvnw clean package -DskipTests  # Package without tests

# Run application
./mvnw spring-boot:run       # Dev mode (devtools enabled)

# Code generation (when MapStruct/Lombok added)
./mvnw compile               # Generates sources in target/
```

**Wrapper scripts:** Use `./mvnw` (Linux/macOS) or `mvnw.cmd` (Windows).

---

## Architecture (Target)

This project **will follow Hexagonal/Ports & Adapters** by functional module under `src/main/java/br/com/homescholling/usecase/`:

```
usecase/
├── pessoa/           # NEW - to be created
│   ├── adapter/
│   │   ├── driven/           # Output: DB, external APIs (future)
│   │   └── external/         # Input: controllers, DTOs (future)
│   │       ├── controller/
│   │       └── dto/
│   ├── converter/            # DTO ↔ Model ↔ Entity (future)
│   ├── entity/               # JPA entities (future - when JPA added)
│   ├── enumeration/          # Module-specific enums
│   ├── model/                # Domain models (pure Java)
│   ├── port/                 # Interfaces (input/output ports)
│   └── service/              # Use cases / application services
```

**Module isolation:** Avoid direct cross-module dependencies; use ports.

---

## Current State (as of 2026-10-03)

- Only base Spring Boot app exists (`Application.java`, `ServletInitializer.java`)
- No `usecase/` directory yet
- No JPA dependency — entities will be plain Java records/classes until JPA added
- No tests beyond generated `ApplicationTests.java`

---

## Creating New Module (e.g., `pessoa`)

1. Create directory structure under `src/main/java/br/com/homescholling/usecase/pessoa/`
2. Start with `model/` (domain model) — pure Java, no framework annotations
3. Add `entity/` later when JPA dependency is added
4. Add `port/`, `service/`, `adapter/`, `converter/` as needed
5. Tests mirror structure under `src/test/.../usecase/pessoa/`

---

## Key Conventions

1. **Java 25** — use latest language features (records, pattern matching, etc.)
2. **Model ≠ Entity ≠ DTO** — separate representations; convert explicitly
3. **Controllers** in `adapter/external/controller/` — thin, no business logic
4. **Services** in `service/` — use cases, orchestration, business rules
5. **Ports** in `port/` — interfaces, technology-agnostic names (e.g., `PessoaRepository`, not `JpaPessoaRepository`)
6. **Driven Adapters** in `adapter/driven/` — concrete implementations
7. **Converters** in `converter/` — MapStruct (when added) or manual
8. **No parallel architectures** — don't create `domain/application/infrastructure` alongside `usecase/`

---

## IDE Setup

- Import as Maven project
- Enable Annotation Processing (for future MapStruct/Lombok)
- Java SDK: 25