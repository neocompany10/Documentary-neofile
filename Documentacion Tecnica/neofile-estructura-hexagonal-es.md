# 🛠️ Neofile - Arquitectura Hexagonal por Microservicio con Patrones de Diseño GoF

Este documento define la **arquitectura estándar** y los **patrones de diseño** que se deben usar en todos los microservicios de Neofile, siguiendo una estructura hexagonal limpia y aplicando patrones GoF reales donde sea apropiado.

## Scaffolding of Clean Architecture: 

https://bancolombia.github.io/scaffold-clean-architecture/docs/intro


---

## 📁 Estructura de Carpetas (Por Microservicio)

```
src/
├── application/              # Configuración de beans y servicios
│   ├── config/              # Configuración de beans (Gateways, UseCases)
│   └── service/             # Servicios de aplicación (@Service de Spring)
│
├── domain/                  # Lógica de negocio central
│   ├── model/              # Entidades, Value Objects, Enums
│   ├── usecase/            # Casos de uso (lógica pura)
│   └── gateway/            # Interfaces a implementar en infraestructura
│
├── infrastructure/
│   ├── driven-adapter/     # Implementaciones externas (DB, GPT, S3)
│   │   ├── persistence/    # Repositorios JPA/R2DBC
│   │   └── client/         # Clientes REST, adaptadores GPT
│   │
│   └── entry-point/        # Interfaces de entrada
│       ├── route/          # Si se usa WebFlux
│       ├── handler/        # Si se usa WebFlux
│       └── controller/     # Si se usa Spring MVC clásico
```

---

## 🧠 Patrones de Diseño GoF Aplicados

| Capa / Contexto                   | Patrón GoF           | Cuándo aplicarlo                                                           |
|----------------------------------|----------------------|----------------------------------------------------------------------------|
| `domain/model`                   | **Factory Method**   | Crear documentos o expedientes dinámicamente                              |
|                                  | **Builder**          | Para construir DTOs o entidades complejas                                 |
|                                  | **Strategy**         | Para aplicar reglas distintas de clasificación o validación               |
| `domain/gateway`                 | **Adapter**          | Interfaz con sistemas externos (GPT, S3, etc.)                            |
| `application/service`           | **Facade**           | Orquestar múltiples casos de uso de forma simple                          |
| `driven-adapter/client`          | **Proxy**            | Acceso a APIs externas con control de caché o seguridad                   |
| `driven-adapter/persistence`     | **Repository (táctico)** | Acceso a BD, con Decorator o Composite si es necesario                |
| `shared/mapper`                  | **Mapper (no GoF)**  | Conversión entre DTOs y modelos de dominio                                |
| Comunicación basada en eventos   | **Observer (Pub-Sub)** | Publicación/consumo de eventos vía RabbitMQ                              |
| Ciclo de vida de Beans Spring    | **Singleton**        | Comportamiento por defecto en Spring (`@Service`, `@Component`)          |

---

## 📌 Ejemplo: Flujo de Clasificación de Documento

```
[POST /documents]
    ↓
[DocumentHandler] → [DocumentService (application)]
    ↓
[ClassifyDocumentUseCase (domain)]
    ↓
[GPTClassifierGateway (interface)] → [GPTClientAdapter (infra)]
    ↓
[retorna clasificación]
```

---

## ✅ Resumen de Responsabilidades

- **application.service** → Define beans de Spring (`@Service`) que inyectan casos de uso.
- **domain.usecase** → Contiene la lógica de orquestación desacoplada de frameworks.
- **domain.gateway** → Interfaces hacia infraestructura externa (GPT, S3, DB...).
- **driven-adapter** → Implementaciones concretas de gateways.
- **entry-point** → Manejo de rutas y controladores (WebFlux o MVC) para solicitudes HTTP.

Aplica esta estructura y estos patrones de forma coherente en todos los microservicios de Neofile: `users-service`, `documents-service`, `ai-service`, etc.