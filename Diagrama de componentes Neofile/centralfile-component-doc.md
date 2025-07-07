# 🧱 Documentación Técnica - Diagrama de Componentes: `centralfile-service`

---

## 📑 Índice

1. Introducción
2. Enfoque arquitectónico
3. Diagrama de componentes
4. Justificación técnica de cada módulo
5. Caso práctico de uso
6. Conclusión
7. Pregunta para discusión
8. Referencia (APA 7.ª edición)

---

## 📘 1. Introducción

Este documento describe la arquitectura interna del microservicio `centralfile-service` del sistema Neofile. La estructura sigue un enfoque basado en arquitectura hexagonal (puertos y adaptadores), promoviendo independencia del dominio frente a la infraestructura, tambien utilizando el enfoque DDD (Domain-Driven Design).

---

## 🧠 2. Enfoque arquitectónico

Se aplica la arquitectura hexagonal (Ports & Adapters), donde:

- El dominio define el comportamiento (casos de uso, entidades, puertos).
- La infraestructura implementa adaptadores que se conectan a S3, PostgreSQL y GPT vía Lambda.
- La entrada del sistema es manejada por un `Handler` basado en WebFlux.

---

## 🎯 3. Diagrama de componentes

![Diagrama de componentes centralfile-service](centralfile-component-diagram.png)

---

## 🔍 4. Justificación técnica de los componentes

| Componente                   | Rol técnico                                                  |
|-----------------------------|--------------------------------------------------------------|
| `DocumentRouteHandler`      | Entry point reactivo vía WebFlux                             |
| `UseCaseConfig`             | Inyección de dependencias y construcción de casos de uso     |
| `UploadDocumentUseCase`     | Caso de uso principal de carga documental                    |
| `ArchiveDocumentUseCase`    | Caso de uso de transición a estado archivado                 |
| `Document`                  | Entidad del dominio que representa un documento              |
| `BDDocumentGateway`         | Puerto de persistencia de documentos                         |
| `StorageGateway`            | Puerto para almacenamiento externo (S3)                      |
| `ClassificationGateway`     | Puerto para envío de clasificación a GPT vía Lambda          |
| `BDDocumentRepository`      | Implementación JPA o Reactive Repository                     |
| `S3Storage`                 | Cliente adaptador hacia Amazon S3                            |
| `LambdaClassificationSQS`   | Adaptador que publica mensaje a SQS para que Lambda lo consuma|

---

## 📦 5. Caso práctico de uso

**Subida de documento**:

1. El usuario hace una petición POST a `/documents`.
2. `DocumentRouteHandler` pasa la petición a `UploadDocumentUseCase`.
3. El archivo se sube a S3 (`S3Storage`) y los metadatos se guardan en PostgreSQL (`BDDocumentRepository`).
4. Se publica un mensaje a SQS vía `LambdaClassificationSQS` para clasificación documental.
5. Posteriormente, el documento será archivado vía `ArchiveDocumentUseCase`.

---

## 🧩 6. Conclusión

Este diseño permite mantener el dominio limpio, testable y desacoplado. Las dependencias técnicas como S3 o GPT están completamente fuera del núcleo del negocio, cumpliendo con el principio de inversión de dependencias (D de SOLID).

---

## 💬 7. Pregunta para discusión

¿Cómo podríamos adaptar este mismo patrón si en lugar de eventos, quisiéramos realizar la clasificación IA de forma síncrona?

---

## 📚 8. Referencia (APA 7.ª edición)

Evans, E. (2004). *Domain-Driven Design: Tackling Complexity in the Heart of Software*. Addison-Wesley.

---