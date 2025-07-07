# 🛠️ Neofile - Microservice Hexagonal Architecture with GoF Design Patterns

This document defines the **standard architecture** and **design patterns** to be used across all Neofile microservices, following a clean hexagonal structure and applying real GoF patterns where appropriate.

---

## 📁 Folder Structure (Per Microservice)

```
src/
├── application/              # Bean configuration & Services
│   ├── config/              # Bean wiring (Gateways, UseCases)
│   └── service/             # Application services (Spring @Service)
│
├── domain/                  # Core business logic
│   ├── model/              # Entities, Value Objects, Enums
│   ├── usecase/            # Use Cases (pure logic)
│   └── gateway/            # Interfaces to be implemented in infra
│
├── infrastructure/
│   ├── driven-adapter/     # External implementations (DB, GPT, S3)
│   │   ├── persistence/    # JPA/R2DBC repositories
│   │   └── client/         # REST clients, GPT adapters
│   │
│   └── entry-point/        # Input interfaces
│       ├── route/          # If WebFlux
│       ├── handler/        # If WebFlux
│       └── controller/     # If using standard Spring MVC
```

---

## 🧠 GoF Design Patterns Applied

| Layer / Context                    | GoF Pattern            | When to Use                                                                 |
|-----------------------------------|------------------------|------------------------------------------------------------------------------|
| `domain/model`                    | **Factory Method**     | Create documents, expedientes dynamically                                   |
|                                   | **Builder**            | For complex DTO/entity construction                                         |
|                                   | **Strategy**           | Different classification or validation strategies                           |
| `domain/gateway`                  | **Adapter**            | Interface to external systems (GPT, S3, etc.)                               |
| `application/service`            | **Facade**             | Simplify orchestration between use cases                                    |
| `driven-adapter/client`           | **Proxy**              | API access with caching/security controls                                   |
| `driven-adapter/persistence`      | **Repository (tactical)** | DB access, possibly enhanced with Decorator or Composite                 |
| `shared/mapper`                   | **Mapper (not GoF)**   | Convert DTOs ↔ Domain                                                       |
| Event-based communication         | **Observer (Pub-Sub)** | RabbitMQ or internal event publishing                                       |
| Spring Bean lifecycle             | **Singleton**          | Default for `@Service`, `@Component`, etc.                                  |

---

## 📌 Example: Classify Document Flow

```
[POST /documents]
    ↓
[DocumentHandler] → [DocumentService (application)]
    ↓
[ClassifyDocumentUseCase (domain)]
    ↓
[GPTClassifierGateway (interface)] → [GPTClientAdapter (infra)]
    ↓
[returns classification]
```

---

## ✅ Summary of Responsibilities

- **application.service** → Defines Spring `@Service` classes that inject `usecases` and coordinate operations.
- **domain.usecase** → Contains orchestration logic, independent of frameworks.
- **domain.gateway** → Interfaces to external systems (DB, GPT, S3, etc.).
- **driven-adapter** → Implements gateways for infrastructure tools.
- **entry-point** → Routes and handlers (WebFlux) or controllers (Spring MVC) handle incoming HTTP requests.

Apply this structure and these patterns consistently across all Neofile microservices: `users-service`, `documents-service`, `ai-service`, etc.